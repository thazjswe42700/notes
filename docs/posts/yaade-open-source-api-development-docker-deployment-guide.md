---
title: "自建私人API开发环境，Yaade开源协作平台Docker快速部署指南"
date: 2025-10-16
description: "Yaade是开源自托管API开发环境，支持多人协作和权限控制。相比Postman云端服务，所有敏感数据存储在自己服务器上更安全。提供REST、WebSocket、脚本执行、OpenAPI导入等功能，适合个人开发者和团队使用。支持Docker一键部署，内置浏览器扩展绕过CORS限制。"
canonical: https://www.passnat.com/posts/yaade-open-source-api-development-docker-deployment-guide
---

## 项目介绍

Yaade 是一款开源、自托管、多人协作的 API 开发环境。它为开发团队提供了一个安全、可控的 API 测试和管理平台，让你可以在自己的服务器上完全掌控所有数据。

相比于 Postman 这类云端服务，Yaade 的最大优势在于——所有敏感信息都存储在你的服务器上，不会泄露到第三方。支持多用户管理、权限控制、API 集合共享，还内置了脚本执行、定时任务、OpenAPI 导入等强大功能。无论是个人开发者还是团队协作，Yaade 都能为你提供一个开箱即用的 API 开发利器。

项目地址：[点我跳转](https://github.com/EsperoTech/yaade)

## 部署安装

如果你需要在公网环境下访问 Yaade，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备后用。具体的操作步骤可以参考 [内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)。

### Docker 快速部署

打开你的 NAS 设备或服务器，使用以下命令一键启动 Yaade：

```bash
docker volume create yaade
docker run -d --restart=always -p 9339:9339 -e YAADE_ADMIN_USERNAME=admin -v yaade:/app/data --name yaade esperotech/yaade:latest
```

命令执行完成后，稍等片刻让容器完全启动。

**端口说明**：

容器对外暴露的端口为 **9339**，这是你访问 Yaade 的入口。如果你的服务器上该端口已被占用，可以修改命令中的 **-p 9339:9339** 为 **-p 你要用的端口:9339**，前面的数字是外部访问端口，后面的是容器内端口，保持容器内端口为 **9339** 不变。

**存储持久化**：

命令中的 **-v yaade:/app/data** 会自动创建一个名为 **yaade** 的数据卷，用于持久化存储所有的 API 集合、用户信息和配置数据。即使容器重启也不会丢失数据。

**环境变量设置**：

必须设置：

**YAADE\_ADMIN\_USERNAME**：管理员用户名，默认为 **admin**（命令中已设置）

其他配置项均使用默认值，无需额外设置。

**首次登录**：

部署完成后，在浏览器中访问 **http://你的服务器IP:9339**（如果修改了端口，请使用对应的端口号）。

使用以下凭证登录：

用户名：**admin**

密码：**password**

*重要*：首次登录后，请立即进入 ⚙️ 设置 > 账户，修改默认密码为强密码，以保护你的 API 数据安全。

### 浏览器扩展配置

为了获得最佳体验，建议在你的本地浏览器上安装 Yaade 浏览器扩展。扩展可以充当代理，帮助你绕过 CORS 限制。

Chrome 用户：在 Chrome 网上应用店搜索并安装 Yaade 扩展

Firefox 用户：在 Firefox 附加组件中搜索并安装 Yaade 扩展

安装完成后，打开扩展并输入你的 Yaade 服务器地址（例如 **https://yaade.example.com/** 或 **http://192.168.1.100:9339/**），之后所有来自 Yaade 标签页的请求都会自动通过扩展代理。

## 总结

通过以上步骤，你已经成功部署了一个功能完整的 API 开发环境。Yaade 支持 REST、WebSocket、Markdown 文档、脚本执行、OpenAPI 导入等丰富功能，可以满足从个人开发到团队协作的各种需求。所有数据都安全地存储在你自己的服务器上，让你可以放心地管理敏感的 API 信息。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/yaade-open-source-api-development-docker-deployment-guide>
