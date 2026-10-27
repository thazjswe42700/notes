---
title: "多存储聚合管理工具AList，支持30+云盘本地存储，Docker极简部署NAS内网穿透在线预览"
date: 2025-10-16
description: "AList是功能强大的文件列表程序，支持30+种存储源聚合管理，包括阿里云盘、OneDrive、谷歌云盘等。提供文件预览、视频音频播放、Office文档预览、权限保护等功能，采用Gin和Solidjs技术栈打造，支持Docker快速部署，是个人和团队文件管理的理想解决方案。"
canonical: https://www.passnat.com/posts/alist-multi-cloud-storage-aggregation-tool-30-plus-cloud-drives-local-storage-docker-simple-deployment-nas-intranet-penetration-online-preview
---

## 项目介绍

AList 是一款功能强大的文件列表程序，采用 Gin 和 Solidjs 技术栈打造，支持多达 30+ 种存储源的聚合管理。无论你是使用阿里云盘、OneDrive、谷歌云盘，还是本地存储、FTP、SMB 等，AList 都能将它们统一整合到一个简洁易用的网页界面中。

这款工具不仅支持文件预览（包括 PDF、Markdown、代码、纯文本等），还能在线播放视频和音频文件，支持字幕和歌词显示。Office 文档预览、图库模式浏览、文件下载加速、离线下载等功能应有尽有。AList 提供了完整的权限保护机制，支持密码保护和身份验证，让你的文件分享既方便又安全。

项目地址：[点我跳转](https://github.com/Xhofe/alist)

## 部署安装

如果你想在家外也能访问这个项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台使用指南可以 [点我查看](https://www.passnat.com/posts/p)。

### 最快速部署方式

AList 提供了开箱即用的 Docker 部署方案，只需一条命令即可启动：

```bash
docker run -d \
  --name alist \
  --restart always \
  -p 5244:5244 \
  -p 5245:5245 \
  -v /etc/alist:/opt/alist/data \
  -e PUID=0 \
  -e PGID=0 \
  -e UMASK=022 \
  -e TZ=UTC \
  xhofe/alist:latest
```

### 或使用 Docker Compose 部署

如果你更喜欢使用 Docker Compose，可以复制下列配置文件：

```yaml
version: '3.3'
services:
  alist:
    restart: always
    volumes:
      - '/etc/alist:/opt/alist/data'
    ports:
      - '5244:5244'
      - '5245:5245'
    environment:
      - PUID=0
      - PGID=0
      - UMASK=022
      - TZ=UTC
    container_name: alist
    image: 'xhofe/alist:latest'
```

将上述内容保存为 `docker-compose.yml` 文件，然后在该文件所在目录执行：

```
docker-compose up -d
```

### NAS 设备部署步骤

打开你的飞牛 NAS 或其他 NAS 设备管理界面，进入到容器管理功能。

点击'本地镜像'→'添加镜像'→'从 URL 添加'，在镜像地址栏填入：

```
xhofe/alist:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

勾选'开机自动启动'选项，确保系统重启后容器能自动运行。

**端口配置说明**

AList 需要映射两个端口：

5244：Web 界面访问端口（主要使用）

5245：WebDAV 服务端口（可选）

如果你的系统中这些端口已被占用，可以修改左侧的端口号（冒号前的数字），但容器内的端口（冒号后的数字）需要保持不变。例如，如果 5244 被占用，可改为 `8080:5244`。

**存储位置配置**

AList 的所有数据（配置文件、用户信息等）都存储在 `/etc/alist` 目录中。建议在 NAS 上创建一个专用文件夹用于数据持久化，这样容器删除后数据不会丢失。

**环境变量设置**

以下环境变量可根据需要调整：

环境变量 | 说明 | 默认值 | 必需

PUID | 运行进程的用户 ID | 0 | 否

PGID | 运行进程的组 ID | 0 | 否

UMASK | 文件权限掩码 | 022 | 否

TZ | 时区设置 | UTC | 否

一般情况下，保持默认值即可，无需特别调整。如果你需要修改时区，可将 TZ 改为你所在的时区（如 `Asia/Shanghai`）。

### 访问应用

部署完成后，在浏览器中访问：

```
http://你的NAS地址:5244
```

系统会显示 AList 的登录界面。首次登录时，默认用户名为 `admin`，密码也是 `admin`，建议登录后立即修改密码以保护你的账户安全。

登录后，你就可以开始添加各种存储源，并通过统一的界面管理和预览所有文件了。

## 总结

AList 是一个功能完整、部署简便的文件聚合管理工具。通过简单的 Docker 命令或 Compose 配置，你就能在几分钟内搭建起一个功能强大的文件服务。无论是个人文件管理、团队协作还是内容分享，AList 都能胜任。赶快部署体验一下吧！

---

原文：<https://www.passnat.com/posts/alist-multi-cloud-storage-aggregation-tool-30-plus-cloud-drives-local-storage-docker-simple-deployment-nas-intranet-penetration-online-preview>
