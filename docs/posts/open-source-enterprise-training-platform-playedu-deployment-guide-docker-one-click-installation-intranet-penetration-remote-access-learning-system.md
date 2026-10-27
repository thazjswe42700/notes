---
title: "开源企业培训平台PlayEdu部署指南，Docker一键安装内网穿透远程访问学习系统"
date: 2025-10-16
description: "PlayEdu是白书科技打造的企业级在线培训平台，基于Java+MySQL和React18开发。提供部门管理、学员管理、在线视频学习、进度追踪等核心功能，支持PC、H5多端访问。本文详细介绍PlayEdu的Docker Compose一键部署方案，包括获取代码、启动容器、访问应用、登录系统等完整步骤，助力企业快速搭建专属培训平台。"
canonical: https://www.passnat.com/posts/open-source-enterprise-training-platform-playedu-deployment-guide-docker-one-click-installation-intranet-penetration-remote-access-learning-system
---

## 项目介绍

PlayEdu 是一款由白书科技团队精心打造的业内领先线上培训解决方案。基于 Java + MySQL 开发，采用前后端分离架构，前端采用 React18 框架，后端基于 SpringBoot3 构建。这是一个功能完整、开箱即用的企业级在线培训平台。

开源版本提供了部门管理、学员管理、在线视频学习、学员进度追踪、视频私有化存储等核心培训功能。无论你是中小企业还是大型组织，都可以快速部署一个属于自己的专属培训平台。平台支持多端访问，包括 PC 端、H5 移动端和 API 接口，让学员随时随地都能进行学习。

项目地址：[点我跳转](https://github.com/PlayEdu/PlayEdu)

## 部署安装

如果需要在外网也能访问你的培训平台，建议配置内网穿透服务。可以前往[帕斯内网穿透](https://passnat.com)注册账号，具体的配置方法可以[点击查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### 最快速上手方案

PlayEdu 项目提供了最便捷的 Docker Compose 一键部署方案，非常适合技术小白用户快速上手。

**第一步：获取项目代码**

打开你的 NAS 设备或服务器终端，执行以下命令：

```bash
git clone --branch main https://gitee.com/playeduxyz/playedu.git playedu
```

**第二步：启动容器**

进入项目目录并启动服务：

```
cd playedu && docker-compose up -d
```

命令执行完成后，Docker 会自动拉取镜像、创建容器并启动所有服务。整个过程可能需要几分钟，请耐心等待。

**第三步：访问应用**

部署完成后，你可以通过以下地址访问：

管理后台：http://localhost:9900

学员端（PC）：http://localhost:9800

学员端（H5 移动）：http://localhost:9801

API 接口：http://localhost:9700

**第四步：登录系统**

打开浏览器访问管理后台地址，使用默认管理员账号登录：

账号：admin@playedu.xyz

密码：playedu

登录成功后，你就可以开始创建部门、添加学员、上传课程视频等操作了。

**端口说明**

如果遇到端口被占用的情况，可以编辑项目根目录的docker-compose.yml文件，修改对应的端口映射。例如，如果 9900 端口被占用，可以改为9901:9900或其他未被占用的端口。修改后重新执行启动命令即可生效。

**存储配置**

PlayEdu 使用 MySQL 数据库存储数据，Docker Compose 配置已包含数据持久化设置。你的所有课程、学员信息、学习记录都会被安全保存。

**环境变量说明**

PlayEdu 的 Docker Compose 配置已预设了所有必要的环境变量，包括数据库连接、服务端口等，开箱即用无需额外配置。如非特殊需求，保持默认设置即可正常运行。

## 核心功能介绍

✨ 部门管理：灵活的组织架构管理，支持多级部门划分

👥 学员管理：完整的学员信息管理和权限控制

🎬 在线学习：支持视频流媒体播放，学习体验流畅

📊 学习追踪：实时记录学员学习进度和完成情况

🔒 私有存储：视频文件私有化存储，安全可控

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-enterprise-training-platform-playedu-deployment-guide-docker-one-click-installation-intranet-penetration-remote-access-learning-system>
