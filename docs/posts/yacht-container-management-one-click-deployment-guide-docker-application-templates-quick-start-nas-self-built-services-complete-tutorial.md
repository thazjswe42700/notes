---
title: "Yacht容器管理系统一键部署指南，Docker应用模板快速启动，NAS自建服务完整教程"
date: 2025-10-16
description: "Yacht是功能强大的Docker容器管理界面，提供直观Web UI和模板化部署。支持一键启动、Docker-Compose兼容、Portainer模板导入。适合NAS用户和自建服务爱好者快速部署管理应用，无需复杂命令行操作。"
canonical: https://www.passnat.com/posts/yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial
---

## 项目介绍

Yacht 是一个功能强大的容器管理界面，专为模板化部署和一键启动而设计。它提供了直观的 Web UI，让你能够轻松管理 Docker 容器，无需复杂的命令行操作。无论是自建服务爱好者还是 NAS 用户，Yacht 都能帮你快速部署和管理各类应用。

项目采用 Vuetify UI 框架构建，支持基础容器管理、模板框架、Docker-Compose 兼容性以及高级容器编辑功能。最重要的是，Yacht 完全兼容 Portainer 模板，你可以导入丰富的应用模板库，实现一键部署。无论你是想快速搭建家庭影视系统、还是部署各类自建服务，Yacht 都能让这一切变得简单高效。

项目地址： [点我跳转](https://github.com/SelfhostedPro/Yacht)

## 部署安装

如果你需要在外网也能访问到 Yacht，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号并按照 [这份指南](https://www.passnat.com/posts/p) 完成全平台配置，轻松实现远程访问。

**打开你的 NAS 设备或 Linux 主机**

点击本地镜像 - 添加镜像 - 从 URL 添加。在镜像地址栏填入以下内容：

```
selfhostedpro/yacht:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

**容器启动配置**

勾选开机自动启动选项，确保 Yacht 随系统启动。

接下来进行端口映射设置。Yacht 默认监听容器内的 **5000 端口**，你可以将其映射到主机的任意端口（比如 5000、8080 等）。如果你的主机上该端口已被占用，只需修改主机端口即可，容器内部端口保持 5000 不变。

**存储和数据持久化**

Yacht 会在 /yacht/AppData 目录下存储应用数据和配置文件。建议你在 NAS 或主机上创建一个专用文件夹（比如 /data/yacht），然后将其映射到容器内的 /yacht/AppData 目录，这样可以确保你的配置和数据在容器重启后不会丢失。

**环境变量设置**

以下是 Yacht 支持的环境变量配置：

**必须设置：** 无（所有变量都是可选的）

**建议设置：**

SECRET\_KEY：设置一个随机字符串，确保你在 Yacht 重启后不会被登出。例如：your-random-secret-key-here

ADMIN\_EMAIL：设置默认管理员账户的邮箱地址。例如：admin@example.com

**可选设置：**

PUID：容器运行的用户 ID（默认值：1000）

PGID：容器运行的用户组 ID（默认值：1000）

DISABLE\_AUTH：设置为 true 可禁用后端认证（不推荐，除非你使用 Authelia 等认证工具）

DATABASE\_URL：如果想使用 PostgreSQL 等外部数据库替代内置 SQLite，按格式填写：postgresql://user:password@postgresserver/db

COMPOSE\_DIR：指定 docker-compose 项目所在的容器内路径（仅 compose 版本需要）

其他变量保持默认即可，无需特殊配置。

**启动容器**

完成以上配置后，点击下一步启动容器。

创建成功后，你可以通过浏览器访问 http://你的主机IP:5000（或你配置的其他端口）来打开 Yacht 的管理界面。

**首次使用建议**

进入 Yacht 后，建议你先在设置中添加模板 URL。官方推荐的模板地址为：

```
https://raw.githubusercontent.com/SelfhostedPro/selfhosted_templates/yacht/Template/template.json
```

添加后就能看到大量预配置的应用模板，一键部署各类服务。

到这里，Yacht 容器管理系统就已经部署完成了。你现在可以通过简洁的 Web 界面轻松管理你的 Docker 容器，享受模板一键部署的便利。祝你使用愉快！

本篇教程结束

---

原文：<https://www.passnat.com/posts/yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial>
