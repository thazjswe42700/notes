---
title: "个人应用书签仪表板DashMachine Docker部署教程 自托管NAS远程访问内网穿透配置方案"
date: 2025-10-16
description: "DashMachine是基于Flask的个人应用书签仪表板，专为自托管用户设计。支持Docker快速部署，提供美观易用的界面来集中管理常用应用和服务。内置丰富自定义选项，支持自定义背景、图标和配置。本文详细介绍DashMachine的部署安装步骤、Docker配置方法、初始登录信息和功能说明。"
canonical: https://www.passnat.com/posts/personal-bookmark-dashboard-dashmachine-docker-deploy-nas-self-hosting-remote-access-intranet-penetration
---

## 项目介绍

DashMachine 是一款功能丰富的个人应用书签仪表板，专为自托管爱好者和技术用户打造。它采用 Flask + Python 3 构建，提供了一个美观、易用的界面来集中管理和快速访问你常用的各类应用和服务。无论是家庭 NAS、本地服务还是云端应用，DashMachine 都能帮你创建一个统一的入口。项目内置了丰富的自定义选项，支持自定义背景、图标和配置，让你的仪表板独具一格。

项目地址：[点我跳转](https://github.com/rmountjoy92/DashMachine)

## 部署安装

如果需要在家外也能访问你的仪表板，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它支持全平台使用。具体的配置步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### Docker 快速部署

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可一键启动：

```bash
docker create \
  --name=dashmachine \
  -p 5000:5000 \
  -v /path/to/data:/dashmachine/dashmachine/user_data \
  --restart unless-stopped \
  rmountjoy/dashmachine:latest
```

然后启动容器：

```bash
docker start dashmachine
```

**端口说明**

容器内部端口：5000

宿主机端口：5000（如果端口冲突，可修改命令中的第一个 5000 为其他端口，例如 8080:5000）

**存储位置**

将 /path/to/data 替换为你本地的实际路径，用于存储配置文件、数据库和自定义资源

例如：-v /mnt/nas/dashmachine:/dashmachine/dashmachine/user\_data

**环境变量设置**（可选）：

变量名 | 说明 | 默认值 | 是否必须

CONTEXT\_PATH | 应用访问路径前缀 | / | 否

如需在子路径下运行（例如 localhost:5000/dash），添加环境变量：

```bash
docker create \
  --name=dashmachine \
  -p 5000:5000 \
  -e CONTEXT_PATH=/dash \
  -v /path/to/data:/dashmachine/dashmachine/user_data \
  --restart unless-stopped \
  rmountjoy/dashmachine:latest
```

### **初始登录信息**

部署完成后，使用以下默认账号登录：

**用户名**：admin

**密码**：admin

**安全提示**：首次登录后请立即修改密码！

### **访问应用**

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:5000
```

即可看到 DashMachine 的仪表板界面。如果修改了端口号，请相应调整访问地址。

### **配置说明**

所有用户数据存储在映射的 /dashmachine/dashmachine/user\_data 目录中，包括：

config.ini - 应用配置文件

SQLite 数据库 - 存储书签和设置

backgrounds/ - 自定义背景图片

icons/ - 自定义应用图标

修改 config.ini 后，需要重启容器或在应用设置页面点击'保存'按钮使配置生效。自定义的背景和图标会立即生效。

---

原文：<https://www.passnat.com/posts/personal-bookmark-dashboard-dashmachine-docker-deploy-nas-self-hosting-remote-access-intranet-penetration>
