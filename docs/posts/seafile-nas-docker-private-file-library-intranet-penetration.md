---
title: "Seafile 私有文件库：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源文件库 Seafile 社区版：按资料库整理文件，按人设置可读写或只读，同名文件自动保留历史版本，访客用分享地址直接看；用帕斯内网穿透拿公网地址，出门用手机也能打开自己家的文件。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/seafile-nas-docker-private-file-library-intranet-penetration
---

## 项目介绍

Seafile 是一个开源的私有文件库，面向的是「一家人、一个小团队的文件该放在哪」这件事：文档、表格、照片、合同按资料库分类存放，每个人一个账号，谁能看、谁能改按人和资料库分别设置，每次上传同名文件都会留下旧版本，改坏了可以翻回去。

它和普通网盘的区别在于数据位置：文件就在自己家的 NAS 上，权限、备份、留多久都自己说了算。网页端可以直接预览 Markdown、图片、PDF 和常见文档，分享可以按账号共享，也可以生成一个访客打开就能看的分享地址。

本文用的是开源社区版（CE），Docker 一条编排就能装起来。

项目地址：[点我跳转](https://github.com/haiwen/seafile)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Seafile 用 Docker Compose 装起来。注意顺序：Seafile 启动时会把「服务器地址」写进配置，所以要先有隧道域名、再部署；装完直接用公网地址登录。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8088（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址。把其中的域名部分（不带 http:// 或 https://）记下来，下一步要填进编排的 SEAFILE\_SERVER\_HOSTNAME；如果分配的是 https 地址，编排里协议那一项也改成 https。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 seafile，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: mariadb:10.11
    restart: unless-stopped
    environment:
      - MYSQL_ROOT_PASSWORD=change-me-db-root
      - MYSQL_LOG_CONSOLE=true
      - MARIADB_AUTO_UPGRADE=1
    volumes:
      - seafile-mysql:/var/lib/mysql
    healthcheck:
      test: ["CMD", "/usr/local/bin/healthcheck.sh", "--connect", "--mariadbupgrade", "--innodb_initialized"]
      interval: 20s
      start_period: 30s
      timeout: 5s
      retries: 10
    networks:
      - seafile-net

  redis:
    image: redis
    restart: unless-stopped
    command: ["/bin/sh", "-c", "exec redis-server --requirepass change-me-cache"]
    networks:
      - seafile-net

  seafile:
    image: seafileltd/seafile-mc:13.0-latest
    restart: unless-stopped
    ports:
      - "8088:80"
    volumes:
      - seafile-data:/shared
    environment:
      - SEAFILE_MYSQL_DB_HOST=db
      - SEAFILE_MYSQL_DB_USER=seafile
      - SEAFILE_MYSQL_DB_PASSWORD=change-me-db
      - INIT_SEAFILE_MYSQL_ROOT_PASSWORD=change-me-db-root
      - SEAFILE_MYSQL_DB_CCNET_DB_NAME=ccnet_db
      - SEAFILE_MYSQL_DB_SEAFILE_DB_NAME=seafile_db
      - SEAFILE_MYSQL_DB_SEAHUB_DB_NAME=seahub_db
      - TIME_ZONE=Asia/Shanghai
      - INIT_SEAFILE_ADMIN_EMAIL=admin@example.com
      - INIT_SEAFILE_ADMIN_PASSWORD=change-me-admin
      - SEAFILE_SERVER_HOSTNAME=seafile.example.com
      - SEAFILE_SERVER_PROTOCOL=http
      - JWT_PRIVATE_KEY=change-me-to-a-random-32-char-string
      - CACHE_PROVIDER=redis
      - REDIS_HOST=redis
      - REDIS_PASSWORD=change-me-cache
      - ENABLE_SEADOC=false
      - ENABLE_NOTIFICATION_SERVER=false
      - ENABLE_SEAFILE_AI=false
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - seafile-net

networks:
  seafile-net:

volumes:
  seafile-mysql:
  seafile-data:
```

编排里有几处要换成自己的：

**数据库口令**：SEAFILE\_MYSQL\_DB\_PASSWORD 和 INIT\_SEAFILE\_MYSQL\_ROOT\_PASSWORD 换成自己的一长串，两处要能对得上。

**管理员邮箱和密码**：INIT\_SEAFILE\_ADMIN\_EMAIL 和 INIT\_SEAFILE\_ADMIN\_PASSWORD 是第一次启动时创建的管理员账号，只生效一次，记好。

**服务器地址**：服务器地址那一项填上一步拿到的隧道域名；协议按公网地址是 http 还是 https 改对应那一项。

**JWT 密钥**：JWT\_PRIVATE\_KEY 换成一串至少 32 位的随机字符，字母数字混着来，别用示例值。

端口 8088 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，第一次启动会建库、初始化管理员，大约一两分钟；这段时间浏览器打不开是正常的，看容器日志出现 Seahub is started 就好了。

数据库放在 seafile-mysql 具名卷里，文件放在 seafile-data 具名卷里，备份这两个卷就够了。MariaDB 的数据目录不要改成 NAS 上的绑定挂载——部分 NAS 的目录权限会让数据库起不来，用具名卷最稳。

### 三、第一次登录和管理后台

用公网地址打开，输入编排里填的管理员邮箱和密码。进去是中文界面，左边是工作区，中间是文件列表，默认已经有一个私人资料库。

管理入口在右上角头像菜单里：点「系统管理」，能看到用户、群组、资料库、分享链接这些页面。以后加家人账号就在这里点「添加用户」，填邮箱、名称和初始密码；对方第一次登录会被要求改一次密码。

### 四、建资料库、上传和预览

点「我的资料库」旁边的新建按钮，给资料库起个名字，比如「项目资料」。进去以后点右上角的上传按钮，把文件拖进来，也可以选择整个目录上传。传完在文件列表里点文件名就能预览：Markdown 直接渲染成排版好的文档，图片、PDF 也能在网页里看。

文件列表里能看到大小和更新时间；同一个文件名再次上传时会问要不要替换，替换之后就多了一个版本。

### 五、按人共享：可读写 / 只读 / 分享地址

在资料库的「共享」里选「共享给用户」，搜家人的账号，选权限再提交：可读写可以查看、修改、上传、下载和同步；只读只能看和下载，改不了。列表里每个人一条，权限随时可以改。

临时给朋友看文件，不必给他建账号：同一页的「共享链接」里点生成链接，可以设权限（预览与下载 / 仅查看 / 下载和上传）和访问范围。生成后把地址发出去，对方不用登录就能打开资料库，看到的是同样的文件列表。

要收回权限，把共享列表里的人删掉、或者把分享链接删除即可；已经发出去的地址立刻失效。

### 六、版本历史：改坏了翻回去

打开一个文件，右上角「更多操作」里点「历史」，右边会列出这个文件的每个版本，谁在什么时候传的都记着。点旧版本可以预览当时的内容，需要的话下载回来；新版本一直都在，不会被旧版本覆盖掉。

这个功能对表格特别有用：一家人改同一份预算，改乱了不用互相问「你改了什么」，翻历史版本对一下就清楚。

### 几点说明

**数据在哪。** 数据库在 seafile-mysql 卷里，文件在 seafile-data 卷里，两个卷一起备份。搬家时把这两个卷导出来，带到新机器上再挂回去。

**升级。** 镜像用的是 13.0-latest，升级前先备份两个卷，再重新拉取镜像、重启项目；数据库结构会跟着镜像版本升级，别跨大版本直接跳。

**在线 Office 和同步客户端。** 本篇只验证了网页端的上传、预览、共享、版本历史。在线 Office（SeaDoc）需要额外服务，桌面和手机同步客户端也要单独安装配置，这两块本文没有验证，不在这里承诺；编排里已经把 SeaDoc 关掉，需要的人按官方文档再开。

**明文 HTTP 访问。** 内网穿透给的是 http 地址时，Seafile 本身能正常用；但它装的是自己家的文件，公网地址别到处发，不用的时候可以在插件里把隧道关掉。

**忘记管理员密码。** 没配邮件服务时网页端找不回密码，可以在 NAS 上执行容器里的 reset-admin 脚本重置，具体命令见官方文档。

---

原文：<https://www.passnat.com/posts/seafile-nas-docker-private-file-library-intranet-penetration>
