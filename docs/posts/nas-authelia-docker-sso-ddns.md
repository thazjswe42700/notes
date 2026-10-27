---
title: "NAS部署Authelia身份认证服务，Docker快速部署SSO单点登录系统，内网穿透远程安全访问"
date: 2025-10-16
description: "Authelia是开源身份认证与授权服务器，提供双因素认证和单点登录功能。支持安全密钥、一次性密码等多种验证方式，与Traefik、Nginx、Caddy无缝集成。本文详细介绍Docker快速部署在NAS上的步骤，包括镜像配置、端口映射、环境变量设置等，帮助运维人员和家庭用户轻松实现多应用统一身份认证管理。"
canonical: https://www.passnat.com/posts/nas-authelia-docker-sso-ddns
---

## Authelia 开源身份认证服务器部署指南

### 项目介绍

Authelia 是一个开源的身份认证与授权服务器，为你的应用程序提供强大的双因素认证和单点登录（SSO）功能。它通过Web门户与反向代理配合工作，能够对请求进行允许、拒绝或重定向操作，是保护内部应用安全的理想方案。

无论你是运维人员还是家庭NAS用户，Authelia都能让你轻松为多个应用统一管理身份认证。支持多种二次验证方式，包括安全密钥、一次性密码和推送通知，让你的应用访问更加安全可靠。项目已获得 OpenID Connect 认证，支持与 Traefik、Nginx、Caddy 等主流反向代理无缝集成。

[点我跳转](https://github.com/authelia/authelia)

项目地址：

### 部署安装

如果你需要在外网也能访问部署的 Authelia 服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的操作指南可以 [点我查看](https://www.passnat.com/posts/p) 帕斯内网穿透全平台快速上手教程。

#### 使用 Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备的容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。在镜像地址栏填入：

```
authelia/authelia:latest
```

等待镜像下载完成后，点击 **启动** 按钮启动容器。

在容器启动配置中，勾选 **开机自动启动** 确保服务持久运行。

#### 端口映射配置

Authelia 默认运行在容器内的 **9091** 端口。在 NAS 容器管理中配置端口映射时，建议设置为：

容器内端口：9091（必须保持不变）

宿主机端口：9091（如果该端口被占用，可修改为其他端口如 8091）

#### 存储和配置

Authelia 需要持久化存储来保存配置和用户数据。建议在 NAS 中创建一个专用文件夹（如 */data/authelia*），并将其映射到容器内的 */config* 目录。

#### 环境变量设置

必须设置的环境变量：

```
AUTHELIA_JWT_SECRET: 你的JWT密钥（建议使用随机字符串，至少32位）
AUTHELIA_SESSION_SECRET: 会话密钥（建议使用随机字符串，至少32位）
```

可选设置的环境变量：

```
AUTHELIA_THEME: 主题选择（light 或 dark，默认为 light）
AUTHELIA_LOG_LEVEL: 日志级别（debug、info、warn、error，默认为 info）
```

其他配置项可以通过挂载的配置文件进行更细致的调整。

#### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:9091
```

即可看到 Authelia 的登录门户界面。首次访问时，你可以根据配置文件中定义的用户进行登录。

### 总结

通过以上步骤，你已经成功在 NAS 上部署了 Authelia 认证服务。现在你可以为家中的多个应用配置反向代理，让它们都通过 Authelia 进行身份验证。这样一来，你只需要记住一个账号就能访问所有应用，同时享受企业级的安全防护。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/nas-authelia-docker-sso-ddns>
