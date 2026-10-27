---
title: "自托管2FA管理器2FAuth详细部署指南，NAS Docker快速启动安全认证"
date: 2025-10-16
description: "2FAuth是自托管网页版双因素认证管理器，提供Google Authenticator替代方案。支持桌面移动设备，数据存储自己的数据库中，避免手机丢失风险。采用现代化设计界面简洁，支持TOTP、HOTP、Steam Guard等多种算法，可通过Docker快速部署。提供账户分组、数据加密、安全密钥等功能。"
canonical: https://www.passnat.com/posts/self-hosted-2fa-manager-2fauth-deployment-guide-nas-docker-quick-start-security-authentication
---

## 项目介绍

2FAuth 是一个自托管的网页版双因素认证（2FA）管理器，是 Google Authenticator 等 OTP 生成器的优秀替代方案。它采用现代化的网页设计，支持桌面和移动设备，让你能够在任何设备上轻松管理和生成安全码。

这个项目的核心价值在于：你可以将所有的 2FA 账户数据存储在自己的数据库中，避免了手机丢失导致所有 2FA 账户丧失的风险。相比于在多个设备间安装认证器应用，2FAuth 提供了一个统一的管理平台，界面简洁优雅，不会像传统认证器那样同时显示所有账户并伴随压力山大的倒计时。无论你是在办公室还是在家中，都可以快速获取所需的安全码，大大提升了工作效率。

项目地址： [点我跳转](https://github.com/Bubka/2FAuth)

## 部署安装

如果你需要在外网也能访问 2FAuth，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com)

注册账号，这样就能随时随地安全地访问你的 2FA 管理器。详细的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)

帕斯内网穿透全平台快速入门指南。

### Docker 单命令快速启动

2FAuth 提供了最简便的 Docker 部署方式，只需一条命令即可启动：

打开你的 NAS 设备或任何支持 Docker 的系统，在本地镜像中添加镜像。

镜像地址填写：`2fauth/2fauth:latest`

等待镜像下载完成后，点击启动按钮启动容器。

**端口映射配置：**

将容器内的 `8000` 端口映射到你想要的宿主机端口（比如 `8000:8000`）。如果提示端口被占用，你可以修改前面的数字，比如改为 `8001:8000`，这样就能通过 `http://你的IP:8001` 访问 2FAuth。重点是要保证容器内的端口始终为 `8000`。

**存储配置：**

创建一个本地文件夹用于存储 2FAuth 的数据，比如 `/data/2fauth`，然后将其映射到容器内的 `/2fauth` 目录。这样可以确保你的 2FA 数据在容器重启后不会丢失。

**环境变量设置：**

必须设置的环境变量：
**APP\_KEY**：加密密钥，必须是 32 个字符的随机字符串。这个值非常重要，请妥善保管，如果丢失将无法解密已有数据。
**SITE\_OWNER**：你的邮箱地址，用于接收系统通知。

建议设置的环境变量：
**APP\_NAME**：应用名称，默认为 `2FAuth`
**APP\_TIMEZONE**：时区设置，默认为 `UTC`
**APP\_URL**：应用的外部访问地址，用于 WebAuthn 功能正常工作，比如 `http://你的IP:8000`
**APP\_DEBUG**：调试模式，生产环境建议设为 `false`

其他环境变量（可选）：
**APP\_ENV**：运行环境，本地测试可设为 `local`，生产环境设为 `production`
**IS\_DEMO\_APP**：演示模式，默认 `false`
**LOG\_CHANNEL**：日志存储方式，默认 `daily`
**LOG\_LEVEL**：日志级别，默认 `notice`
**DB\_DATABASE**：数据库路径，默认 `/srv/database/database.sqlite`

设置完成后点击启动，容器将自动创建并运行。

**访问应用：**

创建成功后，打开浏览器访问 `http://你的IP:8000`，你将看到 2FAuth 的登录界面。首次访问时需要创建一个用户账户，然后就可以开始添加你的 2FA 账户了。

