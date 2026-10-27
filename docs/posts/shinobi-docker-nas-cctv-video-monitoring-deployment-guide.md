---
title: "开源监控系统Shinobi Docker部署指南，NAS一键启动CCTV视频监控解决方案"
date: 2025-10-16
description: "开源CCTV监控解决方案Shinobi，采用Node.js开发，支持多账户、WebSocket流传输和MP4保存。兼容ONVIF、RTSP、MJPEG等多种摄像头协议，适用于家庭和企业安防。开箱即用，内置数据库和视频管理功能，轻松部署家庭监控系统。"
canonical: https://www.passnat.com/posts/shinobi-docker-nas-cctv-video-monitoring-deployment-guide
---

## 项目介绍

Shinobi 是一款开源的CCTV监控解决方案，采用 Node.js 开发，具有多账户系统、WebSocket 流传输和直接MP4保存功能。它支持IP摄像头和本地摄像头的录制，为家庭和小型企业提供了一个强大而灵活的视频监控系统。

无论您是想要监控家中安全、办公室动态，还是建立小型安防系统，Shinobi 都能轻松胜任。它支持多种摄像头协议（ONVIF、RTSP、MJPEG等），几乎兼容市面上大多数的IP摄像头。系统内置数据库和视频存储管理，开箱即用，无需复杂配置。

项目地址：[点我跳转](https://gitlab.com/Shinobi-Systems/Shinobi)

## 部署安装

如果您想在外网也能访问家中的监控系统，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号并配置，具体操作方法可参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让您随时随地都能查看监控画面。

### 最快速部署方案 - Docker 单命令启动

打开飞牛 NAS 或其他 NAS 设备的 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
registry.gitlab.com/shinobi-systems/shinobi:dev
```

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置详解

#### 基本信息设置

勾选 **开机自动启动**，然后进入下一步进行端口设置。

#### 端口映射配置

Shinobi 需要映射以下端口：

8080（Web界面访问端口）- 容器内部端口 8080，这是主要的访问入口

21（FTP服务端口，可选）- 用于文件传输功能

如果您的 NAS 上 8080 端口已被占用，可以修改宿主机端口为其他未使用的端口（如 8081、8082 等），但容器内部端口保持 8080 不变。例如可设置为 *8081:8080*。

#### 存储卷配置

Shinobi 需要两个持久化存储目录：

数据库存储 - 映射到容器内的 */var/lib/mysql*

建议在 NAS 上创建文件夹：*/mnt/data/ShinobiDatabase*

用于存储数据库文件

应用数据存储 - 映射到容器内的 */home/Shinobi*

建议在 NAS 上创建文件夹：*/mnt/data/Shinobi*

用于存储视频录像、配置文件、插件等数据

在 NAS 的存储卷配置中，分别将这两个文件夹映射到对应的容器路径。

#### 环境变量配置

Shinobi 的环境变量已禁用，配置通过 *conf.json* 文件进行管理。

如果需要修改配置，可以在 NAS 上找到映射的 */mnt/data/Shinobi* 文件夹

编辑其中的 *conf.json* 文件进行自定义配置

如果该文件不存在，Shinobi 会在容器启动时自动创建默认配置

#### SSL 证书配置（可选）

如需启用 HTTPS，需要在 */mnt/data/Shinobi/config/ssl* 目录下放置：

server.key - SSL私钥文件

server.cert - SSL证书文件

如果启用了 SSL 但证书文件不存在，系统会自动生成自签名证书。

### 启动完成

容器创建成功后，在浏览器中访问：

```
http://NAS_IP:8080
```

（如果修改了端口，请将 8080 替换为您设置的实际端口号）

此时您应该能看到 Shinobi 的登录界面，可以开始添加摄像头进行监控了。

### 常用操作命令

如需进一步管理容器，可使用以下命令：

查看容器日志：*docker logs Shinobi*

进入容器命令行：*docker exec -it Shinobi /bin/bash*

停止容器：*docker stop Shinobi*

重启容器：*docker restart Shinobi*

通过以上步骤，您已经成功部署了 Shinobi 监控系统。现在可以添加您的摄像头，开始享受专业级的视频监控体验了！

---

原文：<https://www.passnat.com/posts/shinobi-docker-nas-cctv-video-monitoring-deployment-guide>


## 相关文章

- [NodeBB现代论坛社区系统Docker部署指南，NAS一键安装配置实时互动讨论社区平台](nodebb-docker-nas-deployment-guide.md)
- [青蛇探针服务器监控系统，Docker一键部署实时可视化面板，支持远程SSH管理](qing-she-tan-zhen-fu-wu-qi-jian-kong-xi-tong-docker-yi-jian-bu-shu-shi-shi-ke-shi-hua-mian-ban-zhi-chi-yuan-cheng-ssh-guan-li.md)
- [开源私有视频会议系统Nettu Meet，Docker一键部署，支持屏幕共享和内网穿透远程访问](open-source-nettu-meet-video-conference-docker-deployment.md)
- [聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载](musicn-nas-online-music-player-aggregator.md)
- [沉浸式虚拟办公平台 SkyOffice，NAS Docker 极简部署，远程团队协作新体验](skyoffice-nas-docker-remote-team-collaboration.md)
