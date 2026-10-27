---
title: "自建 AI 助手：飞牛 NAS Docker 部署 AstrBot，接上大模型随手问，内网穿透在外面也能用"
date: 2026-09-07
description: "在飞牛 NAS 上用 Docker 部署 AstrBot，接上大模型当自己的 AI 助手，并通过内网穿透在外网访问。"
canonical: https://www.passnat.com/posts/astrbot-self-hosted-ai-assistant-nas-docker-deployment-intranet-penetration
---

## 项目介绍

AstrBot 是一个开源的 AI 助手框架，装好之后你得到的是一个网页管理面板：在里面填上大模型的接口和密钥，就能直接在自带的聊天页跟它对话，还能装插件、设定人格、留存对话记录。

它跟直接打开某个大模型的网页版不一样的地方在于，这一整套是跑在你自己的机器上的 —— 你配了哪些模型、装了哪些插件、聊过什么，都存在你自己的硬盘上，不在别人的账号体系里。想换模型就换一个提供商，其它设置都还在。

它另外一个常见用法是接到 QQ、飞书、企业微信、Telegram 这些 IM 平台上，当群里的机器人。这篇只讲最基础的那一半：把面板跑起来、接上模型、在网页里直接聊，不接任何 IM 平台。

作者在 GitHub 上开源（AGPL-3.0），并且提供了打好的官方镜像，NAS 上拉下来就能跑，不用自己构建。