### Docker Compose 完整配置

如果你想使用 Docker Compose 进行更灵活的配置管理，可以参考以下配置文件：

复制下列的配置文件，保存为 `docker-compose.yml`：

```yaml
version: "3"
services:
  2fauth:
    image: 2fauth/2fauth
    container_name: 2fauth
    volumes:
      - ./2fauth:/2fauth
    ports:
      - 8000:8000/tcp
    environment:
      - APP_NAME=2FAuth
      - APP_ENV=local
      - APP_TIMEZONE=UTC
      - APP_DEBUG=false
      - SITE_OWNER=mail@example.com
      - APP_KEY=SomeRandomStringOf32CharsExactly
      - APP_URL=http://localhost:8000
      - IS_DEMO_APP=false
      - LOG_CHANNEL=daily
      - LOG_LEVEL=notice
      - DB_DATABASE=/srv/database/database.sqlite
      - CACHE_DRIVER=file
      - SESSION_DRIVER=file
      - MAIL_MAILER=log
      - THROTTLE_API=60
      - LOGIN_THROTTLE=5
      - AUTHENTICATION_GUARD=web-guard
```

在 NAS 或服务器上创建一个文件夹，将上述配置保存为 `docker-compose.yml`，然后在该目录下执行：

```
docker-compose up -d
```

容器将在后台启动运行。

**配置说明：**

SITE\_OWNER：请修改为你的邮箱地址
APP\_KEY：这是非常重要的加密密钥，强烈建议修改为一个安全的 32 字符随机字符串
APP\_URL：修改为你实际的访问地址，比如 `http://192.168.1.100:8000` 或你的域名
其他参数保持默认即可

如果 8000 端口被占用，可以修改 `ports` 中的第一个数字，比如改为 `8001:8000`。

**启动成功：**

执行命令后，容器将自动启动。打开浏览器访问 `http://你的IP:8000`，即可看到 2FAuth 的界面。

## 功能特性

部署完成后，你就可以开始使用 2FAuth 的强大功能了：

账户管理：通过扫描二维码快速添加 2FA 账户，也支持手动输入密钥
分组整理：使用分组功能对你的 2FA 账户进行分类管理
多种算法支持：支持 TOTP、HOTP 和 Steam Guard 等多种安全码生成方式
数据加密：敏感数据可选择加密存储，保护你的隐私
安全密钥：支持 Yubikey 等硬件安全密钥登录
自动登出：支持设置不活动自动登出时间，提升安全性
数据导入：支持从 Google Authenticator、Aegis Auth 等应用导入账户数据

## 总结

2FAuth 是一个功能完整、安全可靠的自托管 2FA 管理解决方案。通过 Docker 的快速部署，你可以在几分钟内搭建属于自己的认证器管理系统，从此告别手机丢失导致的 2FA 账户丧失风险。无论你是技术爱好者还是隐私保护倡导者，2FAuth 都是一个值得尝试的项目。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/self-hosted-2fa-manager-2fauth-deployment-guide-nas-docker-quick-start-security-authentication>


## 相关文章

- [极简网页仪表盘SimpleDash，本地快速部署，内网穿透远程访问，YAML配置自定义链接管理](simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config.md)
- [开源网站分析平台Matomo自建部署教程 NAS Docker快速安装配置指南](matomo-open-source-analytics-platform-nas-docker-deployment-guide.md)
- [统一网盘聚合管理工具PanIndex，多云存储跨平台部署，内网穿透远程访问无限资源](panindex-unified-cloud-storage-aggregation-tool-multi-cloud-cross-platform-deployment.md)
- [私人云盘Filestash部署完全指南，NAS一键Docker启动，支持多云存储聚合管理](private-cloud-storage-filestash-deployment-guide-nas-docker-one-click-multi-cloud-storage-aggregation.md)
- [开源自主翻译API LibreTranslate Docker部署指南 NAS一键搭建私人翻译服务](libre-translate-docker-nas-deployment-guide.md)
