---
title: "聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载"
date: 2025-10-16
description: "Musicn是基于Node.js的音乐搜索下载工具，聚合咪咕、酷狗、网易云三大平台资源。支持Web播放界面、命令行操作、批量下载、歌词获取等功能。Docker一键部署，轻松构建个人音乐库。无论音乐爱好者还是收藏家都能畅享海量音乐内容。"
canonical: https://www.passnat.com/posts/musicn-nas-online-music-player-aggregator
---

## 项目介绍

Musicn 是一款功能强大的音乐搜索、播放及下载工具，基于 Node.js 开发的命令行应用。它聚合了咪咕、酷狗、网易云三大音乐平台的资源，让你可以在一个平台上畅享海量音乐内容。不仅支持命令行操作，还提供了友好的 Web 界面，支持音乐搜索、在线播放、批量下载、歌词获取等功能。无论是音乐爱好者还是收藏家，都能通过 Musicn 轻松构建属于自己的音乐库。

项目地址：[点我跳转](https://github.com/zonemeen/musicn)

## 部署安装

如需在外网也能访问你的音乐库，建议配置内网穿透服务。你可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能在任何地方都能远程访问你的音乐播放器。具体的配置步骤可以 [参考这份教程](https://www.passnat.com/posts/p)，包含了全平台的详细上手指南。

### 最快速的部署方式 - Docker 单命令启动

打开你的 NAS 设备或服务器终端，执行以下命令即可一键启动 Musicn 的 Web 播放界面：

```bash
docker run -d --name=musicn --restart=unless-stopped -v ${PWD}/musicn:/data -p 7478:7478 ghcr.io/wy580477/musicn-container:latest msc -q
```

这条命令会：

创建一个名为 'musicn' 的容器

启用自动重启功能（服务器重启后会自动启动）

将下载的音乐文件保存到当前目录下的 'musicn' 文件夹

映射 7478 端口供 Web 访问

**如果 7478 端口已被占用**，可以修改命令中的第一个 '7478' 为其他端口号（比如 8888），修改后的命令如下：

```bash
docker run -d --name=musicn --restart=unless-stopped -v ${PWD}/musicn:/data -p 8888:7478 ghcr.io/wy580477/musicn-container:latest msc -q
```

这样容器内部仍然使用 7478 端口，但你可以通过宿主机的 8888 端口访问。

### 容器启动完成

执行上述命令后，容器会自动启动。你可以通过浏览器访问：

```
http://你的服务器IP:7478
```

打开后就能看到 Musicn 的 Web 界面，可以直接搜索歌曲、在线播放和下载音乐。

### 使用容器内的命令行工具

如果你想通过命令行方式搜索和下载音乐，可以执行：

```bash
docker exec -it musicn msc 周杰伦
```

为了方便使用，可以设置命令别名（仅限 Linux/Mac）：

```
echo "alias msc='docker exec -it musicn msc'" >> ~/.bashrc
# 重新登陆 shell 后生效
source ~/.bashrc
```

之后就可以直接使用 'msc' 命令了。

### 其他常用操作

**查看命令帮助：**

```bash
docker exec -it musicn msc -h
```

**指定子目录作为下载位置：**

```bash
docker exec -it musicn msc 周杰伦 -p ./test
```

**升级容器版本：**

```bash
docker container rm musicn --force && docker pull ghcr.io/wy580477/musicn-container:latest
# 然后重新执行安装命令
```

### 可选：Docker-Compose 部署方式

如果你更习惯使用 Docker-Compose，可以创建一个 'docker-compose.yml' 文件：

```yaml
version: '3.4'
services:
  musicn:
    image: ghcr.io/wy580477/musicn-container:latest
    container_name: musicn
    restart: unless-stopped
    entrypoint: ["/sbin/tini", "--", "msc", "-q"]
    ports:
      - "7478:7478"
    volumes:
      - ./musicn:/data
```

然后在同一目录下执行：

```
docker-compose up -d
```

## 项目亮点

✨ 多平台聚合 - 集咪咕、酷狗、网易云三大音乐平台于一身，资源丰富

🎵 在线播放 - 提供友好的 Web 界面，无需下载即可在线播放

💾 批量下载 - 支持单曲下载、歌单下载，还能下载歌词

📱 二维码访问 - 命令行模式支持生成二维码，手机扫码即可访问

🚀 轻量部署 - 容器化部署，一条命令即可启动，无需复杂配置

本篇教程结束

---

原文：<https://www.passnat.com/posts/musicn-nas-online-music-player-aggregator>


## 相关文章

- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [小爱音箱无限音乐播放器XiaoMusic，Docker极简部署内网穿透远程访问](xiaomi-xiaomusic-docker-lan-remote-access.md)
