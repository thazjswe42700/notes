---
title: "DopplerTask开源自动化平台Docker快速部署指南，NAS轻松搭建工作流自动化系统"
date: 2025-10-16
description: "发现DopplerTask开源任务自动化平台，轻松构建可视化工作流。支持SSH、HTTP、数据库、浏览器自动化等丰富动作库。Docker一键部署，资源占用少，运行速度快，是家庭和企业自动化的理想选择。包含完整部署指南和CLI工具使用说明。"
canonical: https://www.passnat.com/posts/doppler-task-docker-nas-workflow-automation-guide
---

## 项目介绍

DopplerTask 是一个革命性的开源任务自动化平台，它让任何人都能轻松构建、运行、复用和分享自动化工作流。无论你是想自动执行一系列 Bash 脚本，还是想远程启动你的汽车，DopplerTask 都能帮你实现。

这个项目采用现代化的技术栈构建，拥有直观易用的可视化界面，支持丰富的内置动作库（如 SSH 连接、HTTP 请求、数据库操作、浏览器自动化等）。同时，DopplerTask 致力于打造一款环保高效的软件，占用资源少，运行速度快，是家庭自动化和工作流自动化的理想选择。

[点我跳转](https://github.com/dopplertask/dopplertask)

项目地址：

## 部署安装

想要在外网也能访问你的 DopplerTask 实例？建议配置内网穿透功能。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以参考 [全平台上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 快速部署（Docker 单命令）

打开你的 NAS 设备或任何支持 Docker 的系统，执行以下命令即可一键启动 DopplerTask：

```bash
docker run -d -p 8090:8090 -p 61614:61614 -v $HOME:/root dopplertask/dopplertask
```

**命令说明：**

`-d`：后台运行容器

`-p 8090:8090`：映射 Web UI 访问端口（8090 是容器内部端口，前面的 8090 是你本机的访问端口，如果冲突可改为其他端口，如 8091:8090）

`-p 61614:61614`：映射消息队列端口（用于任务调度，如无特殊需求保持不变）

`-v $HOME:/root`：挂载用户主目录，用于任务文件持久化存储

### 访问应用

部署完成后，在浏览器中访问：

```
http://localhost:8090/public/index.html
```

即可看到 DopplerTask 的可视化界面。在这里你可以：

拖拽构建自动化工作流

添加各类动作节点（打印、SSH、HTTP、数据库、浏览器自动化等）

设置条件判断和重试机制

通过 Webhook 触发任务执行

查看执行日志和历史记录

### 可选：安装 CLI 工具

如果你想通过命令行运行任务，可以安装 DopplerTask CLI：

```
npm install -g dopplertask-cli
```

安装完成后，使用以下命令运行任务：

```
dopplertask run [taskname]
```

查看帮助信息：

```
dopplertask --help
```

本篇教程结束。现在你已经拥有一个功能强大的任务自动化平台了，尽情发挥你的创意吧！

---

原文：<https://www.passnat.com/posts/doppler-task-docker-nas-workflow-automation-guide>
