---
title: "Penpot 开源设计工具自建教程：飞牛 NAS Docker Compose 部署官方镜像，Figma 的开源替代，内网穿透出门手机也能改稿"
date: 2025-10-16
description: "Penpot 是开源的在线设计 / 原型工具，Figma 的开源替代，设计稿全在自己的 NAS 上。本文用 Docker Compose 在飞牛 NAS 上部署官方五个容器（frontend / backend / exporter / postgres / valkey），先用内网穿透拿到公网地址填进编排，人在外面手机上一样能打开改稿。"
canonical: https://www.passnat.com/posts/penpot-self-hosted-deployment-guide-nas-one-click-setup
---

## 项目介绍

Penpot 是一个开源的在线设计工具，做界面、画原型、管设计系统、团队协作都行，可以理解成 Figma 的开源替代。装在自己的 NAS 上，设计稿全在自己家，不用把文件放到别人的云上；浏览器打开就能用，不用装客户端。

本文部署的是官方的 Docker Compose 方案：frontend、backend、exporter 三个 Penpot 自己的镜像，加 PostgreSQL 和 Valkey，一共五个容器一起起。

项目地址：[点我跳转](https://github.com/penpot/penpot)

## 部署安装

Penpot 的编排里有一项 PENPOT\_PUBLIC\_URI，要填「用户访问它的地址」——所以这一期的顺序反过来：先用 [帕斯内网穿透](https://passnat.com) 把端口开放到公网、拿到公网地址，再把地址填进编排里起容器。不需要公网 IP，也不用改路由器。

### 一、先建隧道，拿到公网地址

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

在插件里点「创建隧道」：隧道名称随便起一个（比如 penpot），接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 9001（下面编排里给 Penpot 开的端口）。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址，形如 http://IP:端口。把它记下来，下一步要填。

### 二、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 penpot；路径选一个空文件夹。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去（三处 PENPOT\_PUBLIC\_URI 换成你自己隧道给的公网地址；PENPOT\_SECRET\_KEY 两处换成同一串随机字符）：

```yaml
services:
  penpot-frontend:
    image: penpotapp/frontend:2.17
    ports:
      - "9001:8080"
    environment:
      PENPOT_PUBLIC_URI: http://你的公网地址:端口
      PENPOT_FLAGS: disable-email-verification disable-secure-session-cookies disable-onboarding
    volumes:
      - penpot-assets:/opt/data/assets
    depends_on:
      - penpot-backend
      - penpot-exporter
    restart: always

  penpot-backend:
    image: penpotapp/backend:2.17
    environment:
      PENPOT_PUBLIC_URI: http://你的公网地址:端口
      PENPOT_FLAGS: disable-email-verification disable-secure-session-cookies disable-onboarding
      PENPOT_SECRET_KEY: 换成一长串随机字符
      PENPOT_DATABASE_URI: postgresql://penpot-postgres/penpot
      PENPOT_DATABASE_USERNAME: penpot
      PENPOT_DATABASE_PASSWORD: penpot
      PENPOT_REDIS_URI: redis://penpot-valkey/0
      PENPOT_OBJECTS_STORAGE_BACKEND: fs
      PENPOT_OBJECTS_STORAGE_FS_DIRECTORY: /opt/data/assets
      PENPOT_TELEMETRY_ENABLED: "false"
    volumes:
      - penpot-assets:/opt/data/assets
    depends_on:
      penpot-postgres:
        condition: service_healthy
      penpot-valkey:
        condition: service_healthy
    restart: always

  penpot-exporter:
    image: penpotapp/exporter:2.17
    environment:
      PENPOT_PUBLIC_URI: http://你的公网地址:端口
      PENPOT_INTERNAL_URI: http://penpot-frontend:8080
      PENPOT_SECRET_KEY: 换成一长串随机字符
      PENPOT_REDIS_URI: redis://penpot-valkey/0
    restart: always

  penpot-postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: penpot
      POSTGRES_USER: penpot
      POSTGRES_PASSWORD: penpot
    volumes:
      - penpot-postgres:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U penpot"]
      interval: 5s
      retries: 10
    restart: always

  penpot-valkey:
    image: valkey/valkey:8.1
    healthcheck:
      test: ["CMD-SHELL", "valkey-cli ping | grep PONG"]
      interval: 5s
      retries: 10
    restart: always

volumes:
  penpot-assets:
  penpot-postgres:
```

从上往下要认几处：ports 里左边的 9001 是浏览器要敲的端口，也是上面隧道里填的那个；PENPOT\_PUBLIC\_URI 填隧道给的公网地址，frontend、backend、exporter 三个服务都要填一样的，分享链接、导出图片都按它生成；PENPOT\_FLAGS 里三个开关：disable-email-verification 是注册不用邮箱验证（家里没有邮件服务器），disable-secure-session-cookies 是隧道走的是 http 而不是 https、不关掉的话登录态存不下来，disable-onboarding 是跳过注册后的新手问卷；PENPOT\_SECRET\_KEY 是它派生各种密钥的种子，自己随便生成一长串，不设的话每次重启都会把所有人踢下线；两个命名卷 penpot-assets 和 penpot-postgres 分别放上传的素材和数据库，备份就备份它们。

勾上「创建项目后立即启动」，点确认。五个镜像加起来一个多 G，拉起来要等一会儿，列表里看到「运行中」就好了。backend 是 Java 写的，起来之后还要初始化数据库，再等半分钟。

### 三、在公网地址上注册、画第一张图

浏览器打开刚才那个公网地址，落在登录页，点「创建账号」，填全名、邮箱、密码，再点「创建账号」——隧道一开就先把账号建好，别让它空着挂在公网上。没有邮箱验证，注册完直接进面板。

进来是面板，左边是「项目」和「草稿」，点中间的「创建新文件」就进画板了。按 R 拖一个矩形，按 T 打几个字，右边的属性面板改颜色、改尺寸；文件自动保存。

做好之后点右上角的「共享」，生成一个链接，链接就是公网地址开头的，手机上打开一样能看；人在外面登录进去也能接着改。

## 几个提醒

一、注册默认是开着的，公网上谁都能建账号。自己用的话，录完 / 建完账号可以在 PENPOT\_FLAGS 里加一个 disable-registration，停掉项目改一下编排再构建就行。

二、隧道是 http，浏览器的剪贴板 API 在非 https 页面上会受限，画板里复制粘贴偶尔不灵是浏览器的限制，不是 Penpot 的问题。

三、如果以后重建隧道、公网端口变了，记得把编排里三处 PENPOT\_PUBLIC\_URI 一起改掉再重启，不然分享链接还是旧地址。

---

原文：<https://www.passnat.com/posts/penpot-self-hosted-deployment-guide-nas-one-click-setup>


## 相关文章

- [自建在线表格 NocoDB：飞牛 NAS Docker 部署，Airtable 的开源替代，配内网穿透在外面也能打开自己的表](nocodb-one-click-deploy-airtable-open-source-alternative-nas-local-database-management.md)
- [Open WebUI 自建 AI 对话站：飞牛 NAS Docker 部署接云端模型不用显卡，内网穿透出门手机也能聊](open-webui-self-hosted-private-ai-chat-platform-docker-simple-deployment-offline-privacy.md)
- [Home Assistant 自建智能家居中枢：飞牛 NAS Docker 部署官方镜像，不同牌子的设备一页全管，内网穿透出门手机也能控](home-assistant-nas-smart-home-hub-docker-deployment-intranet-penetration.md)
- [Mattermost 团队聊天：NAS Docker 部署 + 内网穿透](mattermost-team-chat-nas-docker-intranet-penetration.md)
- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
