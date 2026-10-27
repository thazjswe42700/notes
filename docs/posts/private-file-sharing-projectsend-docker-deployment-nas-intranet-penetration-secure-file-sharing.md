---
title: "私密文件分享平台 ProjectSend Docker 部署指南，NAS 内网穿透安全共享文件"
date: 2025-10-16
description: "探索ProjectSend开源文件分享应用：一款免费、安全的私密文件传输工具，支持多客户端管理、文件过期自动删除、详细日志记录。本文提供Docker快速部署指南和内网穿透配置方案，帮助自由职业者、小型企业和团队实现高效的文件共享与协作。"
canonical: https://www.passnat.com/posts/private-file-sharing-projectsend-docker-deployment-nas-intranet-penetration-secure-file-sharing
---

## 项目介绍

ProjectSend 是一款免费、开源的私密文件分享应用程序，专为客户端文件传输而设计。它提供了一个安全、简洁的方式让你与客户端或团队成员共享文件。

该应用支持创建多个客户端账户，为不同的客户端分配独立的用户名和密码。你可以灵活地将上传的文件分配给特定客户端或客户端组。此外，ProjectSend 还提供了文件过期自动删除、用户通知、详细的操作日志记录、客户端上传权限配置、多主题切换、多语言支持等丰富功能。无论你是自由职业者、小型企业还是需要安全共享文件的团队，ProjectSend 都能满足你的需求。

项目地址：[点我跳转](https://github.com/projectsend/projectsend)

## 部署安装

如果你需要在家外也能访问这个应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的配置教程可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

### Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，使用以下命令快速启动 ProjectSend：

```bash
docker run -d \
  --name projectsend \
  -p 80:80 \
  -p 443:443 \
  -v projectsend_data:/var/www/projectsend \
  -v projectsend_db:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=your_password \
  projectsend/projectsend:latest
```

或者，如果你的 NAS 提供了可视化的 Docker 管理界面（如飞牛 NAS），按照以下步骤操作：

添加镜像：点击'本地镜像' → '添加镜像' → '从 URL 添加'

输入镜像地址：'projectsend/projectsend:latest'

等待下载：耐心等待镜像下载完成

### 容器配置

端口映射设置

HTTP 访问端口：'80'（容器内端口）→ 可映射到主机的任意端口，如 '8080'

HTTPS 访问端口：'443'（容器内端口）→ 可映射到主机的任意端口，如 '8443'

如果这些端口在你的主机上已被占用，可以修改映射的端口号。重点是容器内的端口保持不变。

存储卷配置

ProjectSend 需要两个持久化存储目录：

'projectsend\_data'：存储应用程序文件和用户上传的文件

'projectsend\_db'：存储 MySQL 数据库文件

建议在 NAS 上创建相应的文件夹，然后将这些路径映射到容器内。

环境变量设置

必须设置：

'MYSQL\_ROOT\_PASSWORD'：MySQL 数据库 root 用户密码（请设置一个安全的密码）

可选设置：

'MYSQL\_DATABASE'：初始化数据库名称（默认：projectsend）

'MYSQL\_USER'：MySQL 普通用户名（默认：projectsend）

'MYSQL\_PASSWORD'：MySQL 普通用户密码

其他配置项可保持默认，直接启动容器即可。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:80
```

或

```
https://你的NAS_IP:443
```

首次访问会进入安装向导，按照提示完成初始配置即可。设置好管理员账户后，ProjectSend 就可以正常使用了。

## 总结

通过 Docker 部署 ProjectSend，你可以在几分钟内建立一个私密的文件分享平台。无论是与客户端共享项目文件，还是团队内部协作传输文件，ProjectSend 都能提供安全、便捷的解决方案。配合内网穿透服务，即使身在外地也能随时随地访问和管理你的文件。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/private-file-sharing-projectsend-docker-deployment-nas-intranet-penetration-secure-file-sharing>


## 相关文章

- [Plik私有文件分享系统NAS部署指南，Docker快速搭建临时上传平台](plik-nas-docker-deployment-guide.md)
- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
- [浏览器P2P文件传输工具FastSend，WebRTC加密传输，NAS极简Docker部署，局域网极速秒传无限制](fastend-p2p-file-transfer-webrtc-nas-docker.md)
- [Seafile 私有文件库：NAS Docker 部署 + 内网穿透](seafile-nas-docker-private-file-library-intranet-penetration.md)
- [PicoShare 文件分享：NAS Docker 部署 + 内网穿透](picoshare-nas-docker-file-sharing-upload-link-intranet-penetration.md)
