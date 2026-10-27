---
title: "轻量级Markdown笔记应用Note Mark，Docker极简部署NAS，支持内网穿透远程访问笔记"
date: 2025-10-16
description: "Note Mark是轻量级网页版Markdown笔记应用，支持GitHub风味语法、深色/浅色主题、笔记共享、资源上传、OIDC登录等功能。提供响应式界面，桌面移动设备体验一致。采用安全的扁平文件存储，支持多种视图模式。Docker一键部署，配置简单，内网穿透访问便捷。"
canonical: https://www.passnat.com/posts/note-mark-markdown-docker-nas-intranet-penetration
---

## 项目介绍

Note Mark 是一款轻量级、高效能的网页版 Markdown 笔记应用。它采用简洁优雅的设计理念，提供流畅的编辑体验和响应式的用户界面。这款应用支持 GitHub 风味 Markdown 语法，集成了深色/浅色主题切换、笔记本共享、资源附件上传、单点登录（OIDC）等丰富功能。

无论是在桌面还是移动设备上，Note Mark 都能为你提供一致的高效笔记体验。其独特的扁平文件存储系统确保了数据的安全性，同时支持多种视图模式（渲染视图和纯文本视图），让你可以灵活地查看和编辑笔记内容。

项目地址：[点我跳转](https://github.com/enchant97/note-mark)

## 部署安装

如果你想在外网也能访问你的笔记应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 这个工具，先注册好账号备用。具体的操作流程可以参考 [这份详细指南](https://www.passnat.com/posts/p)，它涵盖了全平台上手的完整步骤。

### 使用 Docker 快速部署

打开你的 NAS 设备或其他支持 Docker 的环境，进入本地镜像管理界面，选择'添加镜像'，然后选择'从 URL 添加'。

在镜像地址栏中填入：enchant97/note-mark:latest

等待镜像下载完成后，点击启动按钮来启动容器。

**端口配置**

在容器启动配置中，你需要设置端口映射。Note Mark 的容器内部运行在 **5000 端口**。你可以根据需要将其映射到宿主机的任意端口（如 5000、8080 等）。如果遇到端口被占用的情况，只需修改宿主机端口即可，容器内部端口保持为 5000。

**存储配置**

Note Mark 需要持久化存储笔记数据。建议在你的 NAS 上创建一个专用文件夹（例如：/volume1/docker/note-mark/data），然后将其映射到容器内的 /app/data 路径，这样你的所有笔记数据就能被妥善保存。

**环境变量设置**

Note Mark 支持通过环境变量进行配置。以下是可选的环境变量：

可选配置：

SECRET\_KEY：应用密钥，用于会话加密（建议设置一个强密码）

WORKERS：工作进程数（默认值：4）

LOG\_LEVEL：日志级别（默认值：INFO）

如果你不需要自定义这些配置，直接使用默认值即可，无需额外填写环境变量。

**启动应用**

完成上述配置后，点击启动按钮，稍等片刻应用即可启动成功。

访问地址为：http://你的NAS地址:5000（根据你的端口映射调整）

到这里，Note Mark 就已经成功部署并可以正常使用了。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/note-mark-markdown-docker-nas-intranet-penetration>
