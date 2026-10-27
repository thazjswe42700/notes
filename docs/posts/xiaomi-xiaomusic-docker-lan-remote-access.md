---
title: "小爱音箱无限音乐播放器XiaoMusic，Docker极简部署内网穿透远程访问"
date: 2025-10-16
description: "XiaoMusic是基于Python和FastAPI的小爱音箱音乐播放解决方案，支持多种型号音箱，提供Web管理界面。通过与小米账户集成，让小爱音箱播放互联网海量音乐资源。支持Docker一键部署，包含语音命令播放、本地音乐管理等功能，无需占用NAS存储空间，适合音乐爱好者和NAS玩家使用。"
canonical: https://www.passnat.com/posts/xiaomi-xiaomusic-docker-lan-remote-access
---

## XiaoMusic：让小爱音箱成为你的无限音乐播放器

### 项目介绍

XiaoMusic 是一款革命性的小爱音箱音乐播放解决方案，它彻底解放了小爱音箱的音乐播放能力。这个项目基于 Python + FastAPI 框架构建，通过与小米账户的无缝集成，让你的小爱音箱可以播放互联网上的海量音乐资源。

项目支持多种小爱音箱型号（从入门款 L06A 到高端的 Xiaomi Sound Pro），并且提供了一个简洁易用的 Web 管理界面。无论你是想要随时随地用语音命令播放喜欢的歌曲，还是想要建立个人的音乐库，XiaoMusic 都能满足你的需求。最棒的是，所有的音乐资源都由第三方提供，不占用你的 NAS 或服务器的存储空间。

项目地址：[点我跳转](https://github.com/hanxi/xiaomusic)

### 部署安装

如果你需要在外网也能访问 XiaoMusic，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com) 服务，先注册账号备用。详细的配置指南可以 [点我查看](https://www.passnat.com/posts/p)。

#### Docker 单命令快速启动

打开你的 NAS 或其他 Docker 环境，执行以下命令即可一键启动 XiaoMusic：

```bash
docker run -p 58090:8090 -e XIAOMUSIC_PUBLIC_PORT=58090 -v /xiaomusic_music:/app/music -v /xiaomusic_conf:/app/conf hanxi/xiaomusic
```

国内用户 可使用国内镜像源加速下载：

```bash
docker run -p 58090:8090 -e XIAOMUSIC_PUBLIC_PORT=58090 -v /xiaomusic_music:/app/music -v /xiaomusic_conf:/app/conf docker.hanxi.cc/hanxi/xiaomusic
```

启动前，请先创建必要的目录：

```
mkdir -p /xiaomusic_music /xiaomusic_conf
```

#### Docker Compose 配置方式

如果你更习惯使用 Docker Compose，可以创建以下配置文件：

```yaml
services:
  xiaomusic:
    image: hanxi/xiaomusic
    container_name: xiaomusic
    restart: unless-stopped
    ports:
      - 58090:8090
    environment:
      XIAOMUSIC_PUBLIC_PORT: 58090
    volumes:
      - /xiaomusic_music:/app/music
      - /xiaomusic_conf:/app/conf
```

国内用户 配置：

```yaml
services:
  xiaomusic:
    image: docker.hanxi.cc/hanxi/xiaomusic
    container_name: xiaomusic
    restart: unless-stopped
    ports:
      - 58090:8090
    environment:
      XIAOMUSIC_PUBLIC_PORT: 58090
    volumes:
      - /xiaomusic_music:/app/music
      - /xiaomusic_conf:/app/conf
```

保存配置文件后，在同目录执行 docker-compose up -d 即可启动。

### 配置说明

#### 端口映射

58090 是 NAS 宿主机上的访问端口，如果该端口已被占用，可修改为其他端口（如 58091、58092 等）
8090 是容器内部端口，**请勿修改**
启动后，访问地址为：http://NAS\_IP:58090

#### 存储目录

/xiaomusic\_music：用于存放本地音乐文件和下载的歌曲
/xiaomusic\_conf：用于存放配置文件和用户数据
这两个目录需要在宿主机上提前创建，建议分别映射到 NAS 的不同位置，便于管理和备份

#### 环境变量设置

必须设置：
XIAOMUSIC\_PUBLIC\_PORT：对应外部访问端口（与上面的 58090 保持一致）

可选设置：
其他配置参数可在启动后通过 Web 界面进行设置，无需在这里配置

### 初始化配置

启动容器后，打开浏览器访问 http://NAS\_IP:58090，你会看到 XiaoMusic 的管理界面。

首次使用时，**必须** 在设置页面输入你的小米账号和密码，系统才能获取到你的小爱音箱设备列表。完成后，你就可以通过以下方式使用：

Web 界面：直接在浏览器中搜索和播放音乐
语音命令：对小爱音箱说'播放歌曲 [歌名]'即可自动下载并播放
本地音乐：将 MP3、FLAC 等格式的音乐文件放入 /xiaomusic\_music 目录，通过'播放列表'功能访问

### 总结

XiaoMusic 让你的小爱音箱从一个简单的语音助手升级为功能强大的音乐播放中心。通过简单的 Docker 部署，你可以在几分钟内搭建属于自己的个人音乐服务。无论你是音乐爱好者还是 NAS 玩家，这个项目都值得一试。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/xiaomi-xiaomusic-docker-lan-remote-access>


## 相关文章

- [聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载](musicn-nas-online-music-player-aggregator.md)
- [SQLite Web数据库管理工具极简部署指南 NAS内网穿透在线查询编辑](sqlite-web-database-management-tool-minimal-deployment-guide-nas-intranet-penetration-online-query-editing.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
