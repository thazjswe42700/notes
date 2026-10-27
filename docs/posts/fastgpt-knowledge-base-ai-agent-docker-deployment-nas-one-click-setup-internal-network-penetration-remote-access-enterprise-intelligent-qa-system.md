---
title: "FastGPT知识库AI Agent平台Docker部署指南，NAS一键启动，内网穿透远程访问企业级智能问答系统"
date: 2025-10-16
description: "FastGPT是基于大语言模型的知识库和AI Agent构建平台，提供数据处理、RAG检索、模型调用等完整能力。支持可视化Flow工作流编排，让用户轻松构建智能问答系统。本文详细介绍FastGPT的Docker和Docker-Compose部署方法，包括环境变量配置、存储设置等完整步骤。"
canonical: https://www.passnat.com/posts/fastgpt-knowledge-base-ai-agent-docker-deployment-nas-one-click-setup-internal-network-penetration-remote-access-enterprise-intelligent-qa-system
---

## 项目介绍

FastGPT 是一个基于大语言模型的知识库平台和AI Agent构建平台。它提供了开箱即用的数据处理、RAG检索、模型调用等完整能力，同时支持通过可视化Flow工作流进行编排，让你能够轻松构建和部署复杂的智能问答系统，无需繁琐的配置和开发。

无论你是想快速搭建一个企业级知识库系统，还是需要构建复杂的AI工作流应用，FastGPT都能为你提供强大的支持。界面友好易用，即使是技术小白也能通过拖拽式的工作流编排实现专业级的AI应用。

项目地址：[点我跳转](https://github.com/labring/FastGPT)

## 部署安装

如果你想在外网也能访问到你的FastGPT实例，可以考虑使用内网穿透服务。推荐使用[帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的部署和穿透操作方法可以[点我查看](https://www.passnat.com/posts/p)内网穿透完整上手指南。

### Docker 快速启动

打开你的NAS设备或Docker管理面板，使用以下命令快速启动FastGPT：

```bash
docker run -d \
  --name fastgpt \
  -p 3000:3000 \
  -e OPENAI_API_KEY=your-api-key \
  -e DB_HOST=localhost \
  labring/fastgpt:latest
```

镜像地址填写：labring/fastgpt:latest

### Docker-Compose 部署（推荐）

复制以下配置文件到你的NAS或服务器中：

```yaml
version: '3.8'

services:
  fastgpt:
    image: labring/fastgpt:latest
    container_name: fastgpt
    ports:
      - "3000:3000"
    environment:
      - OPENAI_API_KEY=your-openai-api-key
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_NAME=fastgpt
      - DB_USER=fastgpt
      - DB_PASSWORD=fastgpt_password
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
    restart: always

  postgres:
    image: postgres:15
    container_name: fastgpt-postgres
    environment:
      - POSTGRES_DB=fastgpt
      - POSTGRES_USER=fastgpt
      - POSTGRES_PASSWORD=fastgpt_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: always

  redis:
    image: redis:7-alpine
    container_name: fastgpt-redis
    restart: always
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

打开你的NAS管理面板或Docker Compose工具，将上述配置复制进去。

**重要配置说明：**

这里需要修改的是你的OpenAI API密钥，其他配置保持不变即可。

如果提示端口被占用，可以修改上面的3000:3000中的第一个数字（宿主机端口），但容器内的3000端口需要保持不变。

**存储配置：**

FastGPT需要持久化存储数据库和缓存数据。上述配置已经自动创建了postgres\_data和redis\_data两个数据卷来保存数据，无需额外配置。

**环境变量设置：**

必须设置：

OPENAI\_API\_KEY：你的OpenAI API密钥（必填）

可选设置：

DB\_HOST：数据库地址（默认：postgres）

DB\_PORT：数据库端口（默认：5432）

DB\_NAME：数据库名称（默认：fastgpt）

DB\_USER：数据库用户名（默认：fastgpt）

DB\_PASSWORD：数据库密码（默认：fastgpt\_password）

REDIS\_URL：Redis连接地址（默认：redis://redis:6379）

其他配置保持默认即可，直接启动容器。

启动成功后，打开浏览器访问http://你的IP:3000，就能看到FastGPT的管理界面了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/fastgpt-knowledge-base-ai-agent-docker-deployment-nas-one-click-setup-internal-network-penetration-remote-access-enterprise-intelligent-qa-system>


## 相关文章

- [开源Dify平台极简部署，LLM应用开发工具链完整集成，支持内网穿透远程访问](dify-open-source-deployment-llm-application-development-tools.md)
- [企业级智能体平台MaxKB一键部署，支持RAG工作流编排，零代码赋能企业系统智能问答](maxkb-enterprise-ai-agent-platform-rag-workflow-zero-code.md)
- [开源数据应用框架DB-GPT Docker部署指南 NAS私有化部署与内网穿透配置](db-gpt-docker-deployment-nas-private-deployment-intranet-penetration.md)
- [Kotaemo智能文档对话平台开源部署，私有RAG系统本地化搭建与内网穿透远程访问完整指南](kotaemo-smart-document-chat-open-source-deployment-private-rag-system-localization-intranet-penetration-remote-access-complete-guide.md)
- [无代码可视化爬虫平台 Spider-Flow，流程图定义任务无需编程，NAS 一键部署内网穿透远程访问](no-code-web-scraper-spider-flow-flowchart-nas-deployment.md)
