---
title: "Kotaemo智能文档对话平台开源部署，私有RAG系统本地化搭建与内网穿透远程访问完整指南"
date: 2025-10-16
description: "Kotaemo是开源智能文档对话平台，采用RAG技术架构，支持混合检索、多模态文档处理、精准引用和高级推理。提供企业级功能如多用户、权限管理、协作分享，支持本地部署保护隐私，兼容OpenAI、Azure、Ollama等多种LLM，Docker一键部署快速上手。"
canonical: https://www.passnat.com/posts/kotaemo-smart-document-chat-open-source-deployment-private-rag-system-localization-intranet-penetration-remote-access-complete-guide
---

## Kotaemo 智能文档对话平台 - 开箱即用的私有RAG系统

### 项目介绍

Kotaemo 是一个开源的、开箱即用的智能文档问答平台。它采用现代化的 RAG（检索增强生成）技术架构，为用户提供了一个简洁而强大的文档对话界面。无论你是普通用户想要快速搭建自己的文档知识库，还是开发者想要构建自定义的 RAG 管道，Kotaemo 都能满足你的需求。

这个项目具有漂亮的用户界面，支持多用户登录、文件集合管理、协作分享等企业级功能。最重要的是，你可以完全掌控自己的数据，支持使用本地模型进行私密的文档分析，无需将敏感信息上传到云端。

**项目地址：**<https://github.com/Cinnamon/kotaemo>

### 核心特性亮点

🎯 混合检索引擎：结合全文搜索与向量检索，确保最佳的信息检索质量

📄 多模态支持：支持处理包含图表、表格的复杂文档，智能提取关键信息

🔗 精准引用：每个回答都配有详细的文献引用和相关性评分，支持在线 PDF 查看器预览

🧠 高级推理：支持问题分解、ReAct 和 ReWOO 等智能推理方法处理复杂问题

🔐 隐私优先：支持本地部署和本地模型，你的数据永远掌握在自己手中

🛠️ 灵活配置：支持 OpenAI、Azure、Ollama 等多种 LLM 提供商，可在 UI 中实时调整检索和生成参数

👥 企业协作：多用户支持、文件权限管理、共享对话等功能

### 部署安装

如果你需要在外网也能访问这个平台，建议配置内网穿透服务。可以考虑使用 [帕斯内网穿透](https://passnat.com) 这类的解决方案，先注册账号备用。具体的操作指南可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问。

#### 最快速上手方案 - Docker 单命令部署

Kotaemo 提供了三个版本的 Docker 镜像，满足不同的使用场景：

**Lite 版本**（推荐大多数用户）- 支持 PDF、HTML、Excel 等常见文件格式：

```bash
docker run \
  -e GRADIO_SERVER_NAME=0.0.0.0 \
  -e GRADIO_SERVER_PORT=7860 \
  -v ./ktem_app_data:/app/ktem_app_data \
  -p 7860:7860 -it --rm \
  ghcr.io/cinnamon/kotaemo:main-lite
```

**Full 版本** - 支持更多文件格式（Word、PowerPoint 等），包含完整的 OCR 和表格解析能力：

```bash
docker run \
  -e GRADIO_SERVER_NAME=0.0.0.0 \
  -e GRADIO_SERVER_PORT=7860 \
  -v ./ktem_app_data:/app/ktem_app_data \
  -p 7860:7860 -it --rm \
  ghcr.io/cinnamon/kotaemo:main-full
```

**Ollama 集成版本** - 预装本地大语言模型，实现完全私密的本地 RAG：

```bash
docker run \
  -e GRADIO_SERVER_NAME=0.0.0.0 \
  -e GRADIO_SERVER_PORT=7860 \
  -v ./ktem_app_data:/app/ktem_app_data \
  -p 7860:7860 -it --rm \
  ghcr.io/cinnamon/kotaemo:main-ollama
```

**Mac 用户注意**（M1/M2/M3 芯片）：

```bash
docker run \
  -e GRADIO_SERVER_NAME=0.0.0.0 \
  -e GRADIO_SERVER_PORT=7860 \
  -v ./ktem_app_data:/app/ktem_app_data \
  -p 7860:7860 -it --rm \
  --platform linux/arm64 \
  ghcr.io/cinnamon/kotaemo:main-lite
```

#### NAS 部署指南

如果你使用飞牛 NAS 或其他 NAS 设备，按照以下步骤操作：

**1. 添加 Docker 镜像**

打开 NAS 管理界面，找到'本地镜像'或'Docker 镜像'模块

选择'添加镜像'，选择'从 URL 添加'

输入镜像地址：*ghcr.io/cinnamon/kotaemo:main-lite*（或其他版本）

等待镜像下载完成

**2. 启动容器配置**

点击下载好的镜像，选择'启动'按钮

勾选'开机自动启动'确保重启后自动运行

**3. 端口映射设置**

容器端口（内部）：*7860*

宿主机端口（外部）：*7860*（如果冲突可改为其他端口如 *8080*，但容器内部端口必须保持 *7860*）

**4. 数据持久化配置**

创建一个本地文件夹用于存储应用数据，例如 */mnt/storage/kotaemo\_data*

容器内路径：*/app/ktem\_app\_data*

将本地文件夹映射到容器内，确保你的文档和配置数据永久保存

**5. 环境变量配置**（可选但推荐）

**必须设置：**

GRADIO\_SERVER\_NAME=0.0.0.0 - 允许外部访问

GRADIO\_SERVER\_PORT=7860 - 服务端口

**可选设置：**

OPENAI\_API\_KEY - 如果使用 OpenAI 模型，填入你的 API 密钥

AZURE\_OPENAI\_API\_KEY - Azure OpenAI 用户填写

USE\_NANO\_GRAPHRAG=true - 启用 NanoGraphRAG 高级检索

USE\_LIGHTRAG=true - 启用 LightRAG 知识图谱功能

其他变量保持默认即可，无需修改。

**6. 完成部署**

点击'下一步'和'启动'按钮

等待容器启动完成（通常需要 1-2 分钟）

**7. 访问应用**

在浏览器中访问：*http://你的NAS\_IP:7860*

默认用户名和密码都是 *admin*

首次登录后，建议立即修改密码并创建新用户

### 快速上手指南

**1. 登录并配置 LLM**

使用默认账户（admin/admin）登录

进入'Resources'标签页

在'LLMs and Embeddings'中配置你的 AI 模型

支持 OpenAI、Azure OpenAI、本地 Ollama 等多种选择

**2. 上传文档**

点击'Upload'按钮上传你的 PDF、Word、Excel 等文件

系统会自动解析和索引文档内容

**3. 开始对话**

在聊天框输入你的问题

系统会智能检索相关内容并生成回答

每个回答都附带精准的文献引用

**4. 高级功能探索**

尝试'Settings'中的不同推理方法（简单问答、问题分解、智能代理）

调整检索参数以获得最佳效果

创建和分享知识库集合

### 总结

Kotaemo 是一个功能完整、易于部署的企业级文档智能问答系统。无论你是想为团队搭建内部知识库，还是想体验最新的 RAG 技术，这个开源项目都是一个绝佳的选择。通过 Docker 的支持，你可以在几分钟内完成部署，立即开始享受智能文档分析的便利。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/kotaemo-smart-document-chat-open-source-deployment-private-rag-system-localization-intranet-penetration-remote-access-complete-guide>
