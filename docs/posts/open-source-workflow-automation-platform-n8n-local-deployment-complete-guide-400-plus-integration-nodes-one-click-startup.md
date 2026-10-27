---
title: "自建工作流自动化平台 n8n：飞牛 NAS 用 Docker Compose 部署，配内网穿透出门也能改能跑"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker Compose 部署 n8n 工作流自动化平台，先配内网穿透拿到公网地址再写编排，出门也能改流程、跑流程。"
canonical: https://www.passnat.com/posts/open-source-workflow-automation-platform-n8n-local-deployment-complete-guide-400-plus-integration-nodes-one-click-startup
---

## 项目介绍

n8n 是一个工作流自动化平台：把要重复干的事串成一条流程 —— 定时去查、拿到结果、按条件分个岔、最后把通知拼出来，全在一张画布上拖出来，不用自己写调度和重试。

它的节点是真的多。这篇用的 2.38.1 版本，装完在自己的实例上数了一下，自带 987 个节点类型：既有定时、判断、改字段、写代码这类核心节点，也有各家服务的节点，其中 118 个是触发器。（想自己数的话，登录后访问 /types/nodes.json 就能看到这一版带了哪些。）

需要写代码的地方也留了口子：Code 节点里可以直接写 JavaScript 或者 Python，拖不出来的逻辑就自己写几行。

源码在 GitHub 上公开，自己装在 NAS 上用没问题。作者提供官方镜像，拉下来就能跑，不用自己构建。

