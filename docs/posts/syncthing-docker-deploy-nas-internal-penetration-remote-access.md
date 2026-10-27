---
title: "Syncthing 同步手机照片到 NAS：飞牛 Docker 部署 + 内网穿透"
date: 2026-10-13
description: "手机拍的照片自动存到自己的 NAS，不经过任何网盘。本文在飞牛 NAS 上用 Docker Compose 部署 Syncthing，用帕斯内网穿透把管理界面开放到公网，含先设口令、添加设备和共享文件夹的完整步骤。"
canonical: https://www.passnat.com/posts/syncthing-docker-deploy-nas-internal-penetration-remote-access
---

## 项目介绍

Syncthing 是一个开源的文件同步工具：几台设备之间直接同步同一个文件夹，数据在设备之间加密传输，不用上传到任何网盘。常见的用法是把手机相册同步到 NAS：手机里拍完的照片，会自己出现在 NAS 的文件夹里，手机上删了、换了，NAS 上这一份还在。

它不是靠账号认人，而是靠每台设备自己的设备 ID：两边都把对方的设备 ID 加进来，才会开始同步。陌生设备连过来只会弹一条提示，不点同意就什么都不会发生。

项目地址：[点我跳转](https://github.com/syncthing/syncthing)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Syncthing，再用 [帕斯内网穿透](https://passnat.com) 把它的网页管理界面开放到公网，不在家也能打开它加设备、看同步进度。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 syncthing，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  syncthing:
    image: syncthing/syncthing:latest
    container_name: syncthing
    hostname: home-nas
    network_mode: bridge
    environment:
      - PUID=1000
      - PGID=1000
    ports:
      - "8384:8384"
      - "22000:22000/tcp"
      - "22000:22000/udp"
    volumes:
      - ./data:/var/syncthing
    restart: unless-stopped
```

这段编排不用改，认一下几处就行：

冒号左边的 8384 是 NAS 上对外的网页管理端口，等会儿建隧道填的就是它；下面两行 22000 是设备之间同步用的端口（TCP 和 UDP 各一行）；./data 是 NAS 上存数据的文件夹，Syncthing 的配置和收到的照片都放在里面，备份就把它整个拷走；PUID 和 PGID 写 1000，是让它用飞牛默认的用户身份读写这个文件夹。hostname 那一行写的 home-nas 就是这台设备在页面里显示的名字，想换成别的可以改。

勾上「创建项目后立即启动」，点确认。镜像不大，下完就能起来，起来之后端口就通了。

### 二、用帕斯内网穿透开放到公网

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8384。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口）。

### 三、第一件事：给管理界面设用户名和密码

🔴 **用公网地址打开之后，先把口令设上。** Syncthing 出厂的管理界面没有口令，隧道一开，知道地址的人都能打开它，所以先设一个。页面第一次打开会问你要不要发送匿名使用报告，选「否」就行；然后点右上角的「操作」，选「设置」，切到「GUI」这个标签，「GUI 身份验证用户」和「GUI 身份验证密码」各填一个，点保存。

保存之后页面会立刻要你用刚设的用户名和密码登录，登进去就是 Syncthing 的主界面，左边是文件夹，右边是这台设备和远程设备。

### 四、把另一台设备（手机）连过来

手机上装好 Syncthing 客户端，打开之后把这台 NAS 的设备 ID 加进去（NAS 页面右边「此设备」那一栏的「标识」点开就能看到完整的设备 ID）。手机这边一连过来，NAS 的页面上方会出现一条提示，写着某某设备想要连接，点「添加设备」，名字可以改成你认得的，点保存。

手机上把相册文件夹共享给这台 NAS，NAS 的页面上方又会出现一条「想要共享文件夹」的提示，点「添加」，文件夹路径填 /var/syncthing/camera（也就是上面编排里挂出来的 ./data 下面的 camera 文件夹），点保存。

保存之后马上开始同步，文件夹那一行变成「最新」就是传完了。以后手机里新拍的照片，只要手机上的 Syncthing 在运行，就会自己传到这个文件夹里。

### 几点说明

**设备之间怎么互相找到。** Syncthing 默认靠官方的发现服务让两台设备互相找到对方，大多数网络下不用管。如果你的网络里找不到，可以在 PassNAT 隧道的详情里再新增一个端口 22000，手机里把这台 NAS 的地址手填成 tcp://公网地址:新端口，两边就能直连。

**同步方向。** 手机上共享相册时，建议在手机那边把文件夹类型设成「仅发送」，这样 NAS 上删文件不会影响手机里的照片。

**更新。** 镜像用的是官方写法的 latest，升级之前把 ./data 整个备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/syncthing-docker-deploy-nas-internal-penetration-remote-access>
