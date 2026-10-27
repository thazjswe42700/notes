---
title: "Nginx Proxy Manager Docker部署指南，内网穿透反向代理网关搭建教程配置"
date: 2025-10-16
description: "Nginx Proxy Manager是功能强大的反向代理管理工具，基于Docker构建，提供美观易用的Web界面。支持Let's Encrypt免费SSL证书、自定义证书、访问控制等高级功能，无需命令行即可轻松管理域名转发和SSL配置，是自托管爱好者必备的反向代理解决方案。"
canonical: https://www.passnat.com/posts/nginx-proxy-manager-docker-deployment-guide-intranet-penetration-reverse-proxy-gateway-setup-tutorial-configuration
---

## 项目介绍

Nginx Proxy Manager 是一个功能强大的反向代理管理工具，基于Docker构建，专为家庭网络和自托管服务而设计。它提供了一个美观易用的Web管理界面，让即使没有Nginx或SSL证书知识的用户也能轻松管理域名转发、SSL证书配置和访问控制。

无需复杂的命令行配置，只需通过图形化界面即可快速搭建家庭服务网关。支持Let's Encrypt免费SSL证书、自定义证书上传、访问列表、HTTP基础认证等高级功能，是自托管爱好者必备的反向代理解决方案。

项目地址： [点我跳转](https://github.com/jc21/nginx-proxy-manager)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务，推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的配置步骤可以 [点击这里查看](https://www.passnat.com/posts/p)内网穿透全平台快速上手指南。

### 使用 Docker Compose 快速部署

复制下列配置文件，保存为 'docker-compose.yml'：

```yaml
services:
  app:
    image: 'docker.io/jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

打开你的NAS设备或任何支持Docker的服务器

在设备上找到Docker Compose功能，将上述配置文件粘贴进去。

点击启动按钮，等待容器启动完成。建议勾选开机自动启动选项，这样重启后服务会自动恢复运行。

**端口映射说明：**

'80' - HTTP访问端口（用于获取Let's Encrypt证书验证）

'81' - 管理后台访问端口（Web UI界面）

'443' - HTTPS访问端口（安全的反向代理转发）

如果你的服务器上这些端口已被占用，可以修改左侧的端口号（冒号前面的数字），但容器内部端口（冒号后面的数字）需要保持不变。

**存储挂载说明：**

'./data' - 保存应用数据和配置信息，建议提前创建此文件夹

'./letsencrypt' - 存放SSL证书文件，确保持久化保存以避免证书丢失

环境变量设置：

这个项目使用默认配置即可正常运行，无需额外配置环境变量。所有功能都可以在启动后通过Web界面进行管理。

点击启动即可完成部署。

### 访问管理界面

容器启动成功后，在浏览器中访问：

```
http://你的设备IP:81
```

使用默认账户登录：

```
邮箱：admin@example.com
密码：changeme
```

首次登录后系统会强制要求你修改管理员信息和密码，请立即更新为安全的凭证。

### 配置反向代理

登录后台后，你可以：

添加代理主机，将外部域名转发到内网服务

为每个域名自动申请和配置Let's Encrypt免费SSL证书

设置访问控制列表和HTTP基础认证

查看详细的审计日志

到这里部署就全部完成了，你现在拥有了一个功能完整的反向代理网关！

**本篇教程结束**

---

原文：<https://www.passnat.com/posts/nginx-proxy-manager-docker-deployment-guide-intranet-penetration-reverse-proxy-gateway-setup-tutorial-configuration>
