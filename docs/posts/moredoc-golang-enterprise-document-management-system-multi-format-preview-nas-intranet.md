---
title: "魔豆文库MOREDOC开源部署指南，Golang企业级文档管理系统，支持多格式文件预览，NAS内网穿透轻松搭建"
date: 2025-10-16
description: "魔豆文库是基于Golang开发的开源企业级文库系统，提供文档上传、在线预览、搜索分享等功能，支持TXT、PDF、EPUB、MOBI、Office等多种格式。采用现代技术栈，性能稳定可靠，支持Docker快速部署，适合个人知识库和团队文档平台搭建。"
canonical: https://www.passnat.com/posts/moredoc-golang-enterprise-document-management-system-multi-format-preview-nas-intranet
---

## 项目介绍

魔豆文库（MOREDOC）**是一个功能强大的开源文库系统，由深圳市摩枫网络科技使用 Golang 精心打造。它提供了类似百度文库、新浪爱问文库的完整功能，支持 TXT、PDF、EPUB、MOBI、Office 等多种格式文档的在线预览与管理。**

这是一个真正的企业级文库解决方案，具有完整的文档上传、搜索、分享、管理等功能。无论是个人知识库还是团队文档平台，魔豆文库都能满足您的需求。采用 Golang + Vue.js 的现代技术栈，性能稳定，界面友好，是搭建私有文库平台的理想选择。

项目地址：[点我跳转](https://github.com/mnt-ltd/moredoc)

## 部署安装

如需在外网也能访问您的文库系统，建议配置内网穿透功能，推荐使用 [帕斯内网穿透](https://passnat.com)。先注册好账号以备后用，具体操作步骤可参考 [帕斯内网穿透全平台上手指南](https://www.passnat.com/posts/p)。

### Docker 快速部署

打开您的 NAS 设备或服务器的 Docker 管理界面

点击 **本地镜像 - 添加镜像 - 从 URL 添加**

镜像地址填写：mntltd/moredoc:latest

等待镜像下载完成后，点击启动按钮启动容器。

**基础配置步骤：**

1. 开机自启设置：勾选'开机自动启动'选项，确保容器随系统启动而运行，下一步继续。

2. 端口映射配置：

容器内部监听端口：8880（后端服务）

建议映射到主机端口：8880

如果 8880 端口已被占用，可修改为其他未使用的端口（如 8881、8882 等），但容器内部端口保持 8880 不变

3. 存储卷配置：

需要创建两个目录用于持久化存储：

/documents - 用于存储用户上传的文档文件

/uploads - 用于存储其他附件文件

在 NAS 上创建这两个文件夹后，分别映射到容器内对应路径

4. 数据库配置：

魔豆文库需要 MySQL 5.7+ 数据库

如果您已有 MySQL 服务，可直接配置连接信息

如无数据库，建议先部署一个 MySQL 容器

5. 环境变量设置（可选）：

**必须设置：**

DB\_DSN：MySQL 数据库连接字符串（格式：user:password@tcp(host:port)/dbname?charset=utf8mb4&loc=Local&parseTime=true）

**可选设置：**

LOG\_LEVEL：日志级别（debug、info、warn、error，默认 debug）

JWT\_SECRET：JWT 密钥（默认 moredoc）

JWT\_EXPIRE\_DAYS：Token 过期天数（默认 365）

其他参数保持默认即可，无需修改。

6. 启动容器：完成上述配置后，点击下一步启动容器。

**访问您的文库：**

容器启动成功后，在浏览器中访问：http://您的IP:8880

使用默认管理员账号登录：

账号：admin

密码：mnt.ltd

登录后即可开始上传文档、管理文库内容。

本篇教程到此结束，祝您使用愉快！

---

原文：<https://www.passnat.com/posts/moredoc-golang-enterprise-document-management-system-multi-format-preview-nas-intranet>


## 相关文章

- [Stump 电子书库：NAS Docker 部署 + 内网穿透](stump-nas-docker-ebook-library-reading-progress-intranet-penetration.md)
- [开源文件预览服务kkFileView Docker部署完整指南，支持百余种格式在线查看](open-source-file-preview-kkfileview-docker-deployment-guide.md)
- [Calibre-Web 自建电子书库：飞牛 NAS Docker Compose 部署，内网穿透在外面也能翻自己的书](calibre-web-self-hosted-ebook-library-nas-docker-deployment-intranet-penetration.md)
- [开源私密新闻阅读器Readflow，NAS极简部署，内网穿透远程访问，无广告无追踪](readflow-open-source-private-news-reader-nas-deployment-intranet-penetration-remote-access-ad-free-tracking-free.md)
- [MinDoc轻量级文档管理系统Docker极简部署，Golang高性能跨平台支持，内网穿透远程安全访问](mindoc-docker-deployment-golang-intranet-penetration.md)
