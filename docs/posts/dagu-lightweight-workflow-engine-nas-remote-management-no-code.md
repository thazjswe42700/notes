---
title: "轻量级工作流引擎Dagu无代码部署，NAS一键启动内网穿透远程管理任务"
date: 2025-10-16
description: "轻量级工作流引擎Dagu，采用YAML声明式配置，支持定时执行、Shell命令、SSH远程执行和Docker容器运行。无需代码、无需数据库，一个二进制文件即可运行。适合管理大量Cron任务和Shell脚本，通过现代化Web UI直观查看执行状态、日志记录，轻松管理任务依赖关系。"
canonical: https://www.passnat.com/posts/dagu-lightweight-workflow-engine-nas-remote-management-no-code
---

## 项目介绍

Dagu 是一个轻量级的工作流引擎，具有现代化的 Web 用户界面。它采用简洁的 YAML 声明式格式定义工作流，支持定时执行、Shell 命令、SSH 远程执行和 Docker 容器运行。内置错误通知和日志记录功能，开箱即用。

相比于 Airflow 等复杂的工作流调度系统，Dagu 无需编写代码，无需依赖数据库或云服务，只需一个二进制文件即可运行。它特别适合那些有大量 Cron 任务、Shell 脚本的遗留系统，能够帮助您清晰地可视化和管理任务间的依赖关系，通过直观的 Web UI 查看执行状态、日志记录，轻松重新运行或停止任务。

项目地址： [点我跳转](https://github.com/dagu-dev/dagu)

## 部署安装

若需要在公网环境访问 Dagu，建议配置内网穿透服务。可前往 [帕斯内网穿透](https://passnat.com) 注册账号，并参考 [全平台上手攻略](https://www.passnat.com/posts/p) 快速配置，让您随时随地都能管理工作流任务。

**最快速的部署方式 - Docker 单命令启动：**

打开飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
ghcr.io/dagu-org/dagu:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

勾选 **开机自动启动** 后进入下一步。

**端口设置：**

将容器端口 8080 映射到主机端口。如果您的 NAS 上 8080 端口已被占用，可修改主机端口号（如改为 8888），但容器内端口必须保持为 8080。

**存储位置设置：**

Dagu 需要持久化存储工作流定义和执行日志。请在 NAS 上创建一个文件夹（如 /mnt/data/dagu），将其映射到容器内的 /var/lib/dagu 目录。这样您的工作流配置和历史记录才能被保留。

**环境变量设置：**

以下是可选的环境变量配置，根据需求填写：

环境变量 | 默认值 | 说明 | 是否必须

DAGU\_HOST | 127.0.0.1 | Web UI 服务器地址 | 否

DAGU\_PORT | 8080 | Web UI 服务器端口 | 否

DAGU\_TZ | - | 服务器时区（如 Asia/Shanghai） | 否

DAGU\_DEBUG | false | 调试模式开关 | 否

DAGU\_LOG\_FORMAT | text | 日志格式（text 或 json） | 否

DAGU\_AUTH\_BASIC\_USERNAME | - | 基础认证用户名 | 否

DAGU\_AUTH\_BASIC\_PASSWORD | - | 基础认证密码 | 否

大多数情况下无需设置环境变量，使用默认配置即可正常运行。如需启用 Web UI 访问认证，才需要配置用户名和密码。

其他配置项保持默认，直接点击启动即可。

容器启动成功后，打开浏览器访问 http://NAS\_IP:8080（若修改了映射端口则使用对应端口号），即可看到 Dagu 的 Web 界面。

**开始使用：**

在 Web UI 中，您可以创建新的工作流配置文件（YAML 格式），定义任务步骤和执行计划。例如创建一个简单的定时任务：

```
schedule: '0 0 * * *'  # 每天午夜执行

steps:
  - echo 'Hello from Dagu!'
  - echo '这是第二个任务步骤'
```

保存配置后，Dagu 会自动按照 Cron 表达式进行定时调度。您可以在 Dashboard 中实时查看任务执行状态、查阅详细日志，需要时可手动触发或停止任务运行。

到此，Dagu 工作流引擎的部署就完成了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/dagu-lightweight-workflow-engine-nas-remote-management-no-code>


## 相关文章

- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [浏览器里就是命令行：飞牛 NAS 用 Docker 自建 WebSSH + 内网穿透远程敲命令](webssh-fnos-docker-passnat.md)
- [Cronicle Edge 定时任务：NAS Docker 部署 + 内网穿透](cronicle-edge-nas-docker-scheduled-tasks-web-ui-job-logs-intranet-penetration.md)
- [轻量级个人导航页面 Flare 快速部署教程，飞牛 NAS Docker 部署 + 内网穿透远程访问](lightweight-personal-navigation-flare-quick-deploy-tutorial-nas-one-click-install-guide.md)
- [Yacht容器管理系统一键部署指南，Docker应用模板快速启动，NAS自建服务完整教程](yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial.md)
