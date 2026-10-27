---
title: "容器管理神器Yacht一键部署指南，Docker应用模板化管理简单易用，支持内网穿透远程访问管理"
date: 2025-10-16
description: "Yacht是功能强大的容器管理界面，提供直观的Vuetify UI和一键启动功能。支持模板导入、容器编辑、卷管理等高级功能，完全兼容Portainer模板格式。通过简洁Web界面，轻松管理Docker容器和Docker Compose项目，适合自建爱好者和服务器运维人员快速部署各类应用。"
canonical: https://www.passnat.com/posts/yacht-docker-container-management-deployment-guide
---

## Yacht 容器管理平台：功能强大的 Docker 管理界面

#### 项目介绍

Yacht 是一款功能强大的容器管理界面，专注于模板化部署和一键启动功能。它提供了直观的 Vuetify UI 界面，让用户可以轻松管理 Docker 容器和 Docker Compose 项目。无论你是自建爱好者还是服务器运维人员，Yacht 都能帮助你快速部署和管理各类应用容器。

项目支持模板导入、容器编辑修改、卷管理等高级功能，完全兼容 Portainer 模板格式。通过简洁的 Web 界面，即使是初学者也能轻松上手容器管理。项目还在不断完善中，新版本正在积极开发中。

项目地址：[点我跳转](https://github.com/selfhostedpro/yacht)

#### 部署安装

如果你需要在外网也能访问 Yacht，建议配置内网穿透工具。可以前往[帕斯内网穿透](https://passnat.com)注册账号，通过内网穿透实现远程访问。具体的配置步骤可以[参考这里](https://www.passnat.com/posts/p)了解全平台上手的完整攻略。

**使用 Docker 快速部署**

打开你的 NAS 或服务器的 Docker 管理界面，点击「本地镜像」-「添加镜像」-「从 URL 添加」。

在镜像地址栏填入：selfhostedpro/yacht:latest

等待镜像下载完成后，点击启动按钮启动容器。

**容器配置说明**

勾选「开机自动启动」后进入下一步。

**端口映射设置**

Yacht 默认运行在容器内的 8000 端口。你需要将其映射到主机的某个端口（建议使用 8000 或其他未被占用的端口）。如果你的主机 8000 端口已被占用，可以修改为其他端口号，比如 8001、8888 等，但容器内部端口保持 8000 不变。

**存储位置配置**

Yacht 需要访问 Docker Socket 来管理容器。你需要将主机的 `/var/run/docker.sock` 映射到容器内的 `/var/run/docker.sock`。

如果你需要管理 Docker Compose 项目，建议创建一个文件夹（如 `/home/compose`）用于存放 compose 文件，并将其映射到容器内的 `/compose` 目录。

**环境变量设置**

以下是可选的环境变量配置：

**必须设置**

无强制要求的环境变量

**建议设置**

SECRET\_KEY：设置为一个随机字符串，确保重启 Yacht 后不会被登出

ADMIN\_EMAIL：设置默认管理员账户的邮箱地址

**可选设置**

PUID：容器运行的用户 ID（默认为 root）

PGID：容器运行的用户组 ID（默认为 root）

DISABLE\_AUTH：设置为 true 可禁用身份验证（不推荐，除非你使用 Authelia 等外部认证）

DATABASE\_URL：如需使用 PostgreSQL 等数据库替代内置 SQLite，格式为 `postgresql://user:password@server/db`

COMPOSE\_DIR：指定 Docker Compose 项目文件夹的路径（仅 compose 版本需要）

其他变量保持默认即可，直接下一步启动容器。

**访问应用**

容器启动成功后，在浏览器中访问 `http://你的服务器IP:8000`（如果你修改了端口，请使用对应的端口号）即可打开 Yacht 管理界面。

首次访问时，使用默认凭证登录，然后你就可以开始添加模板、管理容器和部署应用了。

建议首先添加官方推荐的模板：`https://raw.githubusercontent.com/SelfhostedPro/selfhosted\_templates/yacht/Template/template.json`，这样你就能快速访问大量预配置的应用模板。

**总结**

通过以上步骤，你就能在自己的服务器或 NAS 上快速部署 Yacht 容器管理平台。它将大大简化你的容器管理工作流程，让部署应用变得像点击几个按钮一样简单。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/yacht-docker-container-management-deployment-guide>


## 相关文章

- [Yacht容器管理系统一键部署指南，Docker应用模板快速启动，NAS自建服务完整教程](yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [Portainer容器管理面板部署指南，飞牛NAS用Docker搭建可视化面板，配内网穿透在外面也能管容器](portainer-nas-docker-container-management-panel-deployment-guide.md)
- [家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆](photostructure-nas-private-photo-management-remote-access.md)
