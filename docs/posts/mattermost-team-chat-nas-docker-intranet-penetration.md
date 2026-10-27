---
title: "Mattermost 团队聊天：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源团队聊天 Mattermost：PostgreSQL 存消息，本地账号不用邮箱，频道、搜索、文件附件都能用；用帕斯内网穿透拿公网地址，成员在外面用手机流量也能接着聊。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/mattermost-team-chat-nas-docker-intranet-penetration
---

## 项目介绍

Mattermost 是一套开源的团队聊天工具，形态跟常见的群聊软件很像：左边是团队和频道，中间是消息，顶上搜索，消息里可以贴文件。区别在于它装在你自己的机器上：账号是本地账号，消息和文件都存在你家里的 NAS 上，不经过别人的服务器。

频道可以按项目或者话题分，群聊、私聊都有；消息支持 Markdown，传上去的文件在聊天里显示成一张卡片，点一下就能下载。全站搜索能把所有频道里提到某个词的对话找出来，翻旧账不用一个频道一个频道地爬。

账号不依赖邮箱：没有配邮件服务也能建管理员、建成员，适合小团队在自己家里用。

项目地址：[点我跳转](https://github.com/mattermost/mattermost)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Mattermost 和 PostgreSQL 装起来。先穿透、后部署，第一次打开就在公网地址上建管理员，以后成员在手机流量下也能收消息。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8065（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 mattermost，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  postgres:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_USER: mmuser
      POSTGRES_PASSWORD: change-me-db-password
      POSTGRES_DB: mattermost
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U mmuser -d mattermost"]
      interval: 5s
      timeout: 5s
      retries: 10

  mattermost:
    image: mattermost/mattermost-team-edition:11.11
    restart: unless-stopped
    ports:
      - "8065:8065"
    environment:
      MM_SQLSETTINGS_DRIVERNAME: postgres
      MM_SQLSETTINGS_DATASOURCE: postgres://mmuser:change-me-db-password@postgres:5432/mattermost?sslmode=disable&connect_timeout=10
      MM_EMAILSETTINGS_REQUIREEMAILVERIFICATION: "false"
      MM_TEAMSETTINGS_ENABLEOPENSERVER: "false"
    volumes:
      - mmdata:/mattermost/data
      - mmconfig:/mattermost/config
      - mmplugins:/mattermost/plugins
      - mmclientplugins:/mattermost/client/plugins
      - mmbleve:/mattermost/bleve-indexes
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  pgdata:
  mmdata:
  mmconfig:
  mmplugins:
  mmclientplugins:
  mmbleve:
```

整段编排里只有一处要改：

**change-me-db-password**：数据库口令，两处（POSTGRES\_PASSWORD 和 MM\_SQLSETTINGS\_DATASOURCE 里）换成同一串你自己的口令，字母数字都行，改的时候两处必须一致。装好之后别再改，改了数据库连不上，聊天记录也跟着打不开。

端口 8065 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，两个容器起来之后 Mattermost 会自己建表、跑一遍数据库迁移，第一次要等半分钟左右。

镜像这里固定用 11.11 这个稳定系列。数据分成两块：PostgreSQL 在 pgdata 卷里，Mattermost 的配置、上传的文件和搜索索引在另外五个卷里，备份的时候一起备份。

### 三、第一次打开：建管理员

用公网地址打开，第一次会直接进创建账号的页面：填邮箱（本地账号，不会真的发信）、用户名和密码，勾一下条款，点创建。第一个建出来的账号自动就是系统管理员，不需要邮件验证。

接着填组织名称（就是左上角显示的工作区名字），后面问「用什么工具」直接点跳过，邀请成员那一步也点完成设置，就进到工作区了。整个过程不用配 SMTP，也不用第三方登录。

**别打开公开注册。** 编排里 MM\_TEAMSETTINGS\_ENABLEOPENSERVER 已经设成 false：陌生人在公网地址上不能自己注册，成员只能由管理员加进来。自建聊天最怕的就是这个口子开着。

### 四、把成员加进来

在左边栏点「邀请成员」，会生成一条邀请链接，把链接发给同事，对方打开就能建账号、进这个团队。也可以进「系统控制台 → 用户管理」直接建账号，把用户名和初始密码线下告诉对方。

频道默认有 Town Square 和 Off-Topic 两个，另外点「添加频道」按项目建新的，比如「日常」「项目周会」，再把人加进对应的频道。

### 五、聊起来

**发消息和文件。** 底部输入框直接打字，回车发送；工具栏可以加粗、贴代码、传附件。传上去的文件在消息里显示成一张卡片，写着文件名和大小，点右边的下载按钮就存到本地。

**搜索。** 按 Ctrl+Shift+F 打开搜索，打字回车，右边栏会列出所有频道里命中的消息，点「跳转」直接定位到那条消息。

**中文界面。** 右上角头像 → 设置 → 显示 → 语言里选简体中文，整套界面就变成中文了。

### 几点说明

**数据在哪。** 消息、账号和文件都在那六个卷里，其中 pgdata 是数据库，mmdata 是上传的文件。迁移机器的时候把卷导出来带走，换一台 NAS 起来还是原来的聊天记录。

**没有邮件服务会怎样。** 忘记密码时的邮件重置用不了，成员密码忘掉只能由管理员在系统控制台里重置。日常聊天、文件、搜索都不受影响。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Mattermost 在这个模式下能正常用；但它装的是自己团队的聊天记录，公网地址别到处发，不用的时候在插件里把隧道电源关掉。

**语音和视频。** 团队版里另有一套通话插件，涉及额外的端口和网络配置，这篇没有启用，只演示文字聊天和文件；需要通话的话等熟了自己再按官方文档配。

---

原文：<https://www.passnat.com/posts/mattermost-team-chat-nas-docker-intranet-penetration>


## 相关文章

- [SyncTV：NAS Docker 同步影院 + 内网穿透](synctv-nas-docker-synchronized-movie-theater-intranet-penetration.md)
- [Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透](mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration.md)
- [Wiki.js 知识库：NAS Docker 部署 + 内网穿透](wikijs-nas-docker-team-knowledge-base-intranet-penetration.md)
- [开源评论引擎Comentario一键部署指南，NAS轻松搭建私人讨论系统，Docker快速启动](open-source-comment-engine-comentario-deployment-guide-nas-private-discussion-system-docker-quick-start.md)
- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
