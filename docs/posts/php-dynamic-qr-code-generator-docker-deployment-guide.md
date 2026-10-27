---
title: "PHP动态二维码生成系统部署指南，Docker一键启动内网穿透远程访问管理平台"
date: 2025-10-16
description: "PHP Dynamic QR Code是功能强大的动态二维码生成管理系统。支持12+种二维码类型，提供实时统计、批量操作、自定义选项等功能。基于AdminLTE开发，界面简洁响应式。可快速部署，支持Docker容器化部署。适合个人和企业级二维码应用需求。"
canonical: https://www.passnat.com/posts/php-dynamic-qr-code-generator-docker-deployment-guide
---

## PHP Dynamic QR Code - 动态二维码生成系统部署指南

### 项目介绍

PHP Dynamic QR Code 是一个功能强大的二维码生成和管理系统，专为需要大规模处理动态二维码的用户设计。该项目基于 AdminLTE 和核心 PHP 开发，提供了一个简洁、响应式且用户友好的管理界面。

无论是创建动态二维码还是静态二维码，该系统都能轻松胜任。支持生成包括文本、邮件、电话、短信、WhatsApp、Skype、位置、名片、日历事件、书签、WiFi 和支付（PayPal、Bitcoin）等多种类型的二维码。系统内置高级统计面板，可实时追踪二维码创建数量和扫描情况，支持批量下载、批量删除等便捷操作。

[点我跳转](https://github.com/giandonatoinverso/PHP-Dynamic-Qr-code)

项目地址：[点我跳转](https://github.com/giandonatoinverso/PHP-Dynamic-Qr-code)

### 部署安装

如果需要在外网也能访问该系统，建议配置内网穿透服务。可以考虑使用 [帕斯内网穿透](https://passnat.com) 等专业工具，先完成账号注册。具体操作步骤可参考 [内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

#### 使用 Docker Compose 快速部署

这是最简单的部署方式，只需一条命令即可启动整个系统。

#### 第一步：启动容器

在你的 NAS 设备或服务器上执行以下命令：

```bash
docker compose build --no-cache && docker compose up -d
```

#### 第二步：访问系统

部署完成后，打开浏览器访问：`http://localhost:80`

使用以下默认账号登录：

用户名：`superadmin`

密码：`superadmin`

#### 第三步：端口配置

如果 80 端口已被占用，你可以修改映射端口。系统默认使用容器内的 80 端口，如需修改外部访问端口，可在启动命令中调整 `-p` 参数。

#### 第四步：数据持久化

为了确保数据安全，建议将数据库文件映射到本地存储。在启动前创建一个本地文件夹用于存储数据库文件，这样即使容器重启也不会丢失数据。

#### 环境变量设置

该项目无需特殊环境变量配置，使用默认设置即可正常运行。

### 功能特色速览

✨ 动态二维码管理 - 创建、修改、删除二维码，随时下载

✨ 多类型二维码支持 - 支持 12+ 种二维码类型

✨ URL 短链功能 - 内置 URL 缩短和重定向功能

✨ 高级自定义选项 - 6 种图片格式、自定义颜色、4 个精度等级、10 种尺寸

✨ 完整统计面板 - 实时展示二维码创建数和扫描数据

✨ 批量操作 - 支持批量下载和批量删除

✨ 双层权限管理 - 内置两个访问权限等级的控制面板

### 总结

通过上述步骤，你就能在几分钟内部署一个功能完整的二维码管理系统。无论是个人使用还是企业级应用，PHP Dynamic QR Code 都能满足你的需求。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/php-dynamic-qr-code-generator-docker-deployment-guide>
