---
title: "AgenDAV 网页日历客户端 NAS 自建部署指南，内网穿透远程访问日程管理系统"
date: 2025-10-16
description: "AgenDAV是功能强大的CalDAV网页客户端，提供现代化AJAX界面管理日历和共享日历。基于PHP构建，支持多种CalDAV服务器连接，界面友好操作直观。本文详细介绍AgenDAV在NAS上的Docker快速部署方法，包括镜像添加、端口配置、存储设置和环境变量配置等完整步骤，让用户轻松搭建自建日程管理系统。"
canonical: https://www.passnat.com/posts/agendav-nas-deployment-guide-calendar-scheduling
---

## 项目介绍

AgenDAV 是一个功能强大的 CalDAV 网页客户端，它提供了一个现代化的 AJAX 交互界面，让用户能够轻松管理自己的日历和共享日历。无论你是个人用户还是团队协作，AgenDAV 都能为你提供一个简洁高效的日程管理解决方案。

这个项目基于 PHP 构建，支持连接多种 CalDAV 服务器（如 Baïkal、DAViCal、Radicale 等），让你可以在任何地方通过浏览器访问和管理你的日历。界面友好，操作直观，即使是技术小白也能快速上手。

项目地址：[点我跳转](https://github.com/agendav/agendav)

## 部署安装

如果你需要在外网也能访问这个项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速入门指南。

### 使用 Docker 快速部署

打开你的 NAS 设备（如飞牛 NAS 或其他支持 Docker 的 NAS）

点击**本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：`ghcr.io/nagimov/agendav-docker:latest`（这是社区提供的非官方镜像）

等待镜像下载完成后，点击**启动**按钮启动容器

勾选**开机自动启动**后进入下一步

### 端口配置

在端口设置页面，请配置以下映射：

容器内端口：80（AgenDAV 的默认服务端口）

主机端口：80（或根据你的需求修改，如 8080）

如果提示端口被占用，你可以修改主机端口的数字，但要保持格式正确。例如可以改为 `8080:80`

### 存储配置

AgenDAV 需要持久化存储数据库和配置文件，请在存储位置设置中：

创建一个文件夹用于存储数据（例如 `/agendav/data`）

将此文件夹映射到容器内的 `/var/www/html` 路径

这样可以确保你的日历数据和配置在容器重启后不会丢失

### 环境变量设置

**必须设置：**

CALDAV\_SERVER：你的 CalDAV 服务器地址（例如：http://caldav-server.com）

**可选设置（按需配置）：**

DB\_TYPE：数据库类型（默认：sqlite）

DB\_HOST：数据库主机地址

DB\_NAME：数据库名称

DB\_USER：数据库用户名

DB\_PASS：数据库密码

LANGUAGE：界面语言设置

其他配置项保持默认即可，直接进入下一步启动容器

### 访问应用

创建成功后，打开浏览器访问：`http://你的NAS地址:80`（或你设置的其他端口）

例如：`http://192.168.1.100:80` 或 `http://192.168.1.100:8080`

此时你应该能看到 AgenDAV 的登录界面，使用你的 CalDAV 账号登录即可开始管理你的日历

### 总结

通过以上几个简单步骤，你就可以在自己的 NAS 上快速部署一个功能完整的日历管理系统。AgenDAV 的稳定性和兼容性都很不错，特别适合需要自建日程管理服务的用户。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/agendav-nas-deployment-guide-calendar-scheduling>
