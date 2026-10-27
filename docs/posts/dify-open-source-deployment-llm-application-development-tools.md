---
title: "开源Dify平台极简部署，LLM应用开发工具链完整集成，支持内网穿透远程访问"
date: 2025-10-16
description: "Dify是开源LLM应用开发平台，提供工作流编排、RAG管道、Agent能力、模型管理等完整功能。支持与GPT、Mistral、Llama3等数百个模型无缝集成，内置50+工具供AI应用使用。Docker Compose一键部署，轻松从原型迈向生产环境。"
canonical: https://www.passnat.com/posts/dify-open-source-deployment-llm-application-development-tools
---

## Dify - 开源LLM应用开发平台

### 项目介绍

Dify 是一个功能强大的开源LLM应用开发平台，为开发者提供了一套完整的工具链来快速构建和部署AI应用。这个平台整合了工作流编排、RAG管道、Agent能力、模型管理、可观测性等多个核心功能，让你能够轻松从原型阶段迈向生产环境。

无论你是想要构建智能对话系统、文档分析工具，还是需要集成多个大语言模型，Dify都能为你提供一个直观易用的可视化界面。平台支持与数百个LLM无缝集成，包括GPT、Mistral、Llama3等开源模型，以及任何兼容OpenAI API的模型。同时，Dify提供了50+的内置工具供AI Agent使用，如Google搜索、DALL·E、Stable Diffusion等，大大加速了AI应用的开发效率。

项目地址：[点我跳转](https://github.com/langgenius/dify)

### 部署安装

若需要在外网环境中访问你的Dify实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) —— 一个稳定可靠的内网穿透解决方案。先行注册好账号以备不时之需。具体的配置步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，支持全平台使用。

#### 系统要求

在开始部署前，请确保你的机器满足以下最低配置：

CPU：≥ 2 核心

内存：≥ 4 GB

#### Docker Compose 快速部署

Dify 最简单的启动方式就是通过 Docker Compose。请先确保你已经安装了 Docker 和 Docker Compose。

**第一步：获取配置文件**

打开你的 NAS 设备（飞牛、群晖或其他支持Docker的NAS），进入终端或SSH界面，执行以下命令：

```
cd dify/docker
cp .env.example .env
```

**第二步：启动服务**

```bash
docker compose up -d
```

**第三步：初始化应用**

启动完成后，打开浏览器访问 `http://你的设备IP/install` 进行初始化配置。

#### 环境变量配置说明

根据你的实际需求，可以在 `.env` 文件中配置以下重要变量：

**必须设置的变量：**

变量名: 'SECRET\_KEY' | 说明: 用于会话加密和数据库敏感信息加密的密钥 | 默认值: sk-****

变量名: 'DB\_PASSWORD' | 说明: PostgreSQL 数据库密码 | 默认值: difyai123456

变量名: 'REDIS\_PASSWORD' | 说明: Redis 缓存密码 | 默认值: difyai123456

**可选配置的变量：**

变量名: 'INIT\_PASSWORD' | 说明: 管理员初始密码（不超过30个字符） | 默认值: 空

变量名: 'LOG\_LEVEL' | 说明: 日志级别（DEBUG/INFO/WARNING/ERROR/CRITICAL） | 默认值: INFO

变量名: 'DEPLOY\_ENV' | 说明: 部署环境（PRODUCTION/TESTING） | 默认值: PRODUCTION

变量名: 'UPLOAD\_FILE\_SIZE\_LIMIT' | 说明: 上传文件大小限制（MB） | 默认值: 15

变量名: 'UPLOAD\_IMAGE\_FILE\_SIZE\_LIMIT' | 说明: 上传图片大小限制（MB） | 默认值: 10

变量名: 'VECTOR\_STORE' | 说明: 向量数据库类型 | 默认值: weaviate

**端口映射说明：**

Dify 默认通过以下端口提供服务：

80/443：Web UI 和 API 服务（对外访问）

5001：API 后端服务端口

6379：Redis 缓存服务

5432：PostgreSQL 数据库服务

如果你的设备上这些端口已被占用，可以修改 `docker-compose.yml` 中的端口映射配置。例如，将外部访问端口改为 `8080:80`，这样就可以通过 `http://你的设备IP:8080` 访问了。

**存储配置：**

Dify 需要持久化存储用户上传的文件和应用数据。建议在你的 NAS 上创建一个专用文件夹（如 `/data/dify`），然后在 `docker-compose.yml` 中配置卷挂载，确保容器重启后数据不会丢失。

#### 高级配置

如果需要更深层次的自定义，可以参考项目中的 `.env.example` 文件，该文件包含了所有可用的环境变量及其详细说明。修改完配置后，重新执行 `docker compose up -d` 命令使配置生效。

对于需要高可用部署的场景，Dify 社区提供了多个 Kubernetes 部署方案（Helm Chart 和 YAML 文件），以及针对 AWS、Azure、Google Cloud 等公有云的一键部署工具。

### 核心功能亮点

**工作流编排**：在可视化画布上构建和测试强大的 AI 工作流，支持复杂的流程控制和条件判断。

**RAG 管道**：完整的检索增强生成能力，支持 PDF、PPT 等多种文档格式的自动提取和智能检索。

**Agent 能力**：基于 LLM Function Calling 或 ReAct 框架定义智能代理，内置 50+ 工具供 Agent 调用。

**模型管理**：集中管理多个 LLM 提供商的模型，轻松切换和对比不同模型的性能表现。

**可观测性**：完整的应用日志和性能监控，支持基于生产数据的持续优化。

**Backend-as-a-Service**：所有功能都提供对应的 API，可以轻松集成到你的业务系统中。

### 总结

Dify 提供了一个从开发到生产的完整 LLM 应用解决方案。通过 Docker Compose 的一键部署，即使是技术小白也能在几分钟内搭建起自己的 AI 应用平台。无论你是想要快速原型验证，还是构建企业级应用，Dify 都能满足你的需求。

立即开始你的 AI 之旅吧！

---

原文：<https://www.passnat.com/posts/dify-open-source-deployment-llm-application-development-tools>
