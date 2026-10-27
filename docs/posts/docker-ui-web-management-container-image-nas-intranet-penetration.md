---
title: "无需命令行Docker可视化管理工具，DockerUI网页界面轻松管理容器与镜像，支持NAS内网穿透部署"
date: 2025-10-16
description: "DockerUI是一款功能强大的Docker可视化管理工具，提供简洁易用的网页界面，无需命令行即可轻松管理Docker环境。支持镜像、容器、数据卷、网络配置和Swarm集群管理，涵盖95%以上Docker功能。完全开源免费，容器化部署便捷高效，适合单节点和集群环境。"
canonical: https://www.passnat.com/posts/docker-ui-web-management-container-image-nas-intranet-penetration
---

## 项目介绍

DockerUI 是一款功能强大的 Docker 可视化管理工具，专为不熟悉 Docker 命令行的用户设计。它提供了一个简洁易用的网页界面，让你无需记忆复杂的 Docker 命令，就能轻松管理和维护 Docker 环境。

无论你是在单节点 Docker 环境中工作，还是在管理 Docker Swarm 集群，DockerUI 都能胜任。它涵盖了超过 95% 的 Docker CLI 命令功能，支持镜像管理、容器管理、数据卷管理、网络配置、Swarm 集群管理等多项功能。最重要的是，这是一个完全开源且免费的项目，基于容器化部署方式，使用非常便捷高效。

项目地址：[点我跳转](https://github.com/gohutool/docker.ui)

## 部署安装

如果你需要在外网也能访问本地部署的 DockerUI，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台使用指南。

### Docker 单命令快速启动

这是最快速的部署方式，只需一条命令即可启动 DockerUI：

```bash
docker container run --rm --name docker.ui -v /var/run/docker.sock:/var/run/docker.sock -p 8999:8999 joinsunsoft/docker.ui
```

打开你的浏览器，访问 `http://你的IP地址:8999`，即可进入 DockerUI 管理界面。

**默认登录凭证：**

用户名：`ginghan`

密码：`123456`

登录后就可以开始使用 DockerUI 的各项功能了。

### 如果端口冲突

如果你的系统中 8999 端口已被占用，可以修改命令中的端口映射。例如，如果想使用 9000 端口，只需将命令改为：

```bash
docker container run --rm --name docker.ui -v /var/run/docker.sock:/var/run/docker.sock -p 9000:8999 joinsunsoft/docker.ui
```

这样就可以通过 `http://你的IP地址:9000` 访问 DockerUI 了。记住，冒号前面的端口号可以自由修改，但冒号后面的 8999（容器内端口）必须保持不变。

### NAS 设备部署

如果你使用的是飞牛 NAS 或其他 NAS 设备，可以按照以下步骤操作：

1. 打开 NAS 设备的管理界面

2. 进入'本地镜像'或'Docker'模块

3. 选择'添加镜像'或'从 URL 添加'

4. 输入镜像名称：`joinsunsoft/docker.ui`

5. 等待镜像下载完成

6. 点击'启动'按钮启动容器

7. 勾选'开机自动启动'确保容器在 NAS 重启后自动运行

8. 设置端口映射为 `8999:8999`（如有冲突可修改外部端口）

9. 完成配置后启动容器

启动成功后，你就可以通过 `http://NAS的IP地址:8999` 访问 DockerUI 了。

## 功能特性

DockerUI 为你提供了以下强大功能：

单机 Docker 管理：镜像管理、容器管理、数据卷管理、网络配置、仓库配置等

Docker Swarm 集群管理：集群信息查看、节点管理、服务管理、任务管理

可视化操作：容器文件系统浏览、实时资源监控、进程查看、Web 控制台

镜像操作：镜像搜索、拉取、构建、导入导出、推送等

任务调度：支持 Docker 和 Swarm 的任务调度功能

本篇教程结束

---

原文：<https://www.passnat.com/posts/docker-ui-web-management-container-image-nas-intranet-penetration>


## 相关文章

- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [容器管理神器Yacht一键部署指南，Docker应用模板化管理简单易用，支持内网穿透远程访问管理](yacht-docker-container-management-deployment-guide.md)
- [可视化Docker Compose管理工具，NAS极简部署Compose Craft，无公网内网穿透远程访问](compose-craft-docker-visualization-nas-deployment-internal-network-remote-access.md)
- [Oxker Docker容器管理工具TUI界面轻量级终端控制，Rust开发支持实时监控日志查看](oxker-docker-container-management-tui-lightweight-terminal-rust-realtime-monitoring.md)
- [BGmi番剧自动追踪管理工具Docker部署指南，NAS内网穿透远程追番解决方案](bgmi-anime-tracker-docker-nas-remote-access.md)
