---
title: "WeekToDo开源周计划管理应用，极简任务管理，本地部署隐私保护，Docker一键启动NAS自建。"
date: 2025-10-16
description: "WeekToDo是开源极简周计划管理应用，采用Vue3技术栈，跨平台支持Windows、Mac、Linux及网页版。功能包括自定义待办事项、拖拽排序、彩色标签、时间设置、任务递归、子任务、Markdown格式、多语言、亮暗主题、本地存储和通知提醒。本文详细介绍Docker Compose快速部署方式，帮助用户在NAS或服务器上轻松搭建个人任务管理系统，保护数据隐私。"
canonical: https://www.passnat.com/posts/week-todo-open-source-task-management-local-deployment-docker-nas
---

## 项目介绍

WeekToDo 是一款开源的、注重隐私保护的极简周计划管理应用。它采用 Vue 3 技术栈构建，跨平台支持 Windows、Mac、Linux 以及在线网页版本。这是一个功能完整的任务规划工具，让你能够轻松组织日常工作和项目计划。

应用界面简洁优雅，提供了丰富的功能特性。你可以创建自定义待办事项列表、使用拖拽功能快速调整任务优先级、为任务设置颜色标签和具体时间、支持任务递归和子任务功能。同时支持 Markdown 格式、多语言界面、亮暗主题切换，以及本地存储确保你的数据隐私。应用还内置了通知提醒功能，帮助你不错过任何重要的任务截止日期。

[点我跳转](https://github.com/manuelernestog/weektodo)

## 部署安装

如果你想在办公室、家里或任何地方都能随时访问 WeekToDo，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的操作步骤可以参考 [内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)。

### 使用 Docker Compose 快速启动

这是最便捷的部署方式，适合所有技术水平的用户。

**第一步：准备配置文件**

复制下列的 docker-compose 配置文件，保存为 `docker-compose.yml`：

```yaml
version: '3'
services:
  app:
    build: .
    container_name: weektodo
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - ".:/app"
      - "node_modules:/app/node_modules"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s

volumes:
  node_modules:
```

**第二步：启动容器**

打开你的 NAS 设备或服务器终端，进入配置文件所在的目录，执行以下命令：

```
docker-compose up -d
```

稍等片刻，容器会自动拉取镜像、安装依赖并启动应用。

**第三步：端口配置说明**

上述配置中，应用运行在容器内的 **8080 端口**，并映射到主机的 8080 端口。如果你的主机上 8080 端口已被占用，可以修改配置文件中的第一个 8080 为其他端口号（如 8081、8082 等），格式保持为 `'新端口:8080'` 即可。

**第四步：访问应用**

部署完成后，在浏览器中访问：

```
http://你的服务器IP:8080
```

即可看到 WeekToDo 的优雅界面，开始管理你的周计划了。

## 总结

通过以上简单几步，你就能在自己的服务器或 NAS 上部署一个功能完整的任务管理系统。WeekToDo 凭借其极简的设计理念和强大的功能，成为了开源社区中备受欢迎的生产力工具。无论是个人任务追踪还是团队项目协作，它都能满足你的需求。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/week-todo-open-source-task-management-local-deployment-docker-nas>


## 相关文章

- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [无状态密码管理器Heimdallr，一密掌控所有账户，NAS本地部署零风险，内网穿透远程访问无忧](heimdallr-stateless-password-manager-nas-local-deployment.md)
- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
- [个人综合服务平台UniBoard Docker极简部署，云笔记短链接文件分享一站式解决方案](unibboard-docker-deployment-personal-service-platform-cloud-notes-shortlinks-file-sharing.md)
