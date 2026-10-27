---
title: "Tinode：NAS 上的自建聊天服务，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "把家庭或小团队的聊天室放在自己的 NAS 上：用 Docker Compose 部署开源即时通讯服务 Tinode，群聊、成员和历史消息都存在自己的数据库里，再用帕斯内网穿透让你在外面用手机也能收发消息。"
canonical: https://www.passnat.com/posts/tinode-nas-docker-self-hosted-chat-server-intranet-penetration
---

## 项目介绍

Tinode 是一套开源的即时通讯服务：一个服务端程序，自带网页客户端。装在自己的 NAS 上，家人或几个朋友用浏览器打开就能聊天，消息、成员和文件都保存在自己的数据库里。

它支持一对一聊天和群聊，群组可以设成员和权限；消息有已读状态，历史记录存在服务端，换一台设备登录同一个账号，之前的消息还在。网页之外也有桌面和手机客户端，填上自己的服务地址就能连。

部署只需要两个容器：一个数据库，一个 Tinode 服务端。服务端同时提供网页界面和接口，不用再单独装一个前端。

项目地址：[点我跳转](https://github.com/tinode/chat)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：用 Docker Compose 把 Tinode 装上，再用 [帕斯内网穿透](https://passnat.com) 开放到公网，出门用手机也能收发消息。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 tinode，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: postgres:16-alpine
    restart: unless-stopped
    environment:
      - TZ=Asia/Shanghai
      - POSTGRES_PASSWORD=换成你自己的数据库口令
    volumes:
      - tinode_pg:/var/lib/postgresql/data
  tinode:
    image: tinode/tinode-postgres:latest
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "3514:6060"
    environment:
      - TZ=Asia/Shanghai
      - WAIT_FOR=db:5432
      - POSTGRES_DSN=postgres://postgres:换成你自己的数据库口令@db:5432/tinode?sslmode=disable
      - AUTH_TOKEN_KEY=换成你自己的32字节随机串
      - UID_ENCRYPTION_KEY=换成你自己的16字节随机串
      - MEDIA_HANDLER=fs
    volumes:
      - tinode_data:/var/tinode/data
volumes:
  tinode_pg:
  tinode_data:
```

两个密钥都要换成你自己的：AUTH\_TOKEN\_KEY 用 32 字节的随机串，UID\_ENCRYPTION\_KEY 必须是 16 字节，两者都要先做 base64 编码。在电脑上敲 openssl rand -base64 32 和 openssl rand -base64 16，把输出各贴一处，别照抄文章里的。

数据库口令要和 POSTGRES\_PASSWORD 那一行一致，POSTGRES\_DSN 里的口令也要一起改。数据放两个具名卷：tinode\_pg 存数据库，tinode\_data 存上传的文件。勾上「创建项目后立即启动」，点确认。

### 二、第一次打开：建账号和群

容器起来之后，用 NAS 的局域网地址加 3514 端口打开，就是登录页。第一次点左上角的「创建账户」，填一个登录名、密码、全名和邮箱，点注册就直接进去了。这里不配邮件服务也能用，邮箱只作为账号信息保存。

要点左边顶部的对话图标，点「新群组」，填一个名称，比如「家里日常」，说明可以留空，点创建。群建好后打开右上角菜单里的「信息」，在成员区点「添加成员」。

加人用用户 ID 更直接：让对方打开「开始新会话 → 通过 id」，就能看到自己的 ID；你在同样的入口里填上对方的 ID，点一下按钮，一对一聊天就先通了。

在输入框里打字，回车发送。对方打开同一个地址、登录自己的账号，就能看到消息和未读数。

### 三、开放到公网

打开飞牛的「应用中心」，搜索 PassNAT，安装后打开插件，点「创建隧道」：名字随便起，不超过 12 个字符；接入节点和传输协议留默认；本地 IP 保持 127.0.0.1；本地端口填 3514，和上面的编排对应。

建好点开这条隧道，按一下电源，状态变成「运行中」，详情里那一串就是公网地址。在外面用手机打开这个地址，登录同一个账号，消息会从自己家的 NAS 同步过来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**数据和备份。** 数据库和上传的文件都在 tinode\_pg、tinode\_data 两个卷里。升级之前把这两个卷备份一份，再重新拉取 latest 镜像、重启项目，数据不会丢。

**账号。** 第一个账号可以在局域网里先建好，再开放到公网。给家人各建一个账号，群聊的成员和权限在群信息里管理。

**客户端。** 网页版是服务端自带的，不需要另外部署。桌面和手机客户端在设置里把服务地址填成你的地址，账号密码和网页版通用。

**安全。** 公网地址只发给家人朋友，别贴到公开的地方。以后换成 HTTPS 访问，网页和客户端都更稳妥。

---

原文：<https://www.passnat.com/posts/tinode-nas-docker-self-hosted-chat-server-intranet-penetration>


## 相关文章

- [自建在线表格 NocoDB：飞牛 NAS Docker 部署，Airtable 的开源替代，配内网穿透在外面也能打开自己的表](nocodb-one-click-deploy-airtable-open-source-alternative-nas-local-database-management.md)
- [code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码](codeserver.md)
- [NodeBB现代论坛社区系统Docker部署指南，NAS一键安装配置实时互动讨论社区平台](nodebb-docker-nas-deployment-guide.md)
- [Mattermost 团队聊天：NAS Docker 部署 + 内网穿透](mattermost-team-chat-nas-docker-intranet-penetration.md)
- [Node-RED 拖一张「家里服务状态页」：飞牛 NAS Docker 部署 + 内网穿透](node-red-nas-docker-home-service-status-page-intranet-penetration.md)
