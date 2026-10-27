---
title: "Docmost 共享文档库：NAS Docker 部署 + 内网穿透"
date: 2026-10-18
description: "家里的事别再散在聊天记录里：在 NAS 上用 Docker Compose 部署开源文档工具 Docmost，用帕斯内网穿透拿到公网地址，家里人手机打开就能看，待办清单和表格一起改。含完整编排和公开链接设置。"
canonical: https://www.passnat.com/posts/docmost-nas-docker-family-shared-wiki-intranet-penetration
---

## 项目介绍

Docmost 是一个开源的在线文档和知识库工具。它把文档按「空间」分好：一个空间相当于一个文件夹，里面可以一层层建页面，页面里能写标题、列表、待办清单和表格。

适合把家里的事集中放：装修清单、常用电话、孩子学校的通知、菜谱和办事流程。谁要看直接打开，不用在聊天记录里翻。

页面还能单独设成公开：点分享、打开「Share to web」，拿到一条公开地址，家里人不用注册账号，手机浏览器打开就能看。

项目地址：[点我跳转](https://github.com/docmost/docmost)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Docmost 装上。顺序反过来不行——Docmost 发出去的公开链接，是按编排里 APP\_URL 填的地址生成的，填成家里的局域网地址，家里人出门用流量就打不开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3000（就是下面编排里冒号左边的端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 docmost，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，再改三处：

```yaml
services:
  docmost:
    image: docmost/docmost:latest
    depends_on:
      - db
      - redis
    restart: unless-stopped
    environment:
      APP_URL: 'http://nas.local:3000'
      APP_SECRET: 'replace-with-a-long-random-secret'
      DATABASE_URL: 'postgresql://docmost:change-me-db-password@db:5432/docmost'
      REDIS_URL: 'redis://redis:6379'
    ports:
      - "3000:3000"
    volumes:
      - docmost:/app/data/storage

  db:
    image: postgres:18
    restart: unless-stopped
    environment:
      POSTGRES_DB: docmost
      POSTGRES_USER: docmost
      POSTGRES_PASSWORD: change-me-db-password
    volumes:
      - db_data:/var/lib/postgresql

  redis:
    image: redis:8
    restart: unless-stopped
    command: ["redis-server", "--appendonly", "yes", "--maxmemory-policy", "noeviction"]
    volumes:
      - redis_data:/data

volumes:
  docmost:
  db_data:
  redis_data:
```

**APP\_URL**：填第一步拿到的公网地址，前面带上 http://。

**APP\_SECRET**：随便敲一串 32 位以上的字母数字，用来加密登录状态。

**数据库口令**：DATABASE\_URL 里冒号后面那段，和下面 POSTGRES\_PASSWORD 填同一个，只用字母和数字。

**端口 3000:3000**：冒号左边是 NAS 上的端口，要和隧道里填的本地端口一致；冒号右边是容器里的端口，不用改。

docmost、db\_data、redis\_data 是三个数据卷，页面内容、数据库和缓存都在里面。勾上「创建项目后立即启动」，点确认。第一次要拉应用、数据库、缓存三个镜像，加起来七百多兆，等它下完。

### 三、建工作区、写第一篇页面

三个容器都起来以后，浏览器打开公网地址，会先让你创建工作区：填工作区名字（比如「我们的家」）、你的名字、邮箱和密码，点 Create workspace。这一步不需要收邮件验证，建好就是管理员。

左侧默认有一个 General 空间。点空间名旁边的菜单进 Space settings，可以给它改个名字，比如「家庭手册」。

点空间右上角的 New page 新建页面，先写标题。正文里：打 ## 加空格是二级标题，打 - 加空格是列表；要待办清单或表格，打一个斜杠 / 调出菜单，选 To-do list 或 Table。

待办清单前面的方框点一下就勾上，马上保存，下次打开还是勾着的状态。

### 四、分享给家里人

页面右上角点 Share，切到 Publish，把「Share to web」打开。输入框里就是公开地址，点右边的复制。

这条地址不用登录就能打开，家里人在手机上用流量也能看。勾上 Include sub-pages，子页面也会跟着公开；Search engine indexing 是让搜索引擎收录，自己家里用就别开。

要收回就回来把这个开关关掉，地址马上失效。

### 几点说明

**分享链接的前缀不对。** 说明 APP\_URL 填的不是公网地址。隧道删了重建、地址变了，要把 APP\_URL 改成新地址再重启，不然新发出去的分享链接还是旧地址。

**备份。** 重点是 db\_data 和 docmost 两个卷：一个存页面内容，一个存上传的图片附件，备份时一起备份。

**更新。** 镜像标签用的是 latest，升级之前先把数据卷备份一份，再到 Docker 里重新拉取镜像、启动项目。

**端口被占。** 如果 3000 被别的容器用了，把 ports 左边的 3000 换成别的（比如 3001），隧道里的本地端口也跟着改。

---

原文：<https://www.passnat.com/posts/docmost-nas-docker-family-shared-wiki-intranet-penetration>


## 相关文章

- [Mattermost 团队聊天：NAS Docker 部署 + 内网穿透](mattermost-team-chat-nas-docker-intranet-penetration.md)
- [ReDoc 接口文档：NAS Docker 部署 + 内网穿透](redoc-api-docs-nas-docker-openapi-intranet-penetration.md)
- [GodoOS 网页办公桌面：NAS Docker 部署 + 内网穿透](godoos-nas-docker-web-office-desktop-docs-sheets-files-intranet-penetration.md)
- [Moodist 环境音效：NAS Docker 部署 + 内网穿透](self-hosted-ambient-sound-platform-moodist-75-curated-sounds-meditation-privacy-first-open-source-free.md)
- [Ghost 博客：NAS Docker 部署 + 内网穿透，写完发个链接](ghost-docker-nas-one-click-cms.md)
