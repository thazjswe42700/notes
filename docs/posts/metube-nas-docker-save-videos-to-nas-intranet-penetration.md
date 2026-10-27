---
title: "MeTube：NAS Docker 部署 + 内网穿透，远程把视频存回家"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源下载队列 MeTube：粘一条地址就排队下载，文件直接落在 NAS 文件夹；用帕斯内网穿透拿公网地址，在外面也能把视频丢回家。没有内建账号，用隧道口令加路径前缀保护访问。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/metube-nas-docker-save-videos-to-nas-intranet-penetration
---

## 项目介绍

MeTube 是一个开源的视频下载队列：在网页里粘一条视频地址，它在服务器后台把文件下下来，直接存进 NAS 的文件夹。手机拍的、自己网盘里的、公版素材，只要拿到一个能直接访问的地址，就能交给它去下，人不用守着。

界面就一页：上面一个输入框加下载按钮，下面是「正在下载」和「已完成」两块。正在下载的行里有进度条、速度、剩余时间；下完的进已完成，显示文件大小和下载时间，每一行都能下载回本机、打开原地址或者删除。文件落在挂载的 downloads 目录里，NAS 上直接可见。

它底层用的是开源的 yt-dlp，支持几百个站点和直链视频；还能按固定间隔去检查一个频道有没有更新。这里只讲最常用的那条路：粘地址、排队、落盘。

项目地址：[点我跳转](https://github.com/alexta69/metube)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 MeTube 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后在外面也能把视频丢回家里的 NAS。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8081（就是下面编排里冒号左边那个端口）。

**MeTube 没有内建账号，登录这一步交给隧道。** 建隧道的时候把「网页访问用户名 / 密码」两格填上，之后打开公网地址浏览器会先要一次口令，口令对了才进得到 MeTube 的界面。MeTube 自己不用 Authorization 头，隧道这层口令不会跟它抢鉴权。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 metube，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  metube:
    image: ghcr.io/alexta69/metube:latest
    container_name: metube
    ports:
      - "8081:8081"
    volumes:
      - ./downloads:/downloads
    environment:
      - DOWNLOAD_DIR=/downloads
      - STATE_DIR=/downloads/.metube
      - TEMP_DIR=/downloads
      - OUTPUT_TEMPLATE=%(title)s.%(ext)s
      - URL_PREFIX=/my-videos
      - TZ=Asia/Shanghai
    restart: unless-stopped
```

整段编排里只有一处按需改：

**URL\_PREFIX**：界面挂在哪个路径下，默认写的是 /my-videos，打开就是「公网地址/my-videos/」。把它换成一段只有你知道的英文加数字，别人就算扫到地址也打不开界面——这是 MeTube 自带的、除隧道口令之外的第二道门。

端口 8081 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了，容器几秒钟就绪。

下载的文件全部落在项目目录下的 downloads 文件夹里，队列记录存在 downloads/.metube，重建容器之后已完成列表还在。

### 三、第一次打开：先过口令

用公网地址打开，浏览器先弹一个用户名密码框，填隧道里设的那组，然后就到了 MeTube 首页。地址栏后面记得带上 /my-videos/，只输域名是打不开的。

页面是英文的，但只有几个词：Download 是下载，Downloading 是正在下载，Completed 是已完成。输入框下面的 Type / Codec / Format / Quality 四个下拉，分别选类型（视频 / 音频）、编码、封装和画质，不想挑就全部留 Auto / Best。

### 四、把视频丢进去

**粘地址。** 在输入框里粘一条视频地址，点 Download。地址必须是能直接访问的公网地址；MeTube 会拒绝指向内网地址的链接（防止被当成跳板去访问局域网里的东西），这是它的安全设计，不是坏了。

**看队列。** 任务立刻出现在 Downloading 里，进度条、速度、剩余时间实时刷新。同时丢几条也行，它按顺序一条条下。

**看落盘。** 下完自动进 Completed，显示文件大小和下载时间。点行尾第一个图标是下载回本机，第二个是打开原地址，第三个是删除文件。文件本身就在 NAS 的 downloads 文件夹里，用飞牛的文件管理或者别的设备都看得到。

要下音频，把 Type 从 Video 换成 Audio；要挑画质，Quality 里选 1080p、720p 这些，或者干脆留 Best。

### 五、只下自己的东西

这个工具本身没有内容来源，下什么完全取决于你粘的地址。建议只用在自己拍的视频、自己网盘里的文件、以及明确允许下载的公版素材上；别人网站上的影视内容有版权，不要拿它去抓。

把手机里拍的素材先传到网盘或者自己的服务器，拿到直链，再交给 MeTube 下回 NAS，手机空间腾出来了，素材也集中到了家里的硬盘上。

### 几点说明

**数据在哪。** 视频文件在 downloads 目录，队列状态在 downloads/.metube，整个项目目录一起备份就行。

**升级。** 镜像标签用的是 latest，升级前先备份项目目录，再到 Docker 里重新拉取镜像、重启项目。

**访问保护。** 隧道口令 + URL\_PREFIX 两道门都建议留着；公网地址别到处发，用完了可以在插件里把隧道关掉。

**定时检查。** 页面最下面还有 Subscriptions，可以按频道定时检查更新；这一篇没展开，需要的时候按界面提示加一条就行。

---

原文：<https://www.passnat.com/posts/metube-nas-docker-save-videos-to-nas-intranet-penetration>


## 相关文章

- [开源私有视频会议系统Nettu Meet，Docker一键部署，支持屏幕共享和内网穿透远程访问](open-source-nettu-meet-video-conference-docker-deployment.md)
- [Metabase 数据看板：NAS Docker 部署 + 内网穿透](metabase-nas-docker-data-dashboard-intranet-penetration.md)
- [Gokapi 文件分享：NAS Docker 部署 + 内网穿透](gokapi-nas-docker-file-sharing-expiry-auto-cleanup-intranet-penetration.md)
- [LubeLogger 保养记录：NAS Docker 部署 + 内网穿透](lubelogger-nas-docker-car-maintenance-fuel-records-intranet-penetration.md)
- [PicoShare 文件分享：NAS Docker 部署 + 内网穿透](picoshare-nas-docker-file-sharing-upload-link-intranet-penetration.md)
