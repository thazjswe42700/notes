---
title: "自建 Smart Mermaid：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-04
description: "打一段话，AI 直接生成 Mermaid 流程图。飞牛 NAS 上用 Docker Compose 部署 Smart Mermaid，配好 AI 接口，再用内网穿透开放到公网，在外面也能用。"
canonical: https://www.passnat.com/posts/smart-mermaid-ai-text-to-diagram-nas-docker-deployment-intranet-penetration
---

## 项目介绍

Smart Mermaid 是一个开源的网页小工具，把一段自然语言交给大模型，直接生成 Mermaid 图表代码，并在右边实时渲染成图。

流程图、时序图、甘特图这些都能画。你不用记 Mermaid 的语法，左边写一句「用户下单后先扣库存，扣不上就取消」，右边就出来一张规规矩矩的流程图。生成完的代码还能自己改，图也能直接导出。

项目地址：[点我跳转](https://github.com/liujuntao123/smart-mermaid)

## 部署安装

Smart Mermaid 装在内网之后，出了门就访问不到了 —— 我家是移动宽带，没有公网 IP。想在外面也能用，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 第一步：准备编排文件

作者本人没有发布 Docker 镜像，仓库里只提供源码和 Dockerfile，官方编排是从源码现场构建的。飞牛的 Compose 面板没法拉源码构建，所以这里用社区打包好的预构建镜像。

下面这份编排可以直接用。三处需要你自己改：端口、AI 接口地址和模型名、以及你自己的密钥。

```yaml
name: smart-mermaid

services:
  smart-mermaid:
    image: ghcr.io/luler/smart-mermaid-docker:latest
    container_name: smart-mermaid
    ports:
      # 左边 3080 是 NAS 上的端口，浏览器敲它；右边 3000 是容器里的，别改
      - '3080:3000'
    environment:
      NODE_ENV: production
      # 换成你自己的 AI 接口就行，OpenAI 格式的都支持
      AI_API_URL: https://api.minimaxi.com/v1
      AI_MODEL_NAME: MiniMax-Text-01
      AI_API_KEY: sk-填你自己的
    restart: unless-stopped
```

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」标签，点「新增项目」。

项目名称填 smart-mermaid。路径这里挑一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把上面那段编排贴进编辑框。

端口那一行是 '3080:3000'：左边的 3080 是 NAS 上对外的端口，一会儿浏览器里敲的就是它；右边的 3000 是容器内部的端口，不要改。如果 3080 在你机器上已经被占用了，改左边那个数字即可。

最后勾上「创建项目后立即启动」，点确认。镜像将近一个 G，第一次拉取按网速等几分钟。

### 第三步：填 AI 接口

Smart Mermaid 自己不带模型，「把中文翻译成 Mermaid 语法」这一步是调外部大模型完成的，所以必须配一个接口才能用。没配的话点「生成图表」会直接返回「AI配置不完整」。

编排里的三个变量就是干这个的：AI\_API\_URL 填接口地址（OpenAI 格式的都支持），AI\_MODEL\_NAME 填模型名，AI\_API\_KEY 填你自己的密钥。

模型建议挑不带思维链的普通对话模型。带推理的模型会先输出一大段思考过程，出图要等十几秒，而且思考文字会跟着刷在界面上。普通模型两三秒就出图了。

### 第四步：建隧道开放到公网

飞牛的应用中心里直接搜 PassNAT 就能装到客户端，装好之后账号登录一次即可。

打开插件，进「隧道」，点创建隧道。名字自己起一个，接入节点和传输协议都留默认。

「本地IP」这一栏不用填 —— 留空默认就是 127.0.0.1，插件本身跑在 NAS 上，回环地址指向的就是这台机器。本地端口填 3080，跟编排里左边那个数字保持一致。

建好之后在隧道列表里点开详情，按一下电源图标连接。端口列表里就能看到公网连接的地址，形如「节点IP:端口」。在外面用这个地址打开，跟在家里访问是一样的。

## 常见问题

点了生成图表没反应，或者提示 AI 配置不完整：检查编排里的 AI\_API\_KEY 有没有填成你自己的，以及那个密钥有没有开通对话权限。

页面能打开但一直转圈：多半是模型太慢。换一个不带思维链的模型试试。

换了端口之后打不开：编排里 ports 左边的数字、隧道里的「本地端口」这两处要一致，改一个就得改另一个。

---

原文：<https://www.passnat.com/posts/smart-mermaid-ai-text-to-diagram-nas-docker-deployment-intranet-penetration>
