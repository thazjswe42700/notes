---
title: "StackEdit开源Markdown编辑器完整部署指南，Docker一键启动支持内网穿透远程访问"
date: 2025-10-16
description: "StackEdit是开源Markdown编辑器，基于PageDown构建，提供实时预览、云端存储、多平台同步等功能。支持浏览器在线使用或自部署服务器，适合技术文档、博客创作者使用。通过Docker可快速部署，完全掌控内容编辑和存储。"
canonical: https://www.passnat.com/posts/stackedit-docker-deployment-guide-internal-penetration-remote-access
---

## 项目介绍

StackEdit 是一款功能完整的开源 Markdown 编辑器，基于 PageDown 构建——这正是 Stack Overflow 和其他 Stack Exchange 网站所采用的 Markdown 库。无论你是技术文档撰写者、博客作者还是内容创作者，StackEdit 都能提供专业级的编辑体验。

这款编辑器提供了全面的 Markdown 编辑功能，支持实时预览、多平台同步、云端存储等特性。你可以直接在浏览器中使用，也可以将其部署在自己的服务器上，完全掌控你的内容。

项目地址： [点我跳转](https://github.com/benweet/stackedit/)

## 部署安装

如果你需要在外网也能访问 StackEdit，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了全平台的内网穿透解决方案。具体的配置步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台使用指南。

### 使用 Docker 快速部署

这是最简便的部署方式，只需一条命令即可启动 StackEdit：

```bash
docker run -d --name stackedit -p 8080:8080 benweet/stackedit:latest
```

打开你的 NAS 设备或云服务器的 Docker 管理界面：

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：`benweet/stackedit:latest`

等待镜像下载完成后，点击 **启动** 按钮启动容器。

勾选 **开机自动启动** 选项，这样容器会在系统重启时自动启动。

**端口设置**：容器内部运行在 8080 端口。如果你的 NAS 或服务器上 8080 端口已被占用，可以修改映射端口。例如，如果想使用 8888 端口访问，可以将端口映射改为 `8888:8080`。

**存储位置**：StackEdit 本身不需要特殊的数据持久化目录，编辑的文档会存储在浏览器本地或云端服务中。

**环境变量**：无需设置任何环境变量，保持默认即可。

点击启动，容器创建成功后，你就可以通过浏览器访问 `http://你的IP:8080` 来使用 StackEdit 了。

到这里 StackEdit 就已经成功部署完成，你可以开始享受专业的 Markdown 编辑体验。

本篇教程结束

---

原文：<https://www.passnat.com/posts/stackedit-docker-deployment-guide-internal-penetration-remote-access>


## 相关文章

- [写公众号不用再手动排版：飞牛 NAS 用一个容器装 doocs/md，Markdown 实时出公众号样式，配内网穿透在公司也能写](professional-wechat-markdown-editor-nas-simple-deployment-doocs-md-ai-intelligent-layout-beautiful-graphic-content.md)
- [轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统](noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access.md)
- [DailyTxT 加密日记：飞牛 NAS Docker 部署 + 内网穿透外网访问](private-diary-encryption-nas-local-deployment-dailytxt-intranet-penetration-remote-access.md)
- [自建备忘录 Memos 飞牛 NAS 极简部署，Docker 一把梭随手记，内网穿透在外面照样翻](memos-self-hosted-notes-nas-docker-deployment-intranet-penetration-remote-access.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
