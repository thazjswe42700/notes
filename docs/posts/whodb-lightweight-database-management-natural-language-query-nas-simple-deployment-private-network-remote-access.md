---
title: "WhoDB轻量级数据库管理工具，自然语言对话查询，NAS极简部署，无公网内网穿透远程访问。"
date: 2025-10-16
description: "WhoDB是轻量级数据库管理工具，采用GoLang构建，提供极速性能和增强用户体验。支持自然语言对话查询、架构可视化、内联编辑等功能，兼容PostgreSQL、MySQL、SQLite3、MongoDB、Redis等多种数据库，部署简单快速，是家庭NAS和小型团队的理想选择。"
canonical: https://www.passnat.com/posts/whodb-lightweight-database-management-natural-language-query-nas-simple-deployment-private-network-remote-access
---

## WhoDB - 轻量级数据库管理工具，让数据库管理变得简单

### 项目介绍

WhoDB 是一款轻量级（<50MB）、功能强大且用户友好的数据库管理工具，专为简化数据库管理任务而设计。它结合了 Adminer 的简洁性与增强的用户体验和性能，采用 GoLang 构建，提供卓越的速度和效率。

WhoDB 的核心特色在于其**自然语言交互功能**——你可以用对话的方式与数据交互，无需编写复杂的 SQL 查询。通过集成 Ollama、ChatGPT 和 Anthropic，你可以直接用自然语言进行数据查询和管理。

无论是小型项目还是复杂的企业系统，WhoDB 都能通过交互式架构可视化和内联编辑等功能，为你提供一致的数据库管理体验。

项目地址：[点我跳转](https://github.com/clidey/whodb)

### 核心功能特性

🗣️ 自然语言对话：告别复杂的 SQL 查询，直接用对话方式管理数据

🎨 增强的用户体验：简洁直观的界面，易于导航

⚡ 极速性能：基于 GoLang 构建，包含前端表格虚拟化技术

📊 架构可视化：交互式图表轻松可视化数据库架构

✏️ 内联编辑：直接在界面中编辑和预览数据

🗄️ 广泛的数据库支持：兼容 PostgreSQL、MySQL、SQLite3、MongoDB、Redis、MariaDB 和 ElasticSearch

📝 Scratchpad 功能：类似 Jupyter Notebook 的数据库查询界面

### 部署安装

如需在外网也能打开项目，需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

#### 最快速上手方案 - Docker 单命令启动

打开飞牛 NAS 或其他 NAS 设备，点击本地镜像 - 添加镜像 - 从 URL 添加。

**镜像地址填写：** `clidey/whodb`

等待镜像下载完成后，点击启动按钮启动容器。

#### 容器配置说明

**端口映射设置：**

容器内部端口：`8080`

建议映射到主机端口：`8080`（如果端口被占用，可修改主机端口，但容器内端口需保持为 8080）

**存储位置配置（可选）：**

如果你需要使用 SQLite 数据库并持久化数据，建议创建一个本地文件夹用于存储数据库文件。在 NAS 中创建一个文件夹（如 `/mnt/whodb\_data`），然后将其映射到容器内的 `/db` 目录。

**环境变量设置：**

可选配置（根据需要设置）：

环境变量 | 说明 | 默认值

WHODB\_OLLAMA\_HOST | Ollama 服务器地址（用于自然语言功能） | localhost

WHODB\_OLLAMA\_PORT | Ollama 服务器端口 | 11434

WHODB\_ANTHROPIC\_API\_KEY | Anthropic API 密钥（可选） | 无

WHODB\_ANTHROPIC\_ENDPOINT | Anthropic API 端点 | https://api.anthropic.com/v1

WHODB\_OPENAI\_API\_KEY | OpenAI API 密钥（可选） | 无

WHODB\_OPENAI\_ENDPOINT | OpenAI API 端点 | https://api.openai.com/v1

说明：如果你想使用自然语言对话功能，需要配置上述 API 密钥之一。如果不需要此功能，可以不设置这些变量，直接启动容器即可。

**启动容器**

勾选开机自动启动后，点击下一步启动容器。创建成功后，可以在浏览器中访问：

```
http://你的NAS地址:8080
```

即可进入 WhoDB 的管理界面。

### 使用建议

**首次使用**：无需任何配置，直接启动即可使用。WhoDB 会自动检测你的数据库连接

**自然语言功能**：如果想体验 AI 对话查询功能，建议先配置 Ollama（推荐使用 Llama 3.1 8b 模型）或配置 OpenAI/Anthropic API 密钥

**数据持久化**：如果使用 SQLite，务必配置存储卷映射，避免容器重启后数据丢失

### 总结

WhoDB 是一款开箱即用的轻量级数据库管理工具，特别适合家庭 NAS 用户和小型团队使用。它的自然语言交互功能让数据库管理变得更加简单直观，即使是技术小白也能轻松上手。通过简单的 Docker 部署，你就能拥有一个功能完整、性能卓越的数据库管理平台。

本篇教程结束

---

原文：<https://www.passnat.com/posts/whodb-lightweight-database-management-natural-language-query-nas-simple-deployment-private-network-remote-access>
