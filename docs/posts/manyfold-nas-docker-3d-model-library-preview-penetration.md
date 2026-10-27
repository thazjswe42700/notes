---
title: "Manyfold：NAS 上的 3D 模型库，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "单容器把 3D 打印模型收进 NAS，浏览器里转着看、按名字找；附编排、扫描规则和备份说明。"
canonical: https://www.passnat.com/posts/manyfold-nas-docker-3d-model-library-preview-penetration
---

## 项目介绍

Manyfold 是一个开源的 3D 模型库，专门用来管理自己攒的打印模型：把一个或多个装着模型文件的文件夹指给它，扫描一遍，网页里就会出现一个带 3D 预览的库。STL、OBJ、3MF 这些常用格式都能直接转着看，不用先切片，也不用装建模软件。

它还会顺手把文件大小、模型尺寸（长宽高）读出来，给每个模型加上名称、标签、合集和说明，模型多了以后按名字搜一下就能找到。

模型留在自己的 NAS 上，没有网盘的上传限制，也不用担心哪天服务关停。

项目地址：[点我跳转](https://github.com/manyfold3d/manyfold)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 Docker Compose 把服务装起来，再用 [帕斯内网穿透](https://passnat.com) 把它放出去，人在外面也能翻自己的模型库。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 manyfold，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  manyfold:
    image: manyfold3d/manyfold-solo:latest
    network_mode: bridge
    ports:
      - "3214:3214"
    volumes:
      - ./config:/config
      - ./models:/models
    environment:
      SECRET_KEY_BASE: 换成你自己的随机字符串
      PUID: 1000
      PGID: 1000
    restart: unless-stopped
```

这份编排用的是 **manyfold-solo** 镜像：数据库和缓存都打包在一个容器里，不用另外配 PostgreSQL 和 Redis，对家用 NAS 最省事。

**SECRET\_KEY\_BASE** 换成你自己的随机字符串，长一点就行，不要照抄示例。

**PUID** 和 **PGID** 填你 NAS 上的用户和组，飞牛默认都是 1000；填错了容器读不到模型目录。

端口一行写的是 **3214:3214**：冒号右边是容器里的端口，不要改；冒号左边是 NAS 上的端口，被占用的话换一个数字，后面隧道填同一个。

两个卷的作用：**./config** 放数据库和插件，**./models** 是你的模型文件夹。第一次启动要跑数据库迁移，大约一分钟，看到网页能打开就是好了。

### 二、第一次打开：建管理员、建库、扫描

浏览器打开 http://NAS的局域网地址:3214，第一次会跳到账户设置页，填邮箱、账户名和密码。单用户模式下填完就直接进库，不用再登录。

接着点「新资料库」：名称随便起，存储服务选「本地文件系统」，路径填容器里的 **/models**，也就是编排里挂载的那个位置。保存之后回到模型页，点顶栏的「扫描」，选「扫描新文件」。

**模型的放法有讲究：每个模型单独放一个子文件夹。** 比如 /models/vase-v2/vase-v2.stl。Manyfold 扫描时会把库根目录本身的变化排除掉，直接把 STL 丢在 /models 根下是扫不出来的。

扫描完成，卡片会出现在模型墙上。卡片默认是「加载」按钮，点一下才用浏览器的 WebGL 把模型画出来；点「打开」进入单文件页，可以拖动旋转、缩放看细节，右边是文件名、大小和尺寸。

### 三、放出去：人在外面也能打开

到这一步模型库还只能在局域网里打开。飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **3214**。

建好点开隧道、按一下电源，状态变成「运行中」，详情里那一串就是公网地址。用手机浏览器打开这个地址，同一个模型库就在眼前。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**模型要放在子文件夹里。** 这是这一期最容易踩的坑：库根目录的文件会被扫描器跳过，每个模型一个文件夹最稳，也方便以后按文件夹整理。

**备份 config 和 models。** config 里是数据库，models 是全部模型文件；升级镜像之前把这两个目录各复制一份，换机器时一起搬过去就能恢复。

**3D 预览靠浏览器。** 卡片和查看页用的是浏览器里的 WebGL，很老的设备或者关了硬件加速的浏览器可能画不出来，换一个浏览器就能看。

**没有公开分享链接。** 这一版的多用户和公网访问都走账号，适合自己家里用；把它放出去的时候，公网地址不要发到公开的地方。

---

原文：<https://www.passnat.com/posts/manyfold-nas-docker-3d-model-library-preview-penetration>
