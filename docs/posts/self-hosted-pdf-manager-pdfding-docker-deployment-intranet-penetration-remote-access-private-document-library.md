---
title: "自托管PDF管理器PdfDing Docker快速部署，内网穿透远程访问私人文档库"
date: 2025-10-16
description: "PdfDing是功能强大的自托管PDF管理查看器，基于Django框架构建，集成Mozilla PDF.js提供流畅浏览体验。所有数据完全保存在您的服务器，保护隐私安全。支持深色模式、多彩主题、标签分类、批量上传、自动记忆阅读位置、OIDC单点登录、S3备份、分享链接密码保护等功能。响应式设计，简洁美观界面，跨设备无缝使用。Docker一键部署,适合注重数据控制的用户。"
canonical: https://www.passnat.com/posts/self-hosted-pdf-manager-pdfding-docker-deployment-intranet-penetration-remote-access-private-document-library
---

## 项目介绍

PdfDing 是一个功能强大的自托管 PDF 管理和查看器，采用现代化的 Web 应用架构设计。它基于 Django 框架构建，结合 Mozilla 的 PDF.js 提供流畅的浏览器内 PDF 查看体验，支持跨设备无缝使用。

这是一款专为注重隐私和数据控制的用户设计的解决方案。所有数据完全保存在您自己的服务器上，您拥有对数据的完全控制权和隐私保护。PdfDing 具有简洁直观的用户界面，支持深色模式和多种彩色主题，让每个用户都能根据自己的喜好自定义应用外观。

项目地址： [点我跳转](https://codeberg.org/mrmn/PdfDing)

## 部署安装

想要在外网也能访问你的 PdfDing？推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。具体的操作步骤可以 [参考这里](https://www.passnat.com/posts/p) 了解内网穿透的全平台使用指南。

### 使用 Docker 快速启动

打开你的 NAS 设备或 Docker 环境，执行下面这个命令即可一键启动 PdfDing：

```bash
docker run --name pdfding \
    -p 8000:8000 \
    -v sqlite_data:/home/nonroot/pdfding/db \
    -v media:/home/nonroot/pdfding/media \
    -e HOST_NAME=127.0.0.1 \
    -e SECRET_KEY=your_secret_key_here \
    -e CSRF_COOKIE_SECURE=FALSE \
    -e SESSION_COOKIE_SECURE=FALSE \
    -d \
    mrmn/pdfding:latest
```

等待容器启动完成后，打开浏览器访问 http://你的IP:8000 就能看到 PdfDing 的登录界面了。

**注意：** 如果你使用 SELinux，需要在卷挂载后添加 :Z 标记，例如 sqlite\_data:/home/nonroot/pdfding/db:Z

### 环境变量配置说明

**必须设置的环境变量：**

HOST\_NAME: 你的 PdfDing 访问地址（例如：pdfding.com 或 127.0.0.1）

SECRET\_KEY: 用于保护签名数据的密钥，应该是一个较长的随机字符串

**可选设置的环境变量：**

HOST\_PORT: 服务端口，默认为 8000（如果端口冲突可修改）

DEFAULT\_THEME: 默认主题，可选值为 light 或 dark，默认为 light

DEFAULT\_THEME\_COLOR: 默认主题颜色，可选值为 green、blue、gray、red、pink、orange，默认为 green

ACCOUNT\_EMAIL\_VERIFICATION: 是否需要邮箱验证，默认为 TRUE

DISABLE\_USER\_SIGNUP: 是否禁用用户注册，默认为 FALSE

其他不需要填写的环境变量可保持默认值。

### 存储卷说明

sqlite\_data: 存储 SQLite 数据库文件，不可删除

media: 存储用户上传的 PDF 文件，不可删除

这两个卷会自动创建，无需手动操作。

### 创建管理员账户

容器启动后，如果需要创建管理员账户，进入容器执行以下命令：

```bash
docker exec pdfding python pdfding/manage.py make_admin -e admin@pdfding.com
```

将 admin@pdfding.com 替换为你想要设置为管理员的邮箱地址。

### 高级功能配置

**启用 SSO 单点登录（OIDC）：**

如果你想集成现有的身份认证系统，可以配置 OIDC 环境变量：

```
OIDC_ENABLE=TRUE
OIDC_CLIENT_ID=pdfding
OIDC_CLIENT_SECRET=your_client_secret
OIDC_AUTH_URL=https://your-auth-server/.well-known/openid-configuration
OIDC_PROVIDER_NAME=Authelia
```

**启用自动备份到 S3：**

支持定期备份到 S3 兼容的存储服务：

```
BACKUP_ENABLE=TRUE
BACKUP_SCHEDULE=0 2 * * *
BACKUP_ENDPOINT=your-s3-endpoint.com
BACKUP_ACCESS_KEY=your_access_key
BACKUP_SECRET_KEY=your_secret_key
BACKUP_SECURE=FALSE
```

**启用加密备份：**

```
BACKUP_ENCRYPTION_ENABLE=TRUE
BACKUP_ENCRYPTION_PASSWORD=your_encryption_password
BACKUP_ENCRYPTION_SALT=your_salt
```

**邮件通知配置：**

如需发送邮件通知（如密码重置、邮箱验证），配置 SMTP：

```
EMAIL_BACKEND=SMTP
SMTP_HOST=your-smtp-server.com
SMTP_PORT=587
SMTP_USER=your_email@example.com
SMTP_PASSWORD=your_password
SMTP_USE_TLS=TRUE
```

## 功能特性

**✨ 核心功能：**

浏览器内无缝 PDF 查看体验，支持桌面和移动设备

使用标签组织和分类 PDF 文件

支持批量上传 PDF

自动记忆阅读位置，下次打开继续阅读

响应式设计，界面简洁美观

**🔒 安全与隐私：**

支持用户邮箱验证

OIDC 单点登录集成

支持分享链接、二维码分享

分享链接可设置密码保护、访问次数限制和过期时间

**💾 备份与恢复：**

自动定期备份到 S3 兼容存储

支持加密备份保护隐私

数据恢复功能

**🎨 用户体验：**

深色模式支持

多种彩色主题可选

PDF 反色阅读模式

简单易用的管理后台

## 部署完成

至此，PdfDing 已经成功部署完毕。你现在可以访问 http://你的IP:8000 来使用这个强大的 PDF 管理工具了。

首次访问时，你需要注册账户。如果启用了邮箱验证，请检查邮箱完成验证。之后就可以上传、管理和查看你的 PDF 文件了。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/self-hosted-pdf-manager-pdfding-docker-deployment-intranet-penetration-remote-access-private-document-library>


## 相关文章

- [PdfDing：NAS 上的 PDF 阅读库，Docker 部署 + 内网穿透](pdfding-nas-docker-pdf-reader-progress-highlights-intranet-penetration.md)
- [自建 BentoPDF：飞牛 NAS Docker 部署隐私优先的 PDF 工具箱，内网穿透外网访问](bentopdf-self-hosted-pdf-toolkit-nas-docker-deployment-intranet-penetration.md)
- [PDFMathTranslate 整篇翻译：NAS Docker 部署 + 内网穿透](pdfmathtranslate-nas-docker-pdf-translate-formula-layout-intranet-penetration.md)
- [私密PDF处理工具Stirling本地部署指南，Docker容器化方案50+功能完整教程](stirling-pdf-local-deployment-guide-docker-containerization-50-plus-features-complete-tutorial.md)
- [Lookscanned：NAS Docker 部署，电子 PDF 一键变扫描件](lookscanned-nas-docker-pdf-to-scanned-image-intranet-penetration.md)
