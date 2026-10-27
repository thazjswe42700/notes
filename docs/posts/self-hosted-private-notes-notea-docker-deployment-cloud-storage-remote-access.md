---
title: "自托管私密笔记应用Notea，Docker极简部署，云存储灵活配置，内网穿透远程访问"
date: 2025-10-16
description: "Notea是一款自托管笔记应用，采用Notion风格Markdown编辑器，支持Amazon S3、MinIO、阿里云OSS等多种云存储集成。数据完全自主控制，支持笔记分享、反向链接、链接嵌入等高级功能。本文详细介绍Notea的Docker部署方法、环境变量配置及访问方式。"
canonical: https://www.passnat.com/posts/self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access
---

## 项目介绍

Notea 是一款自托管的笔记应用，专为追求数据隐私和自主控制的用户设计。它采用现代化的 Notion 风格 Markdown 编辑器，支持与多种云存储服务集成，包括 Amazon S3、MinIO、阿里云 OSS、腾讯云 COS、Oracle 对象存储、Exoscale 以及 Cloudflare R2 等。无论你是个人用户还是团队协作，Notea 都能为你提供灵活、安全、可靠的笔记管理方案。

最大的优势在于，你的所有笔记数据完全由你自己掌控，存储在你选择的云服务上，不用担心数据泄露或被第三方平台限制。同时支持笔记分享、反向链接、链接嵌入（YouTube、Github Gist、Google Docs 等）等高级功能，让你的笔记系统更加强大。

项目地址：[点我跳转](https://github.com/notea-org/notea)

## 部署安装

如果你想在外网也能访问自己的笔记应用，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，注册账号后即可使用。详细的配置步骤可以参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，帮你轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备（飞牛 NAS 或其他品牌），进入本地镜像管理界面。

点击 **添加镜像** → **从 URL 添加**，在镜像地址栏填入：

```
cinwell/notea
```

等待镜像下载完成后，点击启动按钮。

### 端口配置

勾选 **开机自动启动**，然后进入端口设置界面。根据以下信息配置：

容器内端口：`3000`（这是应用内部使用的端口，保持不变）

宿主机端口：`3000`（你访问应用时使用的端口，如果与其他应用冲突，可以修改为其他未占用的端口，比如 `3001`、`3002` 等）

### 环境变量设置

环境变量是应用的核心配置，根据你选择的存储服务不同而不同。以下以 MinIO（自托管 S3 存储）为例：

必须设置的环境变量：

环境变量名 | 说明 | 示例值
PASSWORD | 登录应用的密码 | notea123
STORE\_ACCESS\_KEY | 存储服务的访问密钥 | Q3AM3UQ867SPQQA43P2F
STORE\_SECRET\_KEY | 存储服务的密钥 | zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG
STORE\_BUCKET | 存储桶名称 | notea
STORE\_END\_POINT | 存储服务地址 | http://play.minio.io
STORE\_FORCE\_PATH\_STYLE | 是否强制使用路径风格 URL | true

可选设置的环境变量：

环境变量名 | 说明 | 默认值
STORE\_REGION | 存储区域 | us-east-1
STORE\_PREFIX | 存储路径前缀 | （空）
COOKIE\_SECURE | 仅在 HTTPS 下工作（HTTP 需设为 false） | true
BASE\_URL | 网站域名（用于 SEO） | （空）
DISABLE\_PASSWORD | 禁用密码保护 | false
DIRECT\_RESPONSE\_ATTACHMENT | 直接输出附件而非重定向 | false

如果你使用的是其他存储服务（如 Amazon S3、阿里云 OSS 等），只需根据对应的服务配置相应的环境变量即可。官方文档中提供了各种存储服务的详细配置示例。

所有配置完成后，点击下一步启动容器。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:3000
```

输入你设置的密码即可登录应用。到这里，Notea 就可以正常使用了。

## 总结

通过 Docker 部署 Notea，你可以快速搭建一个属于自己的私密笔记系统。只需几步简单配置，就能享受到企业级的笔记管理体验。配合内网穿透工具，随时随地访问你的笔记。祝你使用愉快！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access>


## 相关文章

- [自托管笔记应用Flatnotes极简部署，无数据库Markdown笔记库远程访问方案](flatnotes-self-hosted-markdown-notes-deployment.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
- [轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统](noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access.md)
- [轻量级Markdown笔记应用Note Mark，Docker极简部署NAS，支持内网穿透远程访问笔记](note-mark-markdown-docker-nas-intranet-penetration.md)
- [Xnote个人笔记系统Docker部署指南，自建私密知识库随处可得内网穿透远程访问](xnote-docker-deployment-guide-personal-note-system-private-knowledge-base-intranet-penetration-remote-access.md)