项目地址：[点我跳转](https://github.com/n8n-io/n8n)

## 部署安装

装在自己的 NAS 上之后，只能在家里的网络访问。想在外面也能打开、也能改流程，家里又没有公网 IP，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 飞牛的应用中心里就能直接装到插件，不用另外找工具。

⚠ 这一期的顺序跟别的教程反过来：要先把内网穿透配好、拿到公网地址，再写编排文件。因为 n8n 需要知道自己对外是什么地址，Webhook 的回调地址是按它拼出来的，地址没定就填不了编排。

### 第一步：先建隧道，拿到公网地址

在飞牛的应用中心里搜 PassNAT，装上，用账号登一次。打开插件切到「隧道」，点「创建隧道」。

隧道名称随便起（注意不能超过 12 个字符），接入节点和传输协议保持默认。本地 IP 这一栏不用动，留空默认就是 127.0.0.1 —— 插件本来就跑在 NAS 上，回环地址天然指向本机。本地端口填 5678。

创建完回到隧道列表，点开这条隧道的详情，按标题行最左边那个电源图标启动它。详情里「公网连接」那一列的地址就是你要的，形如 1.2.3.4:12345 —— 记下来，下一步编排里要填。

### 第二步：准备编排文件

下面这份编排可以直接用，需要你改的只有那三行公网地址。

```yaml
services:
  n8n:
    image: n8nio/n8n
    container_name: n8n
    ports:
      # 左边是宿主端口，浏览器敲它；右边是容器内部的，别动
      - 5678:5678
    volumes:
      # 工作流、凭据、执行记录都存这儿，删容器不丢
      - ./data:/home/node/.n8n
    environment:
      # 时区要填对，定时触发器按它算几点跑
      GENERIC_TIMEZONE: Asia/Shanghai
      TZ: Asia/Shanghai
      # 下面三行填你自己的公网地址，Webhook 地址是按它拼出来的
      N8N_HOST: 你的公网地址不带端口
      N8N_PROTOCOL: http
      WEBHOOK_URL: http://你的公网地址:端口/
      # 走 http 的话这行要设成 false，不然登录状态存不住
      N8N_SECURE_COOKIE: "false"
      N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS: "true"
    restart: unless-stopped
```

镜像这一行跟着上游文档写，连标签都不带 —— 官方 docker run 示例原文就是这样。钉死一个版本号，以后项目更新，你这份编排还停在老版本上。

端口 5678 是网页编辑器，浏览器里敲的就是它。冒号左边是宿主端口，跟你机器上别的服务撞了就换一个；右边是容器内部的端口，不要动。

挂载这一行把工作流、凭据和执行记录存到编排文件旁边的 data 目录里，删掉容器重建，之前搭的流程还在。这里用的是绑定挂载（左边写成 ./data 这种相对路径），不用具名卷 —— 数据看得见摸得着，备份的时候直接把这个目录拷走就行。

时区两行都要填。定时触发器是按 GENERIC\_TIMEZONE 算几点跑的，这一行不对，你设的「每天早上九点」就会在别的时间触发。

N8N\_HOST 和 WEBHOOK\_URL 这两行填上一步拿到的公网地址。N8N\_HOST 只写地址不带端口，WEBHOOK\_URL 要写完整的 http://地址:端口/ 形式。n8n 生成 Webhook 链接的时候用的就是它，不填的话生成出来的是 localhost，外面根本调不到。

N8N\_SECURE\_COOKIE 走 http 的隧道必须设成 false。这一行不设，登录状态存不住 —— 表现是登录之后一刷新又回到登录页。

### 第三步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」，点右上角的「新增项目」。项目名称填 n8n。

路径这一栏点开右边的图标，在「我的文件」下面挑一个空文件夹并选中它。

来源选「创建docker-compose.yml」，把上面那段编排整段贴进去，勾上「创建项目后立即启动」，点确认。镜像不到 300 MB，拉完项目就会自己跑起来。

### 第四步：在公网地址上把管理员建好

用上面那个公网地址打开，第一次进来是建管理员的页面：填邮箱、姓名、密码，点下一步。

这一步要快。n8n 首次启动是没有主人的状态，谁先打开这个地址谁就成了管理员 —— 既然地址已经开到公网了，就别让它空着挂在外面，拿到地址就顺手把账号建掉。

### 第五步：搭一条流程试试

进来先建一条工作流。画布左边那一排是触发器：定时的、收到 HTTP 请求的、有人调接口的都有。挑定时的那个，设成每天早上九点。

后面接一个 HTTP Request 节点去查你关心的接口，再接一个 IF 判断结果符不符合条件，最后用 Edit Fields 把要通知的内容拼出来。想真发出去的话，后面接邮件、微信、Telegram 都是现成的节点，配一次凭据就行。

搭完点一下「Execute workflow」，节点会挨个变绿，每条连线上还标着这一步过了几条数据，哪一步出问题一眼就看得出来。确认没问题之后点右上角发布，它就按你设的时间自己跑了。

## 一些说明

工作流删不掉的时候看一眼是不是还没归档 —— 这一版要先归档才能删除，直接删会提示你先归档。

执行记录在编辑器上方的 Executions 标签里，每次跑的输入输出都留着，排查「昨晚那次为什么没发出来」的时候直接翻这里。

界面是英文的，但节点名称是你自己起的，可以直接写中文 —— 画布上一眼就能看懂这条流程在干嘛。

---

原文：<https://www.passnat.com/posts/open-source-workflow-automation-platform-n8n-local-deployment-complete-guide-400-plus-integration-nodes-one-click-startup>


## 相关文章

- [DopplerTask开源自动化平台Docker快速部署指南，NAS轻松搭建工作流自动化系统](doppler-task-docker-nas-workflow-automation-guide.md)
- [开源自动化工作流平台 Activepieces 内网穿透部署完整指南及应用教程](activepieces---.md)
- [自建 Dockge 管理面板：飞牛 NAS Docker 部署，容器开关、日志、编排一页全管，内网穿透出门也能看](dockge-docker-compose-manager-nas-deployment-intranet-penetration.md)
- [企业级智能体平台MaxKB一键部署，支持RAG工作流编排，零代码赋能企业系统智能问答](maxkb-enterprise-ai-agent-platform-rag-workflow-zero-code.md)
