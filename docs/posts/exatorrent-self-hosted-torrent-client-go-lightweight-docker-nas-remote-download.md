---
title: "Exatorrent 自托管种子客户端，Go 开发轻量级 Docker 部署，NAS 内网穿透远程下载"
date: 2025-10-16
description: "Exatorrent是一款用Go语言编写的自托管BitTorrent客户端，提供优雅的Web界面和零外部依赖的单一二进制设计。支持浏览器流媒体播放、VLC兼容、单用户和多用户模式，资源占用少，可轻松通过Docker部署在NAS或远程服务器上实现高效种子下载管理。"
canonical: https://www.passnat.com/posts/exatorrent-self-hosted-torrent-client-go-lightweight-docker-nas-remote-download
---

## Exatorrent：优雅的自托管种子下载客户端

#### 项目介绍

Exatorrent 是一款用 Go 语言编写的优雅自托管 BitTorrent 客户端。它是一个功能完整、开箱即用的解决方案，采用单一静态链接二进制文件设计，零外部依赖。项目提供了一个由 Svelte 和 TypeScript 构建的美观响应式网页客户端，让你可以在浏览器中轻松管理和下载种子文件。

无论是在本地运行还是部署在远程服务器上，Exatorrent 都表现得轻量级且资源占用少。它支持单用户和多用户模式，支持直接在浏览器中流媒体播放、VLC、mpv 等播放器兼容，让你随时随地享受无缝的下载体验。

项目地址：[点我跳转](https://github.com/varbhat/exatorrent)

#### 部署安装

如果你需要在外网也能访问项目，建议配置内网穿透服务。可以选择 [帕斯内网穿透](https://passnat.com) 来实现，先注册好账号以备后用。详细的操作指南可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手指南。

##### 最快速的部署方式 - Docker 单命令启动

打开你的飞牛 NAS 或其他 NAS 设备，使用以下 Docker 命令一键启动 Exatorrent：

```bash
docker run -p 5000:5000 -p 42069:42069 -v /path/to/directory:/exa/exadir ghcr.io/varbhat/exatorrent:latest
```

参数说明：

-p 5000:5000 - Web 界面访问端口（容器内部使用 5000 端口）

-p 42069:42069 - BitTorrent 协议通信端口（容器内部使用 42069 端口）

-v /path/to/directory:/exa/exadir - 将你的本地目录挂载到容器内，用于存储下载的文件

如果端口冲突怎么办？

如果你的 NAS 上 5000 或 42069 端口已被占用，可以修改前面的端口号。例如，如果想使用 6000 端口访问 Web 界面，改为：

```bash
docker run -p 6000:5000 -p 42069:42069 -v /path/to/directory:/exa/exadir ghcr.io/varbhat/exatorrent:latest
```

存储位置配置：

请确保 /path/to/directory 替换为你 NAS 上实际的存储目录路径。这个目录将用于保存所有下载的种子文件。建议提前在 NAS 上创建一个专门的文件夹（比如 /mnt/nas/exatorrent），然后将其路径填入上述命令。

环境变量设置：

Exatorrent 开箱即用，无需额外的环境变量配置。所有功能都可以通过 Web 界面进行管理和配置。

首次访问：

容器启动成功后，打开浏览器访问 http://你的NAS\_IP:5000（如果修改了端口则使用修改后的端口号）

系统会为你创建一个默认管理员账户：

用户名：adminuser

密码：adminpassword

首次登录后，强烈建议你立即修改密码以保护账户安全。

到这里部署就完成了！

现在你可以在 Web 界面中添加种子文件、管理下载、控制上传速度，甚至直接在浏览器中播放媒体文件。Exatorrent 会在你关闭应用后继续保留下载记录和配置，下次启动时会自动恢复。

---

原文：<https://www.passnat.com/posts/exatorrent-self-hosted-torrent-client-go-lightweight-docker-nas-remote-download>
