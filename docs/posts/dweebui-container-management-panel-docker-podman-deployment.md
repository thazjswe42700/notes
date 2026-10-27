---
title: "DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维"
date: 2025-10-16
description: "DweebUI是功能强大的开源容器管理面板，支持Docker和Podman，提供直观Web界面轻松管理容器、镜像、网络和存储卷。内置多用户权限、实时仪表板、日志查看、应用模板库等丰富功能，完美适配Windows、Linux、MacOS和移动设备，是家庭NAS和小型服务器的理想选择。"
canonical: https://www.passnat.com/posts/dweebui-container-management-panel-docker-podman-deployment
---

DweebUI - 容器管理的全能卫士

## 项目介绍

DweebUI 是一款功能强大的开源容器管理面板，专为 Docker 和 Podman 用户量身打造。它提供了一个简洁直观的 Web 界面，让你能够轻松管理和监控所有的容器、镜像、网络和存储卷。无论你是在 Windows、Linux 还是 MacOS 上运行，DweebUI 都能完美适配，为你的容器管理工作带来前所未有的便利。

该项目采用现代化的技术栈构建，支持多用户权限系统、实时仪表板、容器日志查看、一键启停等丰富功能。界面设计简洁优雅，内置亮色/深色主题切换，完全适配移动设备。最重要的是，DweebUI 内置了应用模板库，与 Portainer 模板兼容，让你一键部署常用应用，开箱即用。

项目地址：[点我跳转](https://github.com/lllllllillllllillll/DweebUI)

## 部署安装

如果你需要在外网也能访问 DweebUI，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com)

注册账号，支持全平台使用。详细的配置教程可以 [点击这里查看](https://www.passnat.com/posts/p)

，一站式解决远程访问需求。

### 快速部署方案

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

**第一步：添加镜像**

点击'本地镜像' - '添加镜像' - '从 URL 添加'，在镜像地址栏填入：

```
lllllllillllllillll/dweebui
```

等待镜像下载完成后，你会看到 DweebUI 镜像出现在镜像列表中，点击启动按钮创建容器。

**第二步：配置容器参数**

启动容器后，进入容器设置界面：

开机自启：勾选'开机自动启动'，确保 NAS 重启后容器能自动恢复运行

端口映射：将容器内的 `8000` 端口映射到宿主机，例如映射到 `8000:8000`。如果你的 NAS 上 8000 端口已被占用，可以改为其他端口如 `8001:8000`，但容器内部端口必须保持为 8000

环境变量设置：

**必须设置：**

`SECRET`: 注册页面的共享密钥，请修改为你自己的密码，默认值为 `MrWiskers`（强烈建议更改）

**可选设置：**

`PORT`: 服务绑定端口，默认为 `8000`（通常无需修改）

存储挂载：

将容器内的 `/app` 目录挂载到 NAS 本地存储，用于保存配置数据

最关键的是要挂载 Docker Socket：`/var/run/docker.sock:/var/run/docker.sock`，这样 DweebUI 才能与宿主机的 Docker 守护进程通信，实现容器管理功能

**第三步：启动并访问**

配置完成后点击启动，容器即可运行。打开浏览器访问 `http://你的NAS地址:8000`，就能看到 DweebUI 的登录界面了。

首次访问时需要用 SECRET 密钥进行注册，创建管理员账户后即可开始使用。

### Docker Compose 部署（可选）

如果你更习惯使用 Docker Compose，可以参考以下配置：

```yaml
version: '3.9'
services:
  dweebui:
    container_name: dweebui
    image: lllllllillllllillll/dweebui
    environment:
      PORT: 8000
      SECRET: MrWiskers
    restart: unless-stopped
    ports:
      - 8000:8000
    volumes:
      - dweebui:/app
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - dweebui_net

volumes:
  dweebui:

networks:
  dweebui_net:
    driver: bridge
```

将上述内容保存为 `docker-compose.yml` 文件，然后在该文件所在目录执行：

```bash
docker compose up -d
```

（如果你的系统较老，可能需要使用 `docker-compose up -d` 或加上 `sudo` 前缀）

## 核心功能一览

📊 实时仪表板：动态展示服务器和容器的性能指标

🎮 容器控制：一键启停、暂停、重启容器，查看详细信息和日志

👥 多用户权限：内置完整的权限管理系统，支持多用户协作

🎨 主题切换：亮色/深色模式随心切换，适配各种使用场景

📱 移动友好：响应式设计，手机平板电脑完美适配

🏗️ 资源管理：轻松管理 Docker 网络、镜像和存储卷

📦 应用模板：内置丰富的应用模板库，一键部署常用服务

🔧 Compose 支持：原生支持 Docker Compose 编排文件

## 总结

DweebUI 是一个功能完整、易于上手的容器管理面板。无论你是 Docker 新手还是经验丰富的运维人员，都能在这个工具中找到适合自己的使用方式。简单的部署流程、强大的功能特性和优雅的用户界面，使得 DweebUI 成为家庭 NAS 和小型服务器的理想选择。赶快部署一个，开启更高效的容器管理之旅吧！

---

原文：<https://www.passnat.com/posts/dweebui-container-management-panel-docker-podman-deployment>
