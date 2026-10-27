---
title: "自建私有图片床Picsur，完全控制图片资源，NAS部署内网穿透远程访问"
date: 2025-10-16
description: "Picsur是自建图片托管服务，具有Imgur和Pastebin特色。支持图片上传、存储、分享、编辑和权限管理。本文提供Docker Compose快速部署指南，包含完整配置文件、环境变量说明和访问方法，帮助你在NAS上轻松搭建私人图片床，完全掌控图片数据。"
canonical: https://www.passnat.com/posts/self-hosted-picsur-private-image-bed-nas-intranet-penetration-remote-access
---

## 项目介绍

Picsur 是一个功能完整的自建图片托管服务，兼具 Imgur 和 Pastebin 的特色。它提供了一个简洁而强大的图片上传、存储和分享平台，让你可以完全掌控自己的图片资源。项目采用现代化的技术栈构建，支持多种图片格式、图片编辑功能、用户权限管理等丰富特性。

无论你是想为自己的网站搭建图片床，还是想建立一个私人的图片库，Picsur 都能满足你的需求。最重要的是，所有的图片数据完全由你自己控制，隐私性和安全性有保障。

项目地址：[点我跳转](https://github.com/CaramelFur/Picsur)

## 部署安装

如果你需要在外网访问自己搭建的 Picsur 服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，注册账号后即可使用。详细的配置步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)，帮助你快速实现远程访问。

### 使用 Docker Compose 快速部署

复制下列配置文件到你的 NAS 或服务器上，保存为 'docker-compose.yml'：

```yaml
version: '3'
services:
  picsur:
    image: ghcr.io/caramelfur/picsur:latest
    container_name: picsur
    ports:
      - '8080:8080'
    environment:
      PICSUR_DB_HOST: picsur_postgres
      PICSUR_ADMIN_PASSWORD: picsur
    restart: unless-stopped
    
  picsur_postgres:
    image: postgres:17-alpine
    container_name: picsur_postgres
    environment:
      POSTGRES_DB: picsur
      POSTGRES_PASSWORD: picsur
      POSTGRES_USER: picsur
    restart: unless-stopped
    volumes:
      - picsur-data:/var/lib/postgresql/data
      
volumes:
  picsur-data:
```

打开你的飞牛 NAS 或其他 NAS 设备，找到 Docker 应用栏目，选择 Docker Compose 功能，将上述配置文件粘贴进去。

系统会自动拉取所需的镜像并创建容器。这个过程可能需要几分钟，请耐心等待。

**重要提示：** 如果你的 NAS 上已经有服务占用了 8080 端口，可以修改上面配置中的 '8080:8080' 部分。左边的数字是外部访问端口，右边的数字是容器内部端口。比如改成 '9090:8080' 就表示通过 9090 端口访问。

### 环境变量配置说明

**必须设置：**

'PICSUR\_ADMIN\_PASSWORD'：管理员登录密码（默认用户名为 admin）

**可选设置：**

'PICSUR\_HOST'：服务监听地址（默认：0.0.0.0）
'PICSUR\_PORT'：服务端口（默认：8080）
'PICSUR\_MAX\_FILE\_SIZE'：最大上传文件大小，单位字节（默认：128000000，约 128MB）
'PICSUR\_JWT\_SECRET'：JWT 密钥，如不设置会自动生成
'PICSUR\_JWT\_EXPIRY'：登录令牌过期时间（默认：7d）
'PICSUR\_VERBOSE'：是否启用详细日志（默认：false，注意详细模式可能记录敏感数据）

其他变量保持默认即可，直接启动容器。

### 访问你的 Picsur 服务

容器启动成功后，在浏览器中访问 'http://你的NAS地址:8080' 即可打开 Picsur 的管理界面。

使用默认用户名 'admin' 和你设置的密码登录。登录后，你就可以开始上传、管理和分享图片了。

本篇教程到此结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/self-hosted-picsur-private-image-bed-nas-intranet-penetration-remote-access>
