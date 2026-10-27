---
title: "FileZilla Docker容器部署指南，无需客户端浏览器即可远程访问，NAS极简配置内网穿透实现外网管理文件"
date: 2025-10-16
description: "Docker部署FileZilla容器完整指南。FileZilla是功能强大的跨平台文件传输工具，支持FTP、SFTP和FTPS协议。本容器开箱即用，无需安装客户端，通过浏览器或VNC即可轻松访问使用。支持环境变量配置、网页认证、深色模式等多种功能特性，适合NAS和服务器部署。"
canonical: https://www.passnat.com/posts/filezilla-docker-nas-remote-access-guide
---

## 项目介绍

FileZilla 是一款功能强大的跨平台图形化文件传输工具，支持 FTP、SFTP 和 FTPS 协议。这个 Docker 容器项目将 FileZilla 打包成一个开箱即用的解决方案，让用户无需安装任何客户端，仅通过现代网络浏览器或 VNC 客户端即可轻松访问和使用。无论你是在家中的 NAS 设备上运行，还是在服务器上部署，都能快速上手，享受便捷的文件管理体验。

项目地址：[点我跳转](https://github.com/jlesage/docker-filezilla)

## 部署安装

如果你需要在外网也能访问本地部署的 FileZilla，建议配置内网穿透功能。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，然后参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p) 进行配置，这样就能随时随地远程管理你的文件了。

### Docker 单命令快速启动

打开你的 NAS 设备或 Docker 宿主机，执行以下命令即可一键启动 FileZilla 容器：

```bash
docker run -d \
    --name=filezilla \
    -p 5800:5800 \
    -v /docker/appdata/filezilla:/config:rw \
    -v /home/user:/storage:rw \
    jlesage/filezilla
```

启动完成后，在浏览器中访问 `http://你的主机IP:5800` 即可打开 FileZilla 的网页界面。

### 参数说明

-p 5800:5800：将容器内的 5800 端口映射到主机，用于网页访问。如果端口冲突，可以改为其他端口，例如 `-p 8080:5800`

-v /docker/appdata/filezilla:/config:rw：存储 FileZilla 的配置文件和应用数据，建议在宿主机上创建 `/docker/appdata/filezilla` 文件夹

-v /home/user:/storage:rw：将宿主机的文件目录映射到容器内，这样 FileZilla 就能访问你的本地文件

### 环境变量配置

必须设置：

无（所有变量都是可选的）

推荐设置：

TZ：时区设置，例如 `Asia/Shanghai`（默认：`Etc/UTC`）

LANG：语言设置，例如 `zh\_CN.UTF-8`（默认：`en\_US.UTF-8`）

DISPLAY\_WIDTH：窗口宽度，默认 `1920`

DISPLAY\_HEIGHT：窗口高度，默认 `1080`

DARK\_MODE：深色模式，设置为 `1` 启用（默认：`0`）

WEB\_FILE\_MANAGER：启用网页文件管理器，设置为 `1` 启用（默认：`0`）

其他可选变量：

USER\_ID：应用运行的用户 ID（默认：`1000`）

GROUP\_ID：应用运行的组 ID（默认：`1000`）

VNC\_PASSWORD：VNC 连接密码

WEB\_AUTHENTICATION：启用网页认证，设置为 `1` 启用（默认：`0`）

WEB\_AUTHENTICATION\_USERNAME：网页认证用户名

WEB\_AUTHENTICATION\_PASSWORD：网页认证密码

如果需要添加环境变量，在 Docker 命令中使用 `-e 变量名=值` 的格式，例如：

```bash
docker run -d \
    --name=filezilla \
    -p 5800:5800 \
    -e TZ=Asia/Shanghai \
    -e LANG=zh_CN.UTF-8 \
    -e DARK_MODE=1 \
    -e WEB_FILE_MANAGER=1 \
    -v /docker/appdata/filezilla:/config:rw \
    -v /home/user:/storage:rw \
    jlesage/filezilla
```

### 访问应用

容器启动成功后，打开浏览器访问 `http://你的主机IP:5800`，即可看到 FileZilla 的完整网页界面。如果启用了网页认证，需要先输入用户名和密码才能进入。

本篇教程结束

---

原文：<https://www.passnat.com/posts/filezilla-docker-nas-remote-access-guide>


## 相关文章

- [私人云盘Filestash部署完全指南，NAS一键Docker启动，支持多云存储聚合管理](private-cloud-storage-filestash-deployment-guide-nas-docker-one-click-multi-cloud-storage-aggregation.md)
- [多协议文件传输服务 SFTPGo，NAS 极简部署支持 SFTP FTP WebDAV，内网穿透远程访问](sftp-go-nas-sftp-ftp-webdav-remote-access.md)
- [Docker容器运行微信应用，Web远程访问与VNC连接，NAS一键部署搭建家庭微信服务器](docker-weixin-nas-vnc-remote-access.md)
- [青蛇探针服务器监控系统，Docker一键部署实时可视化面板，支持远程SSH管理](qing-she-tan-zhen-fu-wu-qi-jian-kong-xi-tong-docker-yi-jian-bu-shu-shi-shi-ke-shi-hua-mian-ban-zhi-chi-yuan-cheng-ssh-guan-li.md)
- [自动化文件处理系统FileFlows，NAS部署媒体转码工具，内网穿透远程访问管理](fileflows-nas-media-transcoding-intranet-penetration.md)
