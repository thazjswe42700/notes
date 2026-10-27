---
title: "自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷"
date: 2026-09-09
description: "在 NAS 上用 Docker Compose 部署开源 RSS 阅读器 FreshRSS：把常看网站的订阅地址加进来，定时抓取，只留正文；用帕斯内网穿透拿公网地址，出门在手机上也能接着刷。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration
---

## 项目介绍

FreshRSS 是一个开源的 RSS 阅读器。你常看的网站只要对外提供订阅地址，就可以加进来；之后这些站点更新了什么，它自己定时去抓回来，按时间排在同一个列表里。不用一个站一个站地翻，也没有推荐流和开屏广告。

订阅列表、抓回来的文章、你标了收藏的内容，全部存在自己 NAS 的一个数据卷里，备份的时候把那个卷（或者挂载目录）拷走就是完整备份。它在 GitHub 上开源，官方提供了打好的镜像，NAS 上拉下来就能跑，不需要自己构建。

它本身很轻：一个容器就够，数据库用内置的 SQLite，不用另外再起数据库容器；装完抓完几个订阅，占用的内存也就几十兆，常年开着不心疼。

项目地址：[点我跳转](https://github.com/FreshRSS/FreshRSS)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。阅读器是那种「随手就想刷一下」的东西，装好之后给它一个公网地址，出门在地铁上、在外面等车都能接着看。家里没有公网 IP 的话，用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里直接装插件就行。

它没有必须提前填公网地址的字段，先装再建隧道也能用；本文按「先穿透、后部署」的顺序写，和视频里一致。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8077（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 freshrss，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  freshrss:
    image: freshrss/freshrss:latest
    container_name: freshrss
    restart: unless-stopped
    ports:
      - "8077:80"
    volumes:
      - freshrss_data:/var/www/FreshRSS/data
      - freshrss_extensions:/var/www/FreshRSS/extensions
    environment:
      TZ: Asia/Shanghai
      CRON_MIN: '3,33'
      FRESHRSS_INSTALL: |-
        --db-type sqlite
        --default_user admin
        --language zh-cn
      FRESHRSS_USER: |-
        --user admin
        --password ChangeMe123
        --email you@example.com
        --language zh-cn
volumes:
  freshrss_data:
  freshrss_extensions:
```

**端口映射**：容器里是 80，外面映射到 8077。8077 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。

**两个数据卷**：freshrss\_data 存数据库和文章，freshrss\_extensions 存扩展。用命名卷而不是挂到某个文件夹，是为了避开 NAS 上的目录权限问题；真要备份的时候，在 Docker 里把这个卷导出就行。

**账号自动创建**：FRESHRSS\_INSTALL 和 FRESHRSS\_USER 这两段是官方镜像自带的初始化参数，第一次启动时按它把数据库和账号建好，不用再点安装向导。装之前把 --password 后面那串 ChangeMe123 换成你自己的口令，--email 也换成你自己的邮箱。

**定时抓取**：CRON\_MIN 设成 3,33，意思是每小时的第 3 分钟和第 33 分钟各抓一次；改这个值就是改抓取频率。

勾上「创建项目后立即启动」，镜像是二十多兆，很快就能拉完。

### 三、第一次打开：把界面切成中文

用公网地址打开，就是登录页。账号和口令就是编排里 FRESHRSS\_USER 写的那组。

进去之后界面默认是英文：点右上角齿轮 →「显示（Display）」→ 语言选「简体中文（zh-CN）」→ 提交，之后整个界面就是中文了。

### 四、添加订阅

点左侧的「订阅管理」，再点「添加订阅源或分类」，在「源地址」里粘上网站的订阅地址（一般就是以 feed 或 rss 结尾的那个地址），分类保持默认的「未分类」，点「添加」。

加完它立刻会去抓一次，几秒钟文章就进来了；以后按 CRON\_MIN 的频率自己更新。想分门别类，可以先用下面的「添加分类」建几个分类，再在添加订阅源时选对应分类。

已经有一份订阅列表的，可以在「导入/导出」里直接导入 OPML 文件，一次把所有源搬过来。

### 五、日常怎么用

回到首页，左边是订阅的源和未读数，中间按天分组排好，一条条往下刷。点开一条就是干净的正文，右上角的箭头可以直接跳回原网站。

读完点一下「标记已读」，左边和顶部的未读数就往下掉；想留着的点一下星标加进收藏，回头在「收藏」里找。

手机上打开同一个公网地址，排版会收成一列，标题长的会截断，点开照样是正文。在家没读完的，出门接着看；电脑和手机看到的是同一份订阅。

### 几点说明

**数据在命名卷里。** 换机器或者重建项目之前，先在 Docker 里把 freshrss\_data 备份出来；直接删项目并勾选删除卷，订阅和文章就一起没了。

**明文 HTTP 访问。** 编排里没有配 HTTPS，走公网地址时浏览器会显示不安全；阅读器的核心功能不受影响，但不要在公共网络里用它登录别的服务。

**不配发信邮箱也能用。** 只是「忘记密码」的重置邮件发不出去；口令忘了就改编排里的 --password 重建，或者进容器用命令行改。

**抓取依赖源站。** 有些站点会挡抓取、或者干脆不提供订阅地址，加进去会一直抓不到内容，换一个源就行。

**更新。** 镜像标签用的是 latest，升级前先备份数据卷，再到 Docker 里重新拉取镜像、重启项目。

---

原文：<https://www.passnat.com/posts/self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration>
