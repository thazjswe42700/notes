---
title: "Ghost 博客：NAS Docker 部署 + 内网穿透，写完发个链接"
date: 2025-10-16
description: "在 NAS 上用 Docker Compose 部署 Ghost 博客：MySQL 8 加 Ghost 两个容器，第一次打开建管理员，后台写文章、点发布，前台就有一张公开网页，别人不用注册就能读。用帕斯内网穿透拿公网地址，在外面也能打开；不配 SMTP 也能登录和发文。含完整编排、发布步骤和会员关闭方法。"
canonical: https://www.passnat.com/posts/ghost-docker-nas-one-click-cms
---

## 项目介绍

Ghost 是一个开源的博客和内容发布平台：后台写文章、排版，点发布，前台就有一张可以分享的网页。它把「写」和「展示」分开——编辑器里专心写字，主题负责长什么样，读者打开地址就能看，不需要注册账号。

它适合想认真写点东西、又不想把文章交给平台的人：技术笔记、周记、读书笔记都可以。文章支持草稿、定时发布、标签和 SEO 信息；主题可以换，也可以用官方默认的。项目地址：[点我跳转](https://github.com/TryGhost/Ghost)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Ghost 用 Docker Compose 装起来。Ghost 有一项配置必须填公网地址（编排里的 url），所以先穿透、后部署，装完就能直接用公网地址打开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 2368（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来，下一步要把它填进编排。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 ghost，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: mysql:8.0
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: 换成你自己的口令
      MYSQL_DATABASE: ghost
      MYSQL_USER: ghost
      MYSQL_PASSWORD: 换成你自己的口令
    volumes:
      - db:/var/lib/mysql
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h localhost -uroot -p$$MYSQL_ROOT_PASSWORD"]
      interval: 5s
      timeout: 5s
      retries: 20
  app:
    image: ghost:alpine
    restart: unless-stopped
    ports:
      - "2368:2368"
    environment:
      url: http://ghost.local
      security__staffDeviceVerification: "false"
      database__client: mysql
      database__connection__host: db
      database__connection__user: ghost
      database__connection__password: 换成你自己的口令
      database__connection__database: ghost
    volumes:
      - data:/var/lib/ghost/content
    depends_on:
      db:
        condition: service_healthy
volumes:
  db:
  data:
```

这段编排里要改三处口令和一处地址：

**MYSQL\_ROOT\_PASSWORD**：MySQL 的 root 口令，健康检查也要用，改一处即可。

**MYSQL\_PASSWORD** 和 **database\_\_connection\_\_password**：Ghost 连数据库用的口令，出现两次，两处要一致。

**url**：把 http://ghost.local 整串换成你刚才拿到的公网地址（带 http://，不要末尾斜杠）。Ghost 用这个值生成文章链接和资源地址，装好之后再改，旧链接会跟着变，建议一次填对。

端口 2368 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，第一次会拉 MySQL 和 Ghost 两个镜像；Ghost 首次启动要跑数据库迁移，大约两三分钟，期间打开会看到 503，等一会儿就好。

### 三、第一次打开，建管理员

浏览器打开「公网地址/ghost」，例如 http://你的公网地址/ghost。第一次会进安装页：填站点标题、你的名字、邮箱和密码，点「Create account & start publishing」。这个账号就是管理员，不需要邮件验证。

登录只用邮箱和密码。编排里已经写了 security\_\_staffDeviceVerification: "false"——不关掉它的话，Ghost 6 会在密码之外再发一封验证码邮件，没配 SMTP 就会卡在登录页。关掉之后，不配邮件也能正常进后台。

### 四、写第一篇文章并发布

进后台左侧的 Posts，点 New post。标题写在最上面，正文直接往下敲；选中文字可以加粗、做标题、插链接和图片。写完点右上角的 Publish，再点「Continue, final review」，最后点「Publish post, right now」。

没有配邮件时，发布面板会显示「Not sent as newsletter」，意思是这次只发网页、不发邮件，正好符合「只做文章发布和公开阅读」。发布之后，在 Posts 列表里能看到状态变成 Published。

### 五、把地址发出去，别人直接看

公开文章页的地址就是「公网地址」加上文章 slug，在文章页右上角的 Share 里也能复制。把这个地址发到群里，别人不用注册、不用登录，打开就能读。

如果不想让页面出现会员注册入口，可以在后台设置里把会员注册关掉：Members → Access，把「Who can sign up?」设成 Nobody；评论也可以在设置里关。这样前台就只剩文章本身。

### 几点说明

**数据在哪。** 文章、图片和主题在 ghost\_content 这个具名卷里，MySQL 数据在 db 卷里；备份这两个卷就够了。

**邮件是可选的。** 不配 SMTP 不影响写文章、发布和公开阅读，只是不会发会员邮件和找回密码邮件。需要时再在编排里加 mail\_\_transport 和 SMTP 相关变量。

**后台是英文。** Ghost 的管理后台目前只有英文界面；前台主题文字也可以按主题自行调整，不影响写中文文章。

**公网地址别乱发。** 后台登录地址是「公网地址/ghost」，知道地址的人都能看到登录框；不写的时候就到插件里把隧道电源关掉。

**升级。** 镜像标签用的是 alpine，升级前先备份两个卷，再到 Docker 里重新拉取镜像、重启项目；Ghost 会在启动时自动跑数据库迁移。

---

原文：<https://www.passnat.com/posts/ghost-docker-nas-one-click-cms>


## 相关文章

- [Rallly 约时间投票：NAS Docker 部署 + 内网穿透](rallly-nas-docker-group-scheduling-poll-intranet-penetration.md)
- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
- [单文件PHP数据库管理工具Adminer，支持MySQL、PostgreSQL等多种数据库，NAS极简部署内网穿透远程管理](adminer-php-database-management-tool-mysql-postgresql-nas-deployment-internal-penetration-remote-management.md)
- [轻量级PHP博客系统Typecho在NAS上Docker部署，内网穿透远程访问详细教程指南](typecho-nas-docker-deployment-internal-penetration-remote-access.md)
- [NAS部署Waline评论系统，Docker极简安装，内网穿透远程访问博客评论管理](nas-deploy-waline-docker-simple-install-intranet-penetration-remote-access-blog-comment-management.md)
