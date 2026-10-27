---
title: "开源隐私优先工作平台AFFiNE，本地部署NAS无公网内网穿透远程协作文档白板一体化方案"
date: 2025-10-16
description: "发现开源全能工作空间AFFiNE，集文档、白板、任务管理于一身。支持本地优先存储、实时协作、跨平台同步，是Notion和Miro的完美替代品。完全开源可自行托管，Docker快速部署，保护隐私打造个人知识管理系统。"
canonical: https://www.passnat.com/posts/affine-open-source-privacy-first-workspace-nas-local-deployment-intranet-remote-collaboration-docs-whiteboard
---

## 项目介绍

AFFiNE 是一款开源、隐私优先、本地优先的全能工作空间平台，是 Notion 和 Miro 的完美替代品。它集文档编辑、白板绘制、任务规划于一身，为富有创意的用户打造了一个超融合的生产力生态。

AFFiNE 的独特之处在于它将文档、画布和表格完全融合在一起。无论你是想写一份专业报告、在无限画布上自由创意、还是用 AI 助手帮你生成思维导图和演示文稿，AFFiNE 都能胜任。它支持本地优先存储，你的数据始终掌握在自己手中，同时又支持实时协作和跨平台同步。

最重要的是，AFFiNE 完全开源，你可以自行托管、定制和扩展，打造属于自己的知识管理系统。

项目地址： [点我跳转](https://github.com/toeverything/AFFiNE)

## 部署安装

如果你需要在公网上随时随地访问自己的 AFFiNE 工作空间，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可以 [参考这里](https://www.passnat.com/posts/p)，帮助你快速上手全平台内网穿透方案。

### Docker 快速启动

打开你的 NAS 设备或任何支持 Docker 的系统，进入 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
ghcr.io/toeverything/affine:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

**基础配置步骤：**

**1. 勾选开机自启** - 确保容器在系统重启后自动启动

**2. 端口映射设置** - 将容器内端口 **3000** 映射到主机端口（建议使用 3000 或其他未被占用的端口）

如果提示端口被占用，可修改主机端口号，但容器内端口保持为 3000 不变

**3. 存储卷配置** - AFFiNE 需要持久化存储来保存你的工作数据

在 NAS 上创建一个文件夹（如 '/mnt/affine-data'）

将其挂载到容器内的 '/root/.local/share/affine' 路径

**4. 环境变量设置（可选）** - 通常无需配置，保持默认即可

如有特殊需求，可根据官方文档添加自定义环境变量

完成上述配置后，点击启动容器。

**访问应用：**

容器启动成功后，打开浏览器访问：

```
http://你的设备IP:3000
```

即可看到 AFFiNE 的欢迎界面，开始创建你的第一个工作空间。

本篇教程结束

---

原文：<https://www.passnat.com/posts/affine-open-source-privacy-first-workspace-nas-local-deployment-intranet-remote-collaboration-docs-whiteboard>


## 相关文章

- [开源个人数据管理框架Eidos本地部署指南，SQLite私密数据库离线使用，NAS内网穿透远程访问完整教程](eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial.md)
- [本地知识库HamsterBase Docker部署指南，NAS私密收集网页资源，内网穿透远程访问知识管理系统](hamsterbase-docker-deployment-nas-private-knowledge-management-remote-access.md)
- [NotNav导航网站Notion数据库驱动，Next.js打造响应式设计支持内外网切换](notnav-notion-database-nextjs-responsive-design.md)
- [开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程](focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial.md)
- [自托管私密笔记应用Notea，Docker极简部署，云存储灵活配置，内网穿透远程访问](self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access.md)
