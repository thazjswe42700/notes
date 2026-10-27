---
title: "Gokapi 文件分享：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署 Gokapi 文件分享：上传文件自动生成下载地址，可设几天到期、能下载几次、是否要口令；到期或下载完自动清理，链接随即失效；文件存在自己家 NAS 上，不经过第三方网盘；用帕斯内网穿透拿公网地址，在外面也能上传、发地址。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/gokapi-nas-docker-file-sharing-expiry-auto-cleanup-intranet-penetration
---

## 项目介绍

Gokapi 是一个自托管的文件分享服务，核心就一件事：把文件传上去，生成一条下载地址，发给该发的人；并且可以给每条地址设一个到期时间，到期自动删除。它不追求做成网盘，没有文件夹树，页面就是上传区、文件列表和访客下载页三块。

它适合临时传一个大安装包、几张原图、一份文档给朋友或同事：上传时设几天到期、能下载几次，链接发出去之后不用再操心，过期或被下载完就自动失效。文件存在自己家 NAS 上，不经过第三方网盘。

几个特点：只有管理员能上传，访客拿到地址只能下载；可以限制下载次数、设置下载口令；同一个文件重复上传会自动去重，不多占空间。项目地址：[点我跳转](https://github.com/Forceu/Gokapi)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Gokapi 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后在外面也能上传、发地址。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3720（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 gokapi，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  gokapi:
    image: f0rc3/gokapi:latest
    restart: unless-stopped
    ports:
      - "3720:53842"
    environment:
      TZ: Asia/Shanghai
    volumes:
      - gokapi_data:/app/data
      - gokapi_config:/app/config

volumes:
  gokapi_data:
  gokapi_config:
```

这段编排不用改任何东西就能启动：端口 3720 是对外的访问端口，容器里 Gokapi 自己监听 53842；数据卷 gokapi\_data 存文件，配置卷 gokapi\_config 存数据库和设置，两个卷都要留着。

端口 3720 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像拉下来之后几秒就起来了。

文件都存在 gokapi\_data 这个具名卷里，备份这一个卷就够了；它默认把文件存在本地磁盘，不依赖 S3 之类的外部对象存储。

### 三、第一次打开，跟向导走一遍

第一次用公网地址打开，会自动跳到 /setup 设置向导。数据库选默认的 SQLite，Web 服务两项保持默认，认证方式选「Username / Password」，填上管理员用户名和口令；存储选「Local Storage」，加密等级选 0。向导里有一项 Public facing URL，填你实际访问的地址（比如公网地址或局域网地址），后面生成的下载地址会用这个主机名。

最后点 Submit 保存。之后打开 /admin，用刚才设的管理员用户名和口令登录，就进到后台了。

### 四、上传一个文件，拿到下载地址

后台的 Upload 页上方是拖拽区，把文件拖进去，或者点一下选择文件。上传之前可以设三样：Limit Downloads 限制能下载几次，Expiry 设几天后到期，Password 给下载加一道口令（不填就是无口令）。

文件上传后立刻出现在下面的列表里，一行一个：文件名、大小、剩余下载次数、到期时间、已下载次数、ID。点行内的「URL」按钮，下载地址就复制到剪贴板，形如 http://你的地址/d?id=一串ID，发给对方就行。

想改到期时间或下载次数，点行内的铅笔图标打开编辑框，改完 Save changes；不想要了，点红色垃圾桶直接删掉。

### 五、访客下载，以及到期后的清理

对方在浏览器里打开下载地址，会看到一个简单的卡片：文件名、大小、一个 Download 按钮，点了就开始下载。整个过程不用注册、没有广告，也不会被转码或改文件名。

到期时间一到，或者下载次数用完，这条地址就自动失效；再打开会显示「File not found」，提示链接可能已过期或下载次数已用完。文件本身也会被清理掉，不用你手动去删。

### 几点说明

**数据在哪。** 文件在 gokapi\_data 卷，数据库和配置在 gokapi\_config 卷，备份这两个卷就行；想搬到别的机器，把卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先把两个卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Gokapi 本身不需要 HTTPS 才能用；但下载地址谁拿到谁都能打开，公网地址和链接别到处发，用完了可以在插件里把隧道关掉。

**不接外部存储。** 这篇用的是本地卷，文件和数据库都在 NAS 上；需要接 S3 兼容对象存储的场景这篇不讲。

---

原文：<https://www.passnat.com/posts/gokapi-nas-docker-file-sharing-expiry-auto-cleanup-intranet-penetration>
