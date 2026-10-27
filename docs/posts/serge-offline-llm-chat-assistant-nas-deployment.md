---
title: "本地私有AI聊天助手 Serge 离线运行LLM 无需API密钥 NAS极简部署内网穿透远程访问"
date: 2025-10-16
description: "Serge是基于llama.cpp的离线大语言模型聊天界面，支持完全本地部署，无需API密钥。采用SvelteKit、FastAPI和LangChain技术栈，提供隐私保护和数据完全控制。本文详细介绍Serge的Docker快速部署方案、端口映射、存储配置和环境变量设置步骤。"
canonical: https://www.passnat.com/posts/serge-offline-llm-chat-assistant-nas-deployment
---

## 项目介绍

Serge 是一个功能强大的本地大语言模型聊天界面，它基于 llama.cpp 构建，让你可以完全离线运行 LLM 模型，无需任何 API 密钥。这是一个真正意义上的自主托管解决方案，为用户提供了完整的隐私保护和数据控制权。

项目采用现代化的技术栈构建：前端使用 SvelteKit 打造美观的用户界面，后端基于 FastAPI 和 LangChain 框架，通过 llama.cpp 的 Python 绑定来调用模型，同时使用 Redis 存储聊天历史记录和参数配置。无论你是 AI 爱好者还是隐私保护倡导者，Serge 都能让你轻松部署和运行各种开源大语言模型，享受本地化的 AI 对话体验。

项目地址：[点我跳转](https://github.com/nsarrazin/serge)

## 部署安装

如需在外网也能访问你的 Serge 服务，建议配置内网穿透工具。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 全平台内网穿透上手指南。

### 最快速部署方案 - Docker 单命令启动

打开飞牛 NAS 或其他 NAS 设备的管理面板，进入容器管理模块。

点击 **本地镜像 - 添加镜像 - 从 URL 添加**，在镜像地址栏填入：

```
ghcr.io/serge-chat/serge:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

**端口映射设置：**

这一步很关键。容器内部使用的是 `8008` 端口，你需要将其映射到宿主机。如果你的 NAS 上 8008 端口已被占用，可以修改宿主机端口号（比如改成 8080、8888 等），但容器内的 8008 端口保持不变。

**存储卷配置：**

Serge 需要两个持久化存储位置：

'weights' - 用于存放下载的模型文件

'datadb' - 用于存储聊天历史和数据库文件

在 NAS 中分别创建这两个文件夹，然后将它们映射到容器内对应的路径。

**环境变量设置：**

以下是可选的环境变量配置，如果你想自定义设置可以填写：

选择设置（可以不填）：

'SERGE\_DATABASE\_URL' - 数据库连接字符串，默认值：'sqlite:////data/db/sql\_app.db'

'SERGE\_JWT\_SECRET' - 认证令牌加密密钥，建议使用随机字符串替换默认值 'uF7FGN5uzfGdFiPzR'

'SERGE\_SESSION\_EXPIRY' - 用户会话过期时间（分钟），默认值：'60'

'NODE\_ENV' - Node.js 运行环境，默认值：'production'

其他设置保持默认即可，直接下一步启动容器。

**访问应用：**

容器启动成功后，在浏览器中访问 `http://你的NAS地址:8008` 即可进入 Serge 聊天界面。同时，API 文档可以在 `http://你的NAS地址:8008/api/docs` 查看。

### 内存提示

⚠️ **重要提示**：LLaMA 模型对内存需求较高，请确保你的 NAS 有足够的可用内存来运行所选的模型，否则可能导致程序崩溃。建议先从较小的模型开始尝试。

本篇教程结束

---

原文：<https://www.passnat.com/posts/serge-offline-llm-chat-assistant-nas-deployment>


## 相关文章

- [免费私密本地AI搜索引擎，无需API密钥NAS一键部署，多LLM模型聚合智能问答](free-private-local-ai-search-engine-no-api-key-nas-one-click-deploy-multi-llm-aggregation-qa.md)
- [本地部署AI大模型Ollama，离线运行Gemma Llama DeepSeek，支持内网穿透远程访问](local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [本地AI交互平台SillyTavern Docker部署指南，NAS快速安装配置教程](silly-tavern-docker-deployment-guide-nas-quick-install-setup.md)
