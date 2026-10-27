---
title: "高效文本分享平台Hasty Paste II，Docker快速部署NAS私有服务，支持内网穿透远程访问"
date: 2025-10-16
description: "Hasty Paste II是开源文本分享平台，支持代码、笔记快速分享。提供私密、隐链、公开多种可见性选项，支持Markdown和代码高亮展示。内置用户账户系统、SSO登录、黑暗主题切换等功能。Docker一键部署，适合开发者和普通用户快速安全地分享信息内容。"
canonical: https://www.passnat.com/posts/hasty-paste-ii-docker-nas-private-service-intranet-penetration
---

Hasty Paste II - 高效便捷的文本分享平台

## 项目介绍

Hasty Paste II 是一个功能强大的开源文本分享服务，旨在让用户能够快速、安全地分享代码片段、笔记和其他文本内容。该项目采用现代化的技术栈构建，提供了灵活的隐私控制、多样化的内容呈现方式以及完整的用户管理系统。

无论你是开发者需要分享代码，还是普通用户想要快速分享信息，Hasty Paste II 都能满足你的需求。它支持多种可见性选项（私密、隐链、公开），内容可以以纯文本、Markdown 或代码高亮的形式展示，并且支持自定义分享链接或随机生成。此外，项目还内置了用户账户系统、SSO 登录、黑暗/浅色主题切换等实用功能。

项目地址：[点我跳转](https://github.com/enchant97/hasty-paste)

## 部署安装

如果你想在外网也能访问这个服务，建议配置内网穿透方案。你可以考虑使用 [帕斯内网穿透](https://passnat.com)，先注册账号备好。详细的配置步骤可以参考 [这份完整攻略](https://www.passnat.com/posts/p)，涵盖了全平台的上手方法。

### Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，点击'本地镜像'→'添加镜像'→'从 URL 添加'。

在镜像 URL 栏中填入以下地址：

```
enchant97/hasty-paste:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

### 容器配置步骤

**1. 基础设置**

勾选'开机自动启动'选项，确保服务在系统重启后能够自动恢复运行。

**2. 端口映射**

Hasty Paste II 默认运行在容器内的 **8000 端口**。你需要将其映射到主机上的某个可用端口（例如 8000）。

如果你的主机上 8000 端口已被占用，可以修改为其他端口号（如 8001、8080 等），但容器内部的端口保持不变为 8000。

**3. 存储配置**

如果你需要持久化保存粘贴内容和用户数据，建议创建一个本地文件夹（例如 `/data/hasty-paste`）并将其映射到容器内的 `/app/data` 目录。这样即使容器重启，你的数据也不会丢失。

**4. 环境变量设置**

以下是 Hasty Paste II 支持的环境变量配置：

**必须设置：**

DATABASE\_URL: 数据库连接字符串（如使用 SQLite，可设置为 `sqlite:///app/data/db.sqlite`）

**可选设置（根据需求配置）：**

SECRET\_KEY: 应用密钥（用于加密会话，建议设置一个复杂的随机字符串）

ADMIN\_EMAIL: 管理员邮箱地址

PASTE\_EXPIRY\_DAYS: 粘贴过期天数（默认 30 天）

MAX\_PASTE\_SIZE: 单个粘贴的最大大小（单位：字节）

ENABLE\_REGISTRATION: 是否允许用户注册（true/false）

其他参数保持默认值即可，无需额外配置。

**5. 启动容器**

完成上述配置后，点击'下一步'并确认启动。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:8000
```

例如，如果你的 NAS IP 为 `192.168.1.100`，则访问 `http://192.168.1.100:8000` 即可打开 Hasty Paste II 的界面。

首次访问时，你可以选择'匿名模式'快速开始使用，或者注册账户以获得更多功能。

## 总结

通过以上步骤，你就能快速在自己的 NAS 或 Docker 环境中部署 Hasty Paste II。这是一个功能完整、易于使用的文本分享解决方案，特别适合团队协作和个人知识积累。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/hasty-paste-ii-docker-nas-private-service-intranet-penetration>
