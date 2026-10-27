---
title: "开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问"
date: 2025-10-16
description: "Cal.com是开源日程管理和会议预约平台，Calendly的完美替代方案。基于Next.js、React等现代技术构建，支持自托管、白标定制、API驱动。完全掌控数据和工作流，支持商务会议、研讨会、课程等多种场景。可在NAS上快速部署，Docker镜像开箱即用，无需依赖第三方平台。"
canonical: https://www.passnat.com/posts/open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access
---

## 项目介绍

Cal.com（原名 Calendso）是一个开源的日程管理和会议预约平台，是 Calendly 的完美开源替代方案。它基于 Next.js、React、Tailwind CSS、Prisma 和 tRPC 等现代技术栈构建，提供了一个功能完整、高度可定制的日程安排解决方案。

无论是商务会议、研讨会、瑜伽课程还是家庭通话，Cal.com 都能胜任。与其他商业日程工具不同的是，Cal.com 让你完全掌控自己的数据、工作流和外观。支持自托管或由官方托管，天生支持白标定制，API 驱动，可以部署在自己的域名上。你拥有对所有事件和数据的完全控制权，无需依赖任何第三方平台。

项目地址：[点我跳转](https://github.com/calendso/calendso)

## 部署安装

如果你需要在外网也能访问这个应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

Cal.com 提供了多种部署方式，最快速的方式是使用 Docker。下面我们来看看如何在 NAS 或其他设备上快速部署。

### 使用 Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备的管理界面。

进入容器管理模块，点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入以下镜像地址：

```
calcom/cal.com:latest
```

等待镜像下载完成，下载完毕后点击 **启动** 按钮启动容器。

勾选 **开机自动启动** 选项，确保容器在系统重启后能自动运行。

### 端口配置

在容器启动配置中进行端口映射设置。Cal.com 默认运行在容器内的 **3000 端口**。

容器内端口：3000（这个不需要修改）

主机映射端口：可以设置为 3000 或其他未被占用的端口

如果你的系统中 3000 端口已被其他应用占用，可以修改主机映射端口为其他端口（比如 3001、8080 等），但请记住容器内的 3000 端口保持不变。

### 存储配置

Cal.com 需要持久化存储来保存数据库和配置信息。建议在 NAS 上创建一个专用文件夹用于数据存储，例如 /mnt/data/calcom。

在容器配置中添加以下目录映射：

容器内路径：/data

NAS 本地路径：/mnt/data/calcom（或你自己创建的路径）

这样可以确保即使容器重启，你的所有数据都不会丢失。

### 环境变量设置

Cal.com 需要配置一些必要的环境变量才能正常运行。

**必须设置的环境变量：**

环境变量 | 说明 | 示例值

---------|------|--------

`DATABASE\_URL` | PostgreSQL 数据库连接地址 | `postgresql://postgres:password@db:5432/calendso`

`NEXTAUTH\_SECRET` | 用于加密会话的密钥 | 使用 `openssl rand -base64 32` 生成

`NEXTAUTH\_URL` | 应用访问地址 | `http://localhost:3000`

**可选配置的环境变量：**

环境变量 | 说明 | 默认值

---------|------|--------

`NEXT\_PUBLIC\_WEBAPP\_URL` | Web 应用 URL | `http://localhost:3000`

`NEXT\_PUBLIC\_WEBSITE\_URL` | 网站 URL | `http://localhost:3000`

`CALCOM\_LICENSE\_KEY` | 企业版许可证密钥 | 空

如果你不需要使用企业版功能，其他环境变量可以保持默认，直接点击下一步启动容器即可。

### 访问应用

容器启动成功后，打开浏览器访问：

```
http://你的NAS地址:3000
```

例如：`http://192.168.1.100:3000`

首次访问时，你需要创建管理员账户。按照界面提示完成初始化设置后，就可以开始使用 Cal.com 来管理你的日程和会议预约了。

### 部署完成

到此为止，Cal.com 已经成功部署在你的 NAS 上了。你现在拥有了一个完全属于自己的、功能强大的日程管理系统。所有的数据都存储在本地，完全由你掌控。

如果后续需要在外网访问，记得配合内网穿透工具使用，这样无论身在何处都能管理你的日程。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access>