项目地址：[点我跳转](https://github.com/AstrBotDevs/AstrBot)

## 部署安装

装在自己的 NAS 上之后，只能在家里的网络访问。想在外面也能打开，家里又没有公网 IP，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 飞牛的应用中心里就能直接装到插件，不用另外找工具。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

下面这份编排可以直接用，里面没有任何密钥 —— AstrBot 的模型密钥不走环境变量，是装好之后在网页面板里填的。

```yaml
services:
  astrbot:
    image: soulter/astrbot:latest
    restart: always
    ports:
      - "6185:6185"
    environment:
      TZ: Asia/Shanghai
    volumes:
      - ./data:/AstrBot/data
```

端口：冒号左边的 6185 是待会儿在浏览器里输的宿主端口，右边的 6185 是容器内部的端口，不要动。6185 如果和你机器上别的服务撞了，换一个没被占用的即可。

时区：TZ 这一行不填的话，面板里显示的时间是 UTC，跟本地差八小时，看对话记录和日志的时候会很别扭。

数据目录：./data 是相对项目目录的，也就是你在飞牛里选的那个文件夹底下会自己长出来一个 data。配置、对话记录、装的插件全在里面，容器删了重建数据还在。

镜像标签这里写的是 latest，跟上游官方的写法一致 —— 作者仓库根目录的 compose.yml 和官方文档里的 docker run 命令，用的都是 soulter/astrbot:latest。照抄的时候直接用它就行，拉到的是当下的正式版本。这个项目另外还有一大批每天构建的 nightly 标签，那些是开发版，日常用不到。

官方给的编排里还映射了一个 6199 端口，那是给 OneBot v11 / Napcat 这条 QQ 链路用的。这篇不接 IM 平台，所以上面这份把它去掉了。你以后要接 QQ 再加回来就行。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」，点右上角的「新增项目」。项目名称填 astrbot。

路径这一栏点开，在「我的文件」下面新建一个空文件夹并选中它。这个文件夹就是这个项目以后放编排文件的地方。

来源选「创建docker-compose.yml」，把上面那段编排整段贴进去，勾上「创建项目后立即启动」，点确认。这个镜像有七百多兆，拉的时间比一般的应用长一些，拉完项目就会自己跑起来。

### 第三步：第一次登录先把密码改掉

AstrBot 的面板是有登录的，用户名是 astrbot，但初始密码不是固定的 —— 它在容器第一次启动的时候随机生成一串，只打在启动日志里。

去飞牛的 Docker → 容器，找到这个容器，点行尾的「…」选「运行日志」，往上翻能看到 Initial username 和 Initial password 这两行，密码就在那儿。用它登进面板之后，系统会直接要求你设置一个新密码，设一个自己的强密码。

这一步要放在开放公网之前做完。密码改好了，再往下走。

### 第四步：用 PassNAT 插件开放到公网

在飞牛的应用中心里搜 PassNAT，装上，用账号登一次。打开插件切到「隧道」，点「创建隧道」。

隧道名称随便起（注意不能超过 12 个字符），接入节点和传输协议保持默认。本地 IP 这一栏不用动，留空默认就是 127.0.0.1 —— 插件本来就跑在 NAS 上，回环地址天然指向本机。本地端口填 6185，也就是上面编排里冒号左边那个。

创建完回到隧道列表，点开这条隧道的详情，按标题行最左边那个电源图标启动它，状态转成「运行中」。详情里「公网连接」那一列的地址，就是你在外面要访问的地址。

### 第五步：接上大模型

用公网地址打开面板，左边点「模型提供商」，右上角点「新增」。弹出来的提供商源列表里有一长串现成的，OpenAI 兼容、Gemini、Anthropic、DeepSeek、Ollama 这些都在里面。挑你自己在用的那一个，点它之后 ID 和 API Base URL 会自动填好。视频里我用的是 MiniMax，那只是拿来演示 —— 这一栏换成任何一个兼容 OpenAI 协议的服务都一样填法，不想把内容发到外面就选 Ollama，模型直接跑在自己机器上。

接着把你自己的 API Key 填进去。这一栏是密码框，打进去显示成圆点，看不见明文。填完点「保存并获取模型」—— 能拉回一串模型名，就说明密钥填对了，这一步顺便就当成密钥的校验。

如果你要用的那个模型不在拉回来的列表里（有些服务商的模型列表接口并不会把全部模型都列出来），点旁边的「自定义模型」，把模型名手填进去，再保存配置就行。

然后回到右上角的 Chat，新建一个对话，随手问一句，回答就出来了。问题是从公网地址发进去的，模型在服务商那边跑，NAS 这台机器负责的是转发和把记录存下来。

## 一些说明

挑模型的时候留意一下是不是「推理型」的。有些推理模型会把自己的思考过程直接写进回复正文里，聊天界面上就会看到一大段推演之后才是正经答案。只想要一个干脆的回答的话，选非推理的那一档更合适。

面板左边还有插件、知识库、人格设定这些，都是在同一套数据目录里的。想让它说话的风格变一变，去「人格设定」里改；想加功能，去插件那里装。这些都不影响上面这套部署流程。

最后一句关于安全的：这个面板一旦开放到公网，任何知道地址的人打开都会看到登录页。所以第三步那个密码一定要设得像样一点，而且模型密钥是存在你这台机器上的，别人登进来就能用你的额度。

---

原文：<https://www.passnat.com/posts/astrbot-self-hosted-ai-assistant-nas-docker-deployment-intranet-penetration>


## 相关文章

- [自建推送通知 Gotify：飞牛 NAS Docker 部署，家里的脚本有事就发一条，配内网穿透在外面也能收](gotify-self-hosted-push-notification-deployment-guide.md)
- [自建在线表格 NocoDB：飞牛 NAS Docker 部署，Airtable 的开源替代，配内网穿透在外面也能打开自己的表](nocodb-one-click-deploy-airtable-open-source-alternative-nas-local-database-management.md)
- [自建 Smart Mermaid：飞牛 NAS Docker 部署 + 内网穿透外网访问](smart-mermaid-ai-text-to-diagram-nas-docker-deployment-intranet-penetration.md)
- [Open WebUI 自建 AI 对话站：飞牛 NAS Docker 部署接云端模型不用显卡，内网穿透出门手机也能聊](open-webui-self-hosted-private-ai-chat-platform-docker-simple-deployment-offline-privacy.md)
