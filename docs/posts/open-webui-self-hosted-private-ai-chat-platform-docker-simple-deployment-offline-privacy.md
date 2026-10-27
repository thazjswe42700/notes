---
title: "Open WebUI 自建 AI 对话站：飞牛 NAS Docker 部署接云端模型不用显卡，内网穿透出门手机也能聊"
date: 2025-10-16
description: "Open WebUI 是装在 NAS 上的 AI 聊天界面，模型接云端 API，NAS 不用显卡也能跑。本文用 Docker Compose 在飞牛 NAS 上部署官方镜像，接 OpenAI 兼容接口，再用内网穿透开放到公网，出门手机上照样跟家里的 AI 聊，对话记录都在自己家。"
canonical: https://www.passnat.com/posts/open-webui-self-hosted-private-ai-chat-platform-docker-simple-deployment-offline-privacy
---

## 项目介绍

Open WebUI 是一个开源的自托管 AI 对话界面，长得跟网页版的 ChatGPT 差不多：左边是对话历史，中间输入框，支持多用户、多模型切换、上传文件问答。它本身不跑模型，模型可以是本机的 Ollama，也可以是任何 OpenAI 兼容的云端接口。

装在飞牛 NAS 上、接云端模型，NAS 不需要显卡，一个容器就够。账号、对话记录全在自己家的 NAS 里，不放在第三方平台上。

项目地址：[点我跳转](https://github.com/open-webui/open-webui)

## 部署安装

装在内网之后，出了门就连不上了。想在外面手机上也能用，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 open-webui；路径选一个空文件夹。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3010:8080"
    volumes:
      - ./data:/app/backend/data
    environment:
      OPENAI_API_BASE_URL: https://api.minimaxi.com/v1
      OPENAI_API_KEY: 换成你自己的模型密钥
      DEFAULT_MODELS: MiniMax-Text-01
      ENABLE_OLLAMA_API: "false"
      OFFLINE_MODE: "true"
      WEBUI_SECRET_KEY: 换成一长串随机字符
    restart: always
```

从上往下要认几处：image 用的是官方镜像 ghcr.io/open-webui/open-webui，标签就写官方文档 quick-start 里的那个 main，跟着上游走、不用自己钉版本号（钉死了以后项目更新，你这份编排还停在老版本）；ports 左边的 3010 是你浏览器要敲的端口，右边的 8080 是容器内部端口，别改；volumes 把数据库和对话记录挂到项目目录下的 data 文件夹。

environment 里：OPENAI\_API\_BASE\_URL 填模型接口的地址，本文拿 MiniMax 做演示，所以写的是它的 OpenAI 兼容端点——这一行不是只能填 MiniMax，任何兼容 OpenAI 协议的接口都能用；OPENAI\_API\_KEY 换成你自己的模型密钥，这个服务后面要开放到公网，密钥只放在编排里、不要发给别人；DEFAULT\_MODELS 是新对话默认选的模型，填你那家的模型名，挑一个不带推理的普通模型，回得快；ENABLE\_OLLAMA\_API 这里关掉了，因为这份编排没装 Ollama，不关的话界面上会一直提示连不上；OFFLINE\_MODE 打开是让它启动时别去 huggingface 下载文档问答用的嵌入模型——国内 NAS 多半连不上那个站，不加这行容器会卡在启动、端口一直打不开（我第一次就是这么卡了二十多分钟），加了以后对话功能不受影响；WEBUI\_SECRET\_KEY 随便打一长串随机字符，是登录会话的签名密钥，不设的话每次重建容器所有人都会被登出。

想换成别家的模型，改三行就行：OPENAI\_API\_BASE\_URL 换成那家的 OpenAI 兼容接口地址、OPENAI\_API\_KEY 换成那家的密钥、DEFAULT\_MODELS 换成那家的模型名，然后把项目重新构建一下。也可以不动编排，进去之后在「管理员面板 → 设置 → 外部连接」里再加一条 OpenAI 连接，效果一样。

家里已经跑着 Ollama 的话就更省事：把 ENABLE\_OLLAMA\_API 改成 true，再加一行 OLLAMA\_BASE\_URL 指到 Ollama 的地址（官方文档里的写法就是 OLLAMA\_BASE\_URL=http://你的地址:11434），Llama、Qwen 这些本地模型都能直接用，云端那几行可以不填。本文为了省事、也为了让 NAS 不用配显卡，走的是云端 API。

勾上「创建项目后立即启动」，点确认。镜像压缩后一个多 G，拉起来要等一会儿，列表里看到「运行中」就好了。

### 二、用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面的操作都在公网地址上做。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 openwebui），接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 3010。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。

### 三、在公网地址上建管理员、开聊

浏览器打开刚才那个公网地址，第一次进去是注册页，第一个注册的账号就是管理员——隧道一开就先把管理员建好，别空着。之后别人再注册会进「待审核」，管理员不批就进不来。

进来就是聊天页，默认模型已经选好。有一步要在管理员设置里做一次：右上角头像 → 管理员面板 → 设置 → 外部连接，点 OpenAI 那条连接的编辑，在「模型 ID」里填上你要用的模型名（例子里是 MiniMax-Text-01）。这样模型列表里只留这一个，也不会把接口返回的一堆推理模型都列出来。

然后随便问一句，比如「帮我列个周末两天的家庭计划」，答案一行一行流出来，跟网页版一个样。人在外面手机上打开公网地址，聊天记录都同步，因为记录本来就存在家里的 NAS 上。

## 几个提醒

一、演示用的模型挑不带推理的普通模型。推理模型会先输出一大段思考过程，回一句要慢好几倍。

二、公网上开着的是一个能调用你模型密钥的服务，管理员口令用长的随机口令，注册审核别关。不用的时候把容器停掉，隧道留着，下次起回来地址不变。

三、如果以后重建隧道、公网端口变了，浏览器里换成新地址就行，Open WebUI 本身不用改配置。

---

原文：<https://www.passnat.com/posts/open-webui-self-hosted-private-ai-chat-platform-docker-simple-deployment-offline-privacy>


## 相关文章

- [本地AI交互平台SillyTavern Docker部署指南，NAS快速安装配置教程](silly-tavern-docker-deployment-guide-nas-quick-install-setup.md)
- [One API 统一大模型接口，支持30+模型OpenAI格式调用，NAS一键部署使用。](one-api-unified-model-interface-30-plus-models-openai-format-nas-deployment.md)
- [免费DeepSeek API接口一键部署，OpenAI兼容格式，NAS本地化私有部署方案](free-deepseek-api-deployment-openai-compatible-nas-local-private.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [深度网络研究工具本地部署指南，NAS一键启动Deep Research Web UI，支持多AI提供商](deep-network-research-tool-local-deployment-guide-nas-one-click-launch-deep-research-web-ui-multi-ai-providers.md)
