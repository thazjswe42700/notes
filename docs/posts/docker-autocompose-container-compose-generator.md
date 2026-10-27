---
title: "docker-autocompose Docker容器自动生成compose配置文件工具，一键导出容器环境设置"
date: 2025-10-16
description: "docker-autocompose是一款强大的Docker容器管理工具，能自动从运行中的容器生成docker-compose配置文件。支持导出单个或所有容器配置，包含卷挂载、端口映射、环境变量等。适合规范化管理Docker环境、快速迁移容器配置。支持Docker和NAS设备使用。"
canonical: https://www.passnat.com/posts/docker-autocompose-container-compose-generator
---

## 项目介绍

docker-autocompose **是一款强大的 Docker 容器管理工具，它能够自动从已运行的 Docker 容器中生成对应的 docker-compose 配置文件。无论你是手动启动了多个复杂的容器（包含各种卷挂载、端口映射、环境变量等配置），还是想要快速迁移现有的容器环境，这个工具都能帮你轻松搞定。**

它的核心功能就是将你已经在运行的容器配置完整地导出成标准的 docker-compose.yml 文件，让你可以轻松复现、共享和管理整个容器环境。对于想要规范化管理 Docker 容器的用户来说，这是一个必不可少的利器。

项目地址：[点我跳转](https://github.com/Red5d/docker-autocompose)

## 部署安装

如果需要在外网也能访问你的 Docker 环境，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它支持全平台使用，具体的上手步骤可以 [点击这里查看](https://www.passnat.com/posts/p)。

### 快速开始

这个工具最简单的使用方式就是通过 Docker 容器来运行。只需一条命令就能将你运行中的容器配置导出成 docker-compose 文件。

**第一步：拉取镜像**

打开你的终端或 NAS 设备的命令行工具，执行以下命令拉取 docker-autocompose 镜像：

```bash
docker pull ghcr.io/red5d/docker-autocompose:latest
```

**第二步：生成 docker-compose 文件**

假设你想要导出某个正在运行的容器（比如容器名为 'my-container'），只需运行：

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose:latest my-container
```

这个命令会输出该容器的完整 docker-compose 配置。

**第三步：导出所有容器配置**

如果你想一次性导出所有正在运行的容器配置，可以使用：

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose:latest $(docker ps -aq)
```

**第四步：保存配置文件**

将输出的内容复制到一个文件中，比如 'docker-compose.yml'：

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/red5d/docker-autocompose:latest my-container > docker-compose.yml
```

完成后，你就可以用这个生成的 docker-compose 文件来管理你的容器了。

### 在 NAS 设备上使用

如果你使用的是飞牛 NAS 或其他 NAS 设备：

打开 NAS 的管理界面，找到'容器'或'Docker'相关菜单

点击'本地镜像' - '添加镜像' - '从 URL 添加'

镜像地址填写：'ghcr.io/red5d/docker-autocompose:latest'

等待镜像下载完成后点击启动

启动后，你可以通过 NAS 的命令行界面或 SSH 连接来运行上述命令。

本篇教程结束

---

原文：<https://www.passnat.com/posts/docker-autocompose-container-compose-generator>


## 相关文章

- [功能丰富的音乐播放器SPlayer部署指南，Vue3开发支持网页和客户端双端，集成网易云API随时享受高品质音乐体验](splayer-music-player-vue3-deployment-guide-netease-cloud-api.md)
- [Docker容器自动更新工具Watchtower部署指南，NAS一键启用容器自动检测升级功能](docker-watchtower-nas-auto-update-guide.md)
- [无需命令行Docker可视化管理工具，DockerUI网页界面轻松管理容器与镜像，支持NAS内网穿透部署](docker-ui-web-management-container-image-nas-intranet-penetration.md)
- [WUD Docker容器镜像更新监控工具，NAS一键部署，自动检测镜像版本，内网穿透远程管理](wud-docker-container-image-update-monitoring-tool-nas-one-click-deployment-automatic-version-detection-intranet-penetration-remote-management.md)
- [容器管理神器Yacht一键部署指南，Docker应用模板化管理简单易用，支持内网穿透远程访问管理](yacht-docker-container-management-deployment-guide.md)
