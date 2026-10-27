---
title: "开源AI工作流平台Langflow，可视化编辑器快速构建智能体，支持NAS本地部署与内网穿透远程访问"
date: 2025-10-16
description: "一款开源AI工作流构建平台，提供可视化编辑器和强大API能力。支持多智能体编排、自定义组件、灵活部署为API或MCP服务器。集成LangSmith、LangFuse等监控工具，支持Docker快速部署。适合开发者原型化和部署复杂AI应用。"
canonical: https://www.passnat.com/posts/langflow-open-source-ai-workflow-platform-visual-editor-local-deployment
---

## 项目介绍

Langflow 是一款功能强大的 AI 工作流构建和部署平台。它为开发者提供了直观的可视化编辑界面和强大的 API 能力，让你能够轻松构建、测试和部署 AI 驱动的智能体和工作流程。无论你是想快速原型化 AI 应用，还是需要构建复杂的多智能体系统，Langflow 都能满足你的需求。

Langflow 支持所有主流的大语言模型、向量数据库和丰富的 AI 工具库。通过拖拽式的可视化界面，即使是非专业开发者也能快速上手。项目完全开源，可以部署到任何云平台或本地环境中。

项目地址：[点我跳转](https://github.com/langflow-ai/langflow)

## 核心功能特性

🎨 **可视化流程编辑器** - 通过拖拽组件快速构建 AI 工作流，无需复杂代码

🔧 **源代码访问** - 支持 Python 自定义组件，完全掌控每一个环节

🧪 **交互式测试台** - 实时调试和优化你的工作流，支持逐步执行

🤖 **多智能体编排** - 支持复杂的多智能体协作、对话管理和数据检索

🚀 **灵活部署** - 支持导出为 API、JSON 或 MCP 服务器

📊 **可观测性** - 与 LangSmith、LangFuse 等平台集成，实时监控工作流运行状态

🔐 **企业级安全** - 提供完整的安全认证和可扩展架构

## 部署安装

如果你想在外网也能访问部署的 Langflow，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，支持全平台使用。具体的操作教程可以 [点我查看](https://www.passnat.com/posts/p)，包含详细的配置步骤。

### 最快速部署方案（Docker 单命令）

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可快速启动 Langflow：

```bash
docker run -p 7860:7860 langflowai/langflow:latest
```

等待镜像下载完成后，直接访问 `http://localhost:7860` 就能看到 Langflow 的可视化界面了。这是最简单快捷的启动方式，适合快速体验和测试。

### 进阶部署方案（Docker Compose + PostgreSQL）

如果你需要更稳定的生产环境和持久化数据存储，可以使用 Docker Compose 方案。这个方案包含 Langflow 服务和 PostgreSQL 数据库，能够保证数据在容器重启后不丢失。

**第一步：克隆项目仓库**

打开你的 NAS 或服务器终端，执行：

```bash
git clone https://github.com/langflow-ai/langflow.git
cd langflow/docker_example
```

**第二步：配置环境变量（可选但推荐）**

在 `docker\_example` 目录下创建 `.env` 文件，用于配置数据库凭证：

```
# 数据库用户配置
POSTGRES_USER=langflow_user
POSTGRES_PASSWORD=your_secure_password_here
POSTGRES_DB=langflow_db

# Langflow 数据库连接配置
LANGFLOW_DATABASE_URL=postgresql://langflow_user:your_secure_password_here@postgres:5432/langflow_db
LANGFLOW_CONFIG_DIR=/app/langflow
```

**重要提示**：请务必将 `your\_secure\_password\_here` 替换为你自己的强密码。这些凭证用于保护你的数据库安全。

**第三步：启动服务**

执行以下命令启动 Langflow 和 PostgreSQL：

```bash
docker compose up -d
```

`-d` 参数表示后台运行。稍等片刻，服务就会启动完成。

### 环境变量说明

**必须配置的环境变量：**

变量名 | 说明 | 示例值
LANGFLOW\_DATABASE\_URL | PostgreSQL 数据库连接字符串 | postgresql://user:password@postgres:5432/langflow\_db
POSTGRES\_USER | 数据库用户名 | langflow\_user
POSTGRES\_PASSWORD | 数据库密码 | your\_secure\_password
POSTGRES\_DB | 数据库名称 | langflow\_db

**可选配置的环境变量：**

变量名 | 说明 | 默认值
LANGFLOW\_CONFIG\_DIR | Langflow 配置目录 | /app/langflow
LANGFLOW\_LOG\_LEVEL | 日志级别 | INFO

如果使用 Docker 单命令启动，这些环境变量都会使用默认值，无需手动设置。

### 端口和存储配置

**端口映射说明：**

容器内部端口：`7860`（Langflow 应用运行端口）
主机访问端口：可自定义，默认为 `7860`

如果你的主机上 7860 端口已被占用，可以修改映射配置。例如，如果要使用 8080 端口访问：

```bash
docker run -p 8080:7860 langflowai/langflow:latest
```

然后访问 `http://localhost:8080` 即可。

**存储和持久化：**

使用 Docker Compose 方案时，PostgreSQL 数据库会自动创建持久化卷，你的所有工作流、配置和数据都会被保存。即使容器重启，数据也不会丢失。

如果需要备份数据，可以定期导出 PostgreSQL 数据库内容。

## 访问应用

部署完成后，在浏览器中访问：

单命令部署：`http://localhost:7860`
Docker Compose 部署：`http://localhost:7860`
自定义端口：`http://localhost:your\_port`

首次访问时，你会看到 Langflow 的可视化编辑界面。现在你就可以开始构建你的第一个 AI 工作流了！

## 总结

通过以上步骤，你已经成功部署了 Langflow。无论选择哪种部署方案，都能快速体验这款强大的 AI 工作流平台。如果你想在外网访问，别忘了配置内网穿透哦。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/langflow-open-source-ai-workflow-platform-visual-editor-local-deployment>


## 相关文章

- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [MinDoc轻量级文档管理系统Docker极简部署，Golang高性能跨平台支持，内网穿透远程安全访问](mindoc-docker-deployment-golang-intranet-penetration.md)
- [开源无代码数据库SeaTable自托管部署指南，NAS极简配置Docker Compose一键启动](seatable-self-hosted-nas-docker-compose-deployment-guide.md)
- [自托管提醒应用MIND，Docker极简部署NAS，80+平台消息推送，内网穿透远程访问](self-hosted-reminder-app-mind-docker-nas-deployment-80-plus-messaging-platforms-internal-penetration-remote-access.md)
