---
title: "Stump 电子书库：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署 Stump 电子书库：把 EPUB、PDF、CBZ 放进一个目录，建库扫描后就能在浏览器里直接读，每个账号单独保存阅读进度，换设备登录接着上次的位置。含单容器编排、初始化管理员、建库扫描和三种阅读器的实测步骤，用帕斯内网穿透在外面也能翻自己的书。"
canonical: https://www.passnat.com/posts/stump-nas-docker-ebook-library-reading-progress-intranet-penetration
---

## 项目介绍

Stump 是一个自托管的电子书和漫画服务器。把 EPUB、PDF、CBZ 这类文件放进 NAS 的一个目录，在网页里建好书库让它扫描，之后就能在浏览器里直接翻开书看，不用把文件挨个拷到每台设备上。它会给每个账号单独记录读到哪一页：今天在电脑上读了三章，明天用手机打开，首页的「Continue reading」直接接着上次的位置。

它适合家里有几台设备、书散在好几个硬盘里的人。阅读器是内置的：EPUB 按章节翻页，CBZ 和 PDF 按页翻，不用另装阅读软件。本文只用自己做的三本书实测了目录扫描、网页阅读和进度保存，没有拿官方的格式支持表当结论。项目地址：[点我跳转](https://github.com/stumpapp/stump)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Stump 用 Docker Compose 装起来。Stump 本身没有要填公网地址的配置项，所以先装后穿透也可以；这里按「先穿透、后部署」的顺序写，装完直接用公网地址打开就能看。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 10801（就是下面编排里冒号左边的端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 stump，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  stump:
    image: aaronleopold/stump:latest
    container_name: stump
    restart: unless-stopped
    ports:
      - "10801:10801"
    environment:
      - PUID=1000
      - PGID=1000
      - STUMP_CONFIG_DIR=/config
    volumes:
      - ./config:/config
      - ./books:/data
```

这段编排只有两个卷和两个环境变量，没什么要改的口令：

**PUID / PGID**：容器里用哪个用户读写文件。飞牛上一般填 1000 就行；如果书库目录属于别的用户，把它改成那个用户的数字 ID，否则扫描时可能读不到文件。

**./config**：配置和数据库放这里，备份这个目录就够了。

**./books**：书库目录。把 EPUB / PDF / CBZ 文件放进项目目录下的 books 文件夹，可以按作者或系列再分子文件夹。想直接用 NAS 上现成的书库目录，就把冒号左边的 ./books 换成那个目录的完整路径。

**10801:10801**：冒号左边是 NAS 上的端口。被占用就换一个数字，隧道里的本地端口也跟着改。

勾上「创建项目后立即启动」，第一次会拉取 Stump 镜像，起来之后大约几秒钟就能打开。

### 三、初始化管理员

浏览器打开「公网地址」，第一次访问会跳到 /auth，页面写着 Initialize your server。这里只要填用户名和密码，点「Create Account」，这个账号就是管理员，建完直接进首页。不需要邮箱，也不需要配 SMTP 收验证码。

界面目前是英文的，没有中文语言选项。几个常用词记住就行：Home 首页、Explore 全部书目、Libraries 书库、Series 系列、Books 单本。

### 四、建书库、扫描目录

首页点「Create library」进入四步向导。

**第一步 Basics**：Name 填「我的书库」，Path 填 /data —— 这是容器里的路径，对应编排里的 ./books，不是在 NAS 上看到的完整路径。Oneshots directory 留空。

**第二步 Scanning**：Library pattern 选 Series priority（每个直接放着书文件的文件夹算一个系列），这也是默认值。下面的 Metadata 和 Watch 两项默认开着：前者会从 EPUB 里读标题、作者和语言，后者会在目录有变化时自动更新。

**第三步 Thumbnails**：缩略图默认不开，这次先跳过。

**第四步 Review**：确认路径和模式，保持「Scan after creation」勾选，点「Create library」。

扫描很快，几本书一两秒就出现在系列页。系列卡片上写着「3 books」，点进去能看到这个系列下的每一本；切到 Books 页签则是所有书的平铺列表。

### 五、打开阅读器，进度会自动保存

点任意一本书进详情页，右上角有「Read」和「Download」。点 Read 进入阅读器：

**EPUB**：左侧是章节内容，顶部显示「1 / 3」这样的页码，用左右方向键或两侧的翻页按钮换章；工具条里还有目录（Location manager）、主题设置和全屏。

**CBZ 和 PDF**：按页显示，页码条可以直接点某一页，方向键翻页，右下角还会显示这次的阅读时长。

进度是按账号保存的。我把 EPUB 翻到第三章后刷新页面，仍然停在「3 / 3」；回到首页，「Continue reading」卡片显示「星海拾遗 01 远航 · 67%」。CBZ 读到第 3 页，卡片上写的是「2 pages left」。换一台设备、用同一个账号登录，看到的也是这个进度。

### 几点说明

**数据在哪。** 数据库和配置都在 ./config 里，书库原文件在 ./books 里，备份这两个目录就够了。

**扫描靠目录结构。** Series priority 模式下，每个直接放书文件的文件夹算一个系列；如果你习惯一层层嵌套，建库时也可以选 Collection priority，让最上层文件夹里的内容归成一个系列。库路径不能和另一个库的路径重叠。

**缩略图可以后开。** 建库时没开缩略图的话，EPUB 会显示一张空白封面卡；想让它按书生成封面，可以进书库设置打开缩略图再重新扫描。

**明文 HTTP 访问。** 整个流程——建管理员、建库、扫描、三种阅读器——都在 http 地址下跑通，没有 HTTPS 也能用。不过公网地址谁拿到都能打开登录页，别到处发，用完可以在插件里把隧道关掉。

**升级。** 镜像标签用的是 latest，升级前先备份 ./config，再到 Docker 里重新拉取镜像、重启项目。

---

原文：<https://www.passnat.com/posts/stump-nas-docker-ebook-library-reading-progress-intranet-penetration>
