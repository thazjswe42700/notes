---
title: "免费DeepSeek API接口一键部署，OpenAI兼容格式，NAS本地化私有部署方案"
date: 2025-10-16
description: "DeepSeek Free API是一个功能强大的逆向接口转换服务，将DeepSeek Web接口转换为OpenAI兼容API。支持V3和R1深度思考模型、高速流式输出、多轮对话、联网搜索等功能。基于Node.js构建，支持Docker一键部署、多账号接入、完全兼容OpenAI规范，可直接与ChatGPT客户端和Dify集成使用。"
canonical: https://www.passnat.com/posts/free-deepseek-api-deployment-openai-compatible-nas-local-private
---

## DeepSeek Free API - 免费 AI 大模型接口服务

#### 项目介绍

DeepSeek Free API 是一个功能强大的 DeepSeek 大模型逆向接口转换服务。它将 DeepSeek 官方的 Web 接口转换为标准的 OpenAI 兼容 API，让开发者可以轻松集成 DeepSeek V3 和 R1 深度思考模型到自己的应用中。

项目基于 Node.js 构建，支持高速流式输出、多轮对话、联网搜索、R1 深度思考等多种功能模式。最重要的是，它提供了零配置部署方案，只需一条命令即可快速启动服务。无论你是开发者还是 AI 爱好者，都可以轻松部署属于自己的 DeepSeek API 服务。

项目支持多账号接入、多路并发请求，完全兼容 OpenAI 接口规范，可以直接与 ChatGPT 客户端、Dify 等工具集成使用。

项目地址：[点我跳转](https://github.com/LLM-Red-Team/deepseek-free-api)

#### 部署安装

如果你需要在办公室、学校或其他地方远程访问部署的服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速入门指南。

##### Docker 单命令部署

这是最快速的部署方式，适合所有用户。

打开飞牛 NAS 或其他 NAS 设备的管理界面，找到 Docker 管理功能。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**。

在镜像地址栏填入：*vinlic/deepseek-free-api:latest*

等待镜像下载完成，然后点击 **启动** 按钮启动容器。

**端口配置**

容器内部运行在 **8000 端口**，你需要将其映射到宿主机的某个端口。建议直接映射到 8000 端口（如果没有冲突的话）。如果 8000 端口已被占用，可以修改为其他端口，比如 8001、8080 等，但容器内的 8000 端口保持不变。

**环境变量设置**

这一步是可选的，但强烈建议配置。

必须设置的环境变量：

DEEP\_SEEK\_CHAT\_AUTHORIZATION：你的 DeepSeek userToken。从 DeepSeek 官网获取方法是：登录 [DeepSeek Chat](https://chat.deepseek.com/)，按 F12 打开开发者工具，在 Application > LocalStorage 中找到 userToken 的值，复制其 value 部分即可。

时区设置（可选但推荐）：

TZ：设置为 *Asia/Shanghai* 以使用中国时区

如果你有多个 DeepSeek 账号，可以用逗号分隔多个 token：TOKEN1,TOKEN2,TOKEN3

其他环境变量都可以保持默认，直接下一步启动容器即可。

**访问服务**

容器启动成功后，打开浏览器访问 http://你的NAS地址:8000（如果映射的是其他端口，请替换为对应端口）。

如果看到 API 文档页面，说明服务已成功部署！

##### Docker-Compose 部署方案

如果你更喜欢使用 docker-compose，可以参考以下配置：

复制下列的配置文件到你的 NAS 或服务器上，保存为 docker-compose.yml：

```yaml
version: '3'

services:
  deepseek-free-api:
    container_name: deepseek-free-api
    image: vinlic/deepseek-free-api:latest
    restart: always
    ports:
      - "8000:8000"
    environment:
      - TZ=Asia/Shanghai
      - DEEP_SEEK_CHAT_AUTHORIZATION=你的token
```

打开飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

点击 **Compose** 或 **Docker-Compose** 选项，粘贴上述配置文件内容。

将 你的token 替换为你从 DeepSeek 获取的 userToken 值。

如果 8000 端口被占用，可以修改配置中的 "8000:8000" 为其他端口，例如 "8001:8000"（前面的数字是宿主机端口，后面的是容器内端口）。

点击启动或部署按钮，等待容器启动完成。

启动成功后，访问 http://你的NAS地址:8000 即可使用服务。

#### 功能特性

**多模型支持**

标准模式：deepseek（通用对话）

深度思考：deepseek-r1（推理能力更强）

联网搜索：deepseek-search（实时信息查询）

组合模式：deepseek-r1-search（深度思考 + 联网搜索）

静默模式：隐藏思考过程或搜索结果

**核心功能**

高速流式输出，实时响应

完整的多轮对话支持

联网搜索能力，获取最新信息

R1 深度思考，复杂问题分析

多账号支持，突破单账号限制

OpenAI 接口完全兼容

**部署优势**

零配置启动，一条命令搞定

支持 Docker、Docker-Compose 等多种部署方式

支持 Render、Vercel 等云平台部署

原生 Node.js 部署选项

#### 快速开始

**获取 DeepSeek Token**

访问 [DeepSeek Chat](https://chat.deepseek.com/) 并登录

按 F12 打开浏览器开发者工具

切换到 Application 标签页

在左侧找到 LocalStorage，点击 DeepSeek 域名

找到 userToken 项，复制其 value 值

**使用 API**

部署完成后，你可以通过以下方式使用 API：

```bash
curl http://localhost:8000/v1/chat/completions \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek",
    "messages": [{"role": "user", "content": "你好"}],
    "stream": false
  }'
```

或者使用任何支持 OpenAI 兼容接口的工具，如 Dify、LobeChat 等。

#### 重要提示

⚠️ 本项目仅供个人学习和研究使用，禁止用于商业目的或对外提供服务。

⚠️ 逆向 API 存在被封禁的风险，建议重要应用直接使用 [DeepSeek 官方 API](https://platform.deepseek.com/)。

⚠️ 请确保你在中国境内或拥有中国境内的计算设备，否则可能无法连接 DeepSeek 服务。

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/free-deepseek-api-deployment-openai-compatible-nas-local-private>
