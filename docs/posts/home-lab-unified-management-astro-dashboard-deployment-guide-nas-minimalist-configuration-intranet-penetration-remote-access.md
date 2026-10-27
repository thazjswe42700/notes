---
title: "家庭实验室统一管理 Astro 仪表盘部署指南，NAS 极简配置内网穿透远程访问"
date: 2025-10-16
description: "Astro是一个开放式家庭实验室仪表盘，采用Go后端和现代前端技术构建。提供统一界面监控和访问NAS或家庭服务器上的各类应用。支持Docker一键部署，轻松管理多个服务，为运维爱好者和家庭网络管理员提供简洁美观的仪表盘体验。一次部署，全面掌控整个家庭实验室生态。"
canonical: https://www.passnat.com/posts/home-lab-unified-management-astro-dashboard-deployment-guide-nas-minimalist-configuration-intranet-penetration-remote-access
---

## 项目介绍

Astro 是一个开放式、可扩展的家庭实验室仪表盘，专为整合管理你所有的自建服务而设计。它采用 Go 语言后端和现代前端技术构建，提供了一个统一的界面来监控和访问你在 NAS 或家庭服务器上部署的各类应用。

无论你是运维爱好者还是家庭网络的管理员，Astro 都能帮助你轻松管理多个服务，提供简洁美观的仪表盘体验。一次部署，全面掌控你的整个家庭实验室生态。

项目地址：[点我跳转](https://github.com/igorsheg/astro)

## 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使不在家也能随时查看你的家庭实验室状态。具体的操作步骤可以参考 [内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)，轻松几步就能实现外网访问。

### 最快速的部署方式 - Docker 单命令启动

打开你的 NAS 设备或其他支持 Docker 的系统，执行以下命令即可一键启动 Astro：

```bash
docker run -d \
  -p 8088:8088 \
  -v /your/local/path/:/app/server/data \
  igorsheg/astro
```

参数说明：

-d：后台运行容器
-p 8088:8088：将容器内的 8088 端口映射到宿主机的 8088 端口（如果该端口已被占用，可改为其他端口，例如 -p 9000:8088）
-v /your/local/path/:/app/server/data：将本地数据目录挂载到容器内，用于持久化保存仪表盘配置和数据。请将 /your/local/path/ 替换为你实际想要存储数据的本地路径

端口配置说明：

如果 8088 端口在你的系统上已被其他应用占用，可以修改映射的宿主机端口。例如，如果想使用 9000 端口访问，命令改为：

```bash
docker run -d \
  -p 9000:8088 \
  -v /your/local/path/:/app/server/data \
  igorsheg/astro
```

然后通过 http://你的IP:9000 访问即可。容器内部始终使用 8088 端口，无需修改。

数据持久化：

-v 参数后的路径用于存储 Astro 的配置文件和数据库。请确保该目录存在，或者 Docker 会自动创建。建议在 NAS 上创建一个专门的文件夹来存放这些数据，这样即使容器重启，你的所有配置也不会丢失。

### 环境变量配置

Astro 的环境变量配置相对简单。默认情况下，以下配置已经内置：

HTTP\_ADDRESS：监听地址（默认值：0.0.0.0）- 无需修改
HTTP\_PORT：监听端口（默认值：8088）- 无需修改

这些环境变量已经在镜像中预设，无需额外配置。如果你有特殊需求，可以在启动命令中添加 -e 参数来覆盖，例如：

```bash
docker run -d \
  -p 8088:8088 \
  -e HTTP_PORT=9000 \
  -v /your/local/path/:/app/server/data \
  igorsheg/astro
```

### 访问你的 Astro 仪表盘

启动容器后，打开浏览器访问：

```
http://你的NAS_IP:8088
```

将 你的NAS\_IP 替换为你的 NAS 或服务器的实际 IP 地址。稍等几秒钟，Astro 的仪表盘就会加载完成，你可以开始配置和管理你的各项家庭服务了。

### 开机自动启动

如果你想让 Astro 在 NAS 重启后自动启动，可以在 Docker 启动命令中添加 --restart=always 参数：

```bash
docker run -d \
  --restart=always \
  -p 8088:8088 \
  -v /your/local/path/:/app/server/data \
  igorsheg/astro
```

这样，每次 NAS 启动时，Astro 容器都会自动运行。

到此，Astro 的部署就完成了。现在你拥有了一个强大的家庭实验室管理工具，可以在统一的仪表盘上查看和管理所有的自建服务。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/home-lab-unified-management-astro-dashboard-deployment-guide-nas-minimalist-configuration-intranet-penetration-remote-access>
