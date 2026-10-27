---
title: "家庭反向代理网关解决方案 Nginx代理管理器Docker快速部署指南"
date: 2025-10-16
description: "轻松搭建家庭网络反向代理网关。Nginx Proxy Manager 是基于 Docker 的开箱即用解决方案，提供美观管理界面、自动 SSL 证书申请、灵活转发规则、用户权限管理等功能。支持外网访问 NAS、监控系统等自建服务，内置安全防护和高级配置选项。部署简单，无需深入了解 Nginx 复杂配置。"
canonical: https://www.passnat.com/posts/home-reverse-proxy-gateway-nginx-proxy-manager-docker-deployment-guide
---

## Nginx 代理管理器 - 轻松搭建家庭网络反向代理网关

### 项目介绍

Nginx Proxy Manager 是一个开箱即用的反向代理解决方案，基于 Docker 构建，专为家庭网络和自建服务而设计。它提供了一个美观易用的管理界面，让您无需深入了解 Nginx 和 Let's Encrypt 的复杂配置，就能轻松搭建属于自己的网络网关。

无论您是想在外网访问家里的 NAS、监控系统，还是自建的各类服务，Nginx Proxy Manager 都能帮您轻松解决。它支持自动化的 SSL 证书申请与续期、域名转发、访问控制等高级功能，让您的家庭网络服务既安全又便利。

[点我跳转](https://github.com/NginxProxyManager/nginx-proxy-manager)

项目地址：

### 核心功能特性

🎨 美观的管理后台 - 基于 Tabler 设计的现代化管理界面

🔒 免费 SSL 证书 - 集成 Let's Encrypt，自动申请和续期

🌐 灵活的转发规则 - 支持域名转发、重定向、流媒体转发等多种模式

👥 用户权限管理 - 支持多用户、权限控制和操作审计日志

🛡️ 安全防护 - 内置访问列表和基础 HTTP 认证

⚙️ 高级配置 - 为超级用户提供 Nginx 高级配置选项

### 部署安装

#### 前置准备

如果您需要在外网访问这个项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) - 注册账号后即可轻松打通内外网络。详细的操作步骤可以参考 [全平台上手攻略](https://www.passnat.com/posts/p)，几分钟即可完成配置。

#### Docker Compose 快速部署

这是最推荐的部署方式，适合大多数用户。

##### 第一步：创建配置文件

在您的 NAS 或服务器上创建一个 docker-compose.yml 文件，复制以下内容：

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

##### 第二步：启动容器

打开您的 NAS 管理面板（飞牛 NAS、群晖或其他品牌），找到 Docker 或容器管理功能，将上述配置文件内容粘贴进去。

然后执行启动命令：

```
docker-compose up -d
```

##### 第三步：端口说明

80 端口：HTTP 流量（容器内外保持一致）

81 端口：管理后台访问端口（容器内外保持一致）

443 端口：HTTPS 流量（容器内外保持一致）

如果您的服务器上这些端口已被占用，可以修改左侧的端口号（冒号前的部分），例如将 81:81 改为 8081:81 表示通过 8081 端口访问管理后台。容器内部的端口（冒号后的部分）请保持不变。

##### 第四步：数据持久化

上述配置中已经包含了两个重要的数据目录映射：

./data:/data - 存储应用数据和配置

./letsencrypt:/etc/letsencrypt - 存储 SSL 证书

这些目录会自动创建，无需手动干预。

##### 第五步：首次登录

容器启动后，在浏览器中访问：

```
http://您的服务器IP:81
```

使用默认账号登录：

邮箱：admin@example.com

密码：changeme

登录后系统会强制您修改默认密码和账户信息，请妥善保管新密码。

#### 环境变量配置

该项目无需额外的环境变量配置，所有功能都已通过 docker-compose 的卷挂载和端口映射完成配置。

### 使用建议

部署完成后，您就可以通过 Nginx Proxy Manager 的管理界面轻松添加转发规则。只需简单填写源域名和目标地址，系统会自动处理 SSL 证书申请，无需任何手动操作。

对于家庭网络用户，建议按照项目文档的说明配置路由器的端口转发（80 和 443 端口），然后将您的域名 DNS 指向家庭网络的公网 IP，即可实现随时随地安全访问家里的各类服务。

### 总结

Nginx Proxy Manager 是自建家庭网络服务的必备工具。它大幅降低了反向代理的使用门槛，让任何人都能轻松搭建自己的网络网关。无论您是技术爱好者还是普通用户，都能通过简单的几步配置，享受到专业级的网络服务体验。

本篇教程结束，祝您使用愉快！

---

原文：<https://www.passnat.com/posts/home-reverse-proxy-gateway-nginx-proxy-manager-docker-deployment-guide>


## 相关文章

- [Nginx Proxy Manager Docker部署指南，内网穿透反向代理网关搭建教程配置](nginx-proxy-manager-docker-deployment-guide-intranet-penetration-reverse-proxy-gateway-setup-tutorial-configuration.md)
- [Nginx Proxy Manager：NAS Docker 统一入口 + 内网穿透](nginx-proxy-manager-nas-docker-unified-entry-intranet-penetration.md)
- [NAS部署Authelia身份认证服务，Docker快速部署SSO单点登录系统，内网穿透远程安全访问](nas-authelia-docker-sso-ddns.md)
- [自建文件服务器 GoHTTPServer Docker极简部署，NAS内网穿透远程访问文件](self-hosted-file-server-gohttpserver-docker-deployment-nas-intranet-penetration-remote-access.md)
- [Web-Check 网站体检：NAS Docker 部署 + 内网穿透](open-source-website-security-analysis-tool-web-check-docker-one-click-deployment-40-plus-deep-detection-local-run.md)
