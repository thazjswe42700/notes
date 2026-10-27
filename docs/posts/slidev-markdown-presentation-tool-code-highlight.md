---
title: "Slidev开发者演示文稿工具，Markdown轻松创建专业幻灯片，代码高亮实时演示编辑"
date: 2025-10-16
description: "Slidev是开发者专属的演示文稿工具，基于Vite、Vue3和Markdown构建。支持代码高亮、实时演示、主题定制、数学公式和图表绘制。安装简单，功能强大，让技术演讲更生动。支持导出PDF、PNG和PPTX格式。"
canonical: https://www.passnat.com/posts/slidev-markdown-presentation-tool-code-highlight
---

## 项目介绍

Slidev 是一个为开发者量身定制的演示文稿工具。它基于 Vite、Vue 3 和 Markdown 构建，让你可以用纯文本的方式创建出专业级别的演示幻灯片。无需学习复杂的设计软件，只需专注于内容本身，用你最喜爱的代码编辑器就能轻松打造炫彩演讲。

这个项目特别适合技术人员、讲师和内容创作者，它提供了代码高亮、实时编码演示、主题定制、数学公式、图表绘制等强大功能。界面简洁优雅，功能开箱即用，让演讲变得更加生动有趣。

项目地址：[点我跳转](https://github.com/slidevjs/slidev)

## 部署安装

如需在公网环境下访问你的演示文稿，建议配置内网穿透服务。可以前往[帕斯内网穿透](https://passnat.com)注册账号，详细的配置指南请[查看这里](https://www.passnat.com/posts/p)。

### 最快速上手方案

Slidev 推荐使用本地开发的方式，无需 Docker 部署。只需简单两步即可开始：

**第一步：安装 Node.js**

确保你的系统已安装[Node.js 18](https://nodejs.org/)或更高版本。

**第二步：初始化项目**

打开终端，运行以下命令：

```
npm init slidev
```

系统会自动为你创建一个新的演示项目文件夹。进入项目目录后，运行：

```
npm run dev
```

稍等片刻，你就可以在浏览器中访问 'http://localhost:3030' 来查看你的演示文稿了。

### 编写你的第一份演示

使用你喜爱的代码编辑器打开项目中的 'slides.md' 文件，用 Markdown 语法开始编写内容。每个 '---' 分隔符代表一张新的幻灯片。

### 内置功能体验

演讲者模式：在另一个窗口或手机上控制幻灯片

代码演示：支持实时代码高亮和动态执行

绘图工具：直接在幻灯片上进行标注和绘画

数学公式：内置 LaTeX 支持

图表绘制：用文字描述快速生成 Mermaid 图表

录制功能：内置屏幕录制和摄像头支持

导出选项：支持导出为 PDF、PNG 或 PPTX 格式

### 在线体验

如果你想先不安装就体验一下 Slidev 的功能，可以直接访问[sli.dev/new](https://sli.dev/new)在线编辑器，立即开始创建你的第一份演示。

本篇教程结束。祝你的演讲精彩纷呈！

---

原文：<https://www.passnat.com/posts/slidev-markdown-presentation-tool-code-highlight>


## 相关文章

- [开箱即用 Markdown 笔记应用，多库协作实时编辑，NAS 极简部署 Many Notes 内网穿透远程访问](many-notes-markdown-notebook-collaborative-editing-nas-deployment.md)
- [个人综合服务平台UniBoard Docker极简部署，云笔记短链接文件分享一站式解决方案](unibboard-docker-deployment-personal-service-platform-cloud-notes-shortlinks-file-sharing.md)
- [私人知识库无限制 Logseq 开源笔记 NAS 部署 内网穿透远程访问管理](private-knowledge-base-unlimited-logseq-opensource-notes-nas-deployment-intranet-penetration-remote-access-management.md)
- [WeekToDo开源周计划管理应用，极简任务管理，本地部署隐私保护，Docker一键启动NAS自建。](week-todo-open-source-task-management-local-deployment-docker-nas.md)
- [思绪思维导图：NAS Docker 部署 + 内网穿透](mindmap-web-app-lightweight-opensource-tool-multiple-structure-types-export-formats-nas-docker-deployment.md)
