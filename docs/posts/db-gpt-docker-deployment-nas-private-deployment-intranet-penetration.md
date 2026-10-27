---
title: "开源数据应用框架DB-GPT Docker部署指南 NAS私有化部署与内网穿透配置"
date: 2025-10-16
description: "DB-GPT是开源AI原生数据应用框架，集成AWEL工作流和多智能体系统。支持Text2SQL、RAG、模型管理等功能，助力企业快速构建数据智能应用。本文详解Docker Compose部署方案，包括环境配置、服务启动、端口映射及数据持久化，助你轻松搭建属于自己的数据应用。"
canonical: https://www.passnat.com/posts/db-gpt-docker-deployment-nas-private-deployment-intranet-penetration
---

## 项目介绍

DB-GPT 是一个开源的 AI 原生数据应用开发框架，集成了 AWEL（智能工作流表达语言）和多智能体系统。它通过多模型管理、Text2SQL 优化、RAG 框架、多智能体协作等先进技术能力，让大模型应用与数据的交互变得更加简洁高效。

在 Data 3.0 时代，企业和开发者可以基于模型和数据库，用更少的代码构建自己独特的数据应用。DB-GPT 支持私有域问答、多数据源交互、生成式商业智能（GBI）、自动化微调等功能，让数据驱动的决策变得触手可得。无论你是数据分析师还是开发者，都能通过 DB-GPT 快速构建属于自己的数据智能应用。

项目地址： [点我跳转](https://github.com/eosphoros-ai/DB-GPT)

## 部署安装

如果你想在公网环境中随时随地访问 DB-GPT，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册。具体操作步骤可以参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，让你的应用轻松暴露到互联网。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，一次性配置完成所有服务。

**第一步：准备配置文件**

复制下列的 docker-compose 配置文件到你的服务器：

```yaml
# 运行此文件前，需要在环境中准备 SILICONFLOW_API_KEY
# 执行命令：SILICONFLOW_API_KEY=你的密钥 docker compose up -d

services:
  db:
    image: mysql/mysql-server
    environment:
      MYSQL_USER: 'user'
      MYSQL_PASSWORD: 'password'
      MYSQL_ROOT_PASSWORD: 'aa123456'
    ports:
      - 3306:3306
    volumes:
      - dbgpt-myql-db:/var/lib/mysql
      - ./docker/examples/my.cnf:/etc/my.cnf
      - ./docker/examples/sqls:/docker-entrypoint-initdb.d
      - ./assets/schema/dbgpt.sql:/docker-entrypoint-initdb.d/dbgpt.sql
    restart: unless-stopped
    networks:
      - dbgptnet

  webserver:
    image: eosphorosai/dbgpt-openai:latest
    command: dbgpt start webserver --config /app/configs/dbgpt-proxy-siliconflow-mysql.toml
    environment:
      - SILICONFLOW_API_KEY=${SILICONFLOW_API_KEY}
      - MYSQL_PASSWORD=aa123456
      - MYSQL_HOST=db
      - MYSQL_PORT=3306
      - MYSQL_DATABASE=dbgpt
      - MYSQL_USER=root
    volumes:
      - ./configs:/app/configs
      - /data:/data
      - /data/models:/app/models
      - dbgpt-data:/app/pilot/data
      - dbgpt-message:/app/pilot/message
    depends_on:
      - db
    ports:
      - 5670:5670/tcp
    restart: unless-stopped
    networks:
      - dbgptnet
    ipc: host

volumes:
  dbgpt-myql-db:
  dbgpt-data:
  dbgpt-message:
  dbgpt-alembic-versions:

networks:
  dbgptnet:
    driver: bridge
    name: dbgptnet
```

**第二步：配置必要的环境变量**

在启动容器前，你需要获取 SiliconFlow API 密钥。访问 [SiliconFlow 官网](https://siliconflow.cn)，注册账号并获取你的 API 密钥。

**第三步：启动服务**

打开你的 NAS 设备或服务器终端，执行以下命令启动所有服务：

```
SILICONFLOW_API_KEY=你的实际密钥 docker compose up -d
```

系统会自动拉取 MySQL 和 DB-GPT 镜像，并完成初始化。整个过程可能需要几分钟，请耐心等待。

**第四步：访问应用**

部署完成后，打开浏览器访问：

```
http://你的服务器IP:5670
```

即可进入 DB-GPT 的 Web 界面，开始构建你的数据应用。

### 环境变量说明

**必须设置：**

'SILICONFLOW\_API\_KEY'：SiliconFlow 的 API 密钥，用于调用大语言模型服务

**数据库配置（已预设，无需修改）：**

'MYSQL\_PASSWORD'：MySQL 密码（默认：aa123456）

'MYSQL\_HOST'：MySQL 主机地址（默认：db）

'MYSQL\_PORT'：MySQL 端口（默认：3306）

'MYSQL\_DATABASE'：数据库名称（默认：dbgpt）

'MYSQL\_USER'：MySQL 用户（默认：root）

### 端口映射说明

'5670'：DB-GPT Web 服务端口，这是你访问应用的主要入口

'3306'：MySQL 数据库端口，用于数据存储和管理

如果你的服务器上 5670 或 3306 端口已被占用，可以修改 docker-compose 文件中的端口映射。例如，要将 Web 服务改为 8080 端口，修改为：

```
ports:
  - 8080:5670/tcp
```

然后访问 'http://你的服务器IP:8080' 即可。

### 数据持久化

配置文件已包含以下持久化卷：

'dbgpt-myql-db'：MySQL 数据库文件，确保你的数据不会因为容器重启而丢失

'dbgpt-data'：应用数据存储目录

'dbgpt-message'：消息记录目录

'/data/models'：如果你有本地模型文件，可以挂载到此目录供应用使用

### 开机自动启动

容器已配置 'restart: unless-stopped'，这意味着服务器重启后，DB-GPT 会自动启动，无需手动干预。

## 总结

通过上述步骤，你就可以在自己的服务器或 NAS 上成功部署 DB-GPT。这个框架为你打开了数据智能应用的大门——无论是构建私有知识库问答系统、连接多个数据源进行智能分析，还是开发复杂的多智能体工作流，DB-GPT 都能胜任。

立即开始你的数据智能之旅吧！

---

原文：<https://www.passnat.com/posts/db-gpt-docker-deployment-nas-private-deployment-intranet-penetration>


## 相关文章

- [开源Dify平台极简部署，LLM应用开发工具链完整集成，支持内网穿透远程访问](dify-open-source-deployment-llm-application-development-tools.md)
- [FastGPT知识库AI Agent平台Docker部署指南，NAS一键启动，内网穿透远程访问企业级智能问答系统](fastgpt-knowledge-base-ai-agent-docker-deployment-nas-one-click-setup-internal-network-penetration-remote-access-enterprise-intelligent-qa-system.md)
- [企业级智能体平台MaxKB一键部署，支持RAG工作流编排，零代码赋能企业系统智能问答](maxkb-enterprise-ai-agent-platform-rag-workflow-zero-code.md)
- [Kotaemo智能文档对话平台开源部署，私有RAG系统本地化搭建与内网穿透远程访问完整指南](kotaemo-smart-document-chat-open-source-deployment-private-rag-system-localization-intranet-penetration-remote-access-complete-guide.md)
- [开源个人数据管理框架Eidos本地部署指南，SQLite私密数据库离线使用，NAS内网穿透远程访问完整教程](eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial.md)
