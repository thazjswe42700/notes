---
title: "自托管音效工具Noisedash一键部署，Docker快速安装配置指南与内网穿透远程访问方案"
date: 2025-10-16
description: "Noisedash是自托管网页工具，支持生成定制环境音效、白噪音助眠、咖啡厅背景音。集成音频处理、滤波器、LFO等功能，可保存导出音景配置、录制分享。提供多用户管理、移动端友好设计，支持Docker快速部署，适合工作学习放松。"
canonical: https://www.passnat.com/posts/noisedash-self-hosted-docker-deployment-guide-intranet-penetration
---

## 项目介绍

Noisedash 是一款自托管的网页工具，专为生成和定制环境音效而设计。无论你是需要白噪音助眠、咖啡厅背景音来提高工作效率，还是想要创建独特的音景体验，Noisedash 都能满足你的需求。

这个项目基于现代Web技术栈构建，集成了强大的音频处理能力。你可以生成各种环境音效，上传自己的音频样本（如雨声、风声、雷声等），并通过滤波器、LFO和各种音效处理工具进行精细调整。最棒的是，你创建的所有'音景配置'都可以保存、导出分享给朋友，甚至可以录制下来供其他地方使用。项目还提供了多用户管理功能和移动端友好的界面设计。

项目地址： [点我跳转](https://github.com/kaythomas0/noisedash)

## 部署安装

如果你想在公网环境下随时随地访问你的 Noisedash 实例，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的部署方案可以参考 [这份全平台操作指南](https://www.passnat.com/posts/p)，帮助你快速上手。

### Docker Compose 部署（推荐）

这是最简单快速的部署方式，适合所有技术水平的用户。

**第一步：准备配置文件**

复制以下 docker-compose 配置文件到你的服务器或 NAS 设备：

```yaml
version: '3'

services:
  noisedash:
    image: noisedash/noisedash:latest
    container_name: noisedash
    ports:
      - '1432:1432'
    volumes:
      - db:/var/noisedash/db
      - samples:/var/noisedash/samples
      - ./config/default.json:/var/noisedash/config/default.json

volumes:
  db:
  samples:
```

**第二步：创建配置文件目录**

在 docker-compose.yml 文件的同级目录下，创建一个名为 `config` 的文件夹，然后在该文件夹内创建 `default.json` 配置文件：

```
{
  'Server': {
    'listeningPort': 1432,
    'sessionFileStorePath': 'sessions',
    'sampleUploadPath': 'samples',
    'maxSampleSize': 10737418240,
    'logFile': 'log/noisedash.log',
    'tls': false,
    'tlsKey': 'certs/key.pem',
    'tlsCert': 'certs/cert.pem'
  }
}
```

**第三步：启动容器**

打开你的 NAS 设备（飞牛 NAS、群晖、威联通等）或服务器的终端，进入到 docker-compose.yml 所在的目录，执行以下命令：

```
docker-compose up -d
```

容器会在后台运行，并自动启动。

**第四步：访问应用**

等待容器完全启动后（通常需要 10-30 秒），在浏览器中访问：

```
http://你的设备IP:1432
```

首次访问时需要注册一个用户账号，之后就可以开始创建你的专属音景了。

### 配置说明

如果你需要调整配置，可以编辑 `config/default.json` 文件中的参数：

listeningPort: 应用监听的端口号，默认为 1432。如果该端口被占用，可以修改为其他端口（如 8080），同时也要更新 docker-compose.yml 中的 `ports` 配置

maxSampleSize: 单个音频样本的最大上传大小（字节），默认为 10GB，可根据你的存储空间调整

tls: 是否启用 HTTPS，默认为 false。如果你使用了 Nginx 等反向代理，建议保持为 false

tlsKey 和 tlsCert: SSL 证书路径，仅在 tls 为 true 时需要配置

### 端口冲突处理

如果系统提示 1432 端口已被占用，你可以修改 docker-compose.yml 中的端口映射：

```
ports:
  - '8080:1432'  # 将外部访问端口改为 8080，容器内部仍为 1432
```

修改后重新执行 `docker-compose up -d` 命令即可。

### 数据持久化

项目会自动在以下位置存储数据：

db: 数据库文件存储位置

samples: 用户上传的音频样本存储位置

这些数据都已通过 Docker 卷进行持久化管理，即使容器重启也不会丢失。

到此，Noisedash 的部署就已经完成了！你现在可以尽情创建属于自己的环境音效，享受高质量的音景体验。无论是工作、学习还是放松，Noisedash 都会是你的最佳伴侣。

本篇教程结束

---

原文：<https://www.passnat.com/posts/noisedash-self-hosted-docker-deployment-guide-intranet-penetration>


## 相关文章

- [自托管书签管理工具Servas无公网部署指南 NAS极简配置内网穿透远程访问](servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access.md)
- [个人应用书签仪表板DashMachine Docker部署教程 自托管NAS远程访问内网穿透配置方案](personal-bookmark-dashboard-dashmachine-docker-deploy-nas-self-hosting-remote-access-intranet-penetration.md)
- [极简网页仪表盘SimpleDash，本地快速部署，内网穿透远程访问，YAML配置自定义链接管理](simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config.md)
- [自托管音乐下载器PlaylistDL，Spotify和YouTube歌曲一键转MP3，内网穿透远程访问](playlist-dl-spotify-youtube-mp3-download.md)
- [自托管私密笔记应用Notea，Docker极简部署，云存储灵活配置，内网穿透远程访问](self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access.md)
