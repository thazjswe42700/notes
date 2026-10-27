---
title: "简洁呼吸冥想应用Calmness Docker快速部署指南 NAS内网穿透远程访问"
date: 2025-10-16
description: "Calmness是一款简洁优雅的呼吸练习冥想工具，帮助用户放松身心、缓解压力。本教程介绍Calmness的项目特点、Docker快速部署方法、端口存储配置及内网穿透远程访问设置，助您轻松搭建个人呼吸冥想应用。"
canonical: https://www.passnat.com/posts/calmness-docker-deployment-nas-remote-access
---

## 项目介绍

Calmness 是一个简洁优雅的呼吸练习工具，专为帮助用户放松身心、缓解压力而设计。这个项目采用现代化的技术栈构建，提供了一个开箱即用的呼吸冥想应用。无论你是在工作间隙需要片刻宁静，还是想要建立健康的呼吸习惯，Calmness 都能为你提供一个舒适的冥想体验。

项目地址：[点我跳转](https://github.com/remvze/calmness)

## 部署安装

如需在外网也能访问该应用，建议配置内网穿透服务以实现远程访问，推荐使用 [帕斯内网穿透](https://passnat.com)。先注册好账号备用，具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，点击本地镜像-添加镜像-从 URL 添加。

镜像地址填写：ghcr.io/remvze/calmness:latest

等待镜像下载完成后，点击启动按钮启动容器。

**端口配置**

在容器启动配置中，设置端口映射。将容器内部的 **8080 端口**映射到宿主机的任意端口（如 8080），如果 8080 端口已被占用，可以修改宿主机端口号，但容器内的 8080 端口保持不变。

**存储配置**

该应用为前端静态应用，无需特殊的数据持久化存储，使用默认配置即可。

**环境变量**

此应用无需配置额外的环境变量，保持默认即可。

勾选开机自动启动后直接启动容器。

创建成功后，你可以通过访问 http://你的NAS地址:8080 来打开 Calmness 应用，开始你的呼吸冥想之旅。

到这里部署就完成了，现在你可以随时随地使用这个优雅的呼吸练习工具来放松身心。

本篇教程结束

---

原文：<https://www.passnat.com/posts/calmness-docker-deployment-nas-remote-access>
