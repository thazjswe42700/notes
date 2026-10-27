---
title: "开源AI搜索平台Search4All部署指南 NAS服务器Docker快速安装内网穿透远程访问"
date: 2025-10-16
description: "Search4All 是 Perplexity 的开源替代方案，集成 OpenAI、Groq、Claude 等多个大语言模型和 Google、Bing、DuckDuckGo 等搜索引擎。支持搜索缓存、结果分享、相关问题推荐，提供完整 Docker 部署方案和详细配置指南，适合个人和企业级搜索平台部署使用。"
canonical: https://www.passnat.com/posts/search4all-nas-docker-deployment-guide-intranet-penetration-remote-access
---

## 项目介绍

Search4All 是一个开源的 AI 搜索平台，它是 Perplexity 的开源替代方案。这是一个集成了多种大语言模型（OpenAI、Groq、Claude）和多个搜索引擎（Google、Bing、DuckDuckGo、SearXNG）的智能搜索系统。

该项目采用 Python 后端 + 前端 Web 界面的架构，提供了一个美观易用的搜索体验。无论你是想要一个私人的 AI 搜索助手，还是想要部署一个企业级的搜索平台，Search4All 都能满足你的需求。它支持搜索结果缓存、结果分享、相关问题推荐等多项实用功能，让你的搜索体验更加高效。

项目地址：[点我跳转](https://github.com/fatwang2/search4all)

## 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考 [这份详细的上手指南](https://www.passnat.com/posts/p)，一步步按照教程操作即可轻松实现远程访问。

### Docker 单命令部署（推荐小白用户）

打开你的 NAS 设备或服务器，找到 Docker 应用管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
docker.io/fatwang2/search4all
```

等待镜像下载完成后，点击启动按钮。

**端口映射设置**

容器内部运行在 **8800** 端口，你可以将其映射到你想要的任何端口。如果 8800 端口在你的系统中已被占用，可以修改映射的宿主机端口，但容器内的 8800 端口保持不变。

**环境变量配置**

这个项目需要配置一些关键的环境变量才能正常运行。

**必须设置的环境变量：**

OPENAI\_API\_KEY：你的 OpenAI API 密钥（格式：sk-****）
OPENAI\_BASE\_URL：OpenAI 的 API 地址，默认为 https://api.openai.com/v1（如果使用 Groq，改为 https://api.groq.com/openai/v1）
LLM\_MODEL：选择要使用的模型，如 gpt-3.5-turbo-0125、mixtral-8x7b-32768 等
BACKEND：选择搜索引擎服务，可选值为 SEARCH1API、BING、GOOGLE、SERPER、SEARCHAPI、SEARXNG
根据选择的搜索引擎，还需要配置对应的 API 密钥（如 SEARCH1API\_KEY、BING\_SEARCH\_V7\_SUBSCRIPTION\_KEY 等）

**可选配置的环境变量：**

RELATED\_QUESTIONS：是否显示相关问题，设置为 1 表示启用
CHAT\_HISTORY：是否支持基于搜索结果的持续对话，设置为 1 表示启用
GROQ\_API\_KEY：如果使用 Groq 模型，填写你的 Groq API 密钥
ANTHROPIC\_API\_KEY：如果使用 Claude 模型，填写你的 Claude API 密钥
NEXT\_PUBLIC\_GOOGLE\_ANALYTICS：Google Analytics 的测量 ID（可选，用于网站分析）
SEARXNG\_BASE\_URL：如果使用 SearXNG 搜索，填写你的 SearXNG 服务器地址

其他变量保持默认即可，点击下一步启动容器。

**访问应用**

容器启动成功后，在浏览器中访问 http://你的IP:8800（或你映射的端口），就可以开始使用 Search4All 了。

### Docker-Compose 部署

如果你倾向于使用 Docker-Compose 进行管理，可以按照以下步骤操作：

首先，下载 docker-compose 配置文件到你的机器：

```
wget https://raw.githubusercontent.com/fatwang2/search4all/main/docker-compose.yml
```

下载完成后，使用文本编辑器打开这个文件，根据你的需求修改环境变量：

```yaml
version: '3.4'

services:
  search4all:
    image: docker.io/fatwang2/search4all
    restart: unless-stopped
    ports:
      - 8800:8800
    environment:
      OPENAI_API_KEY: sk-xxx          # 替换为你的 OpenAI API 密钥
      OPENAI_BASE_URL: https://api.openai.com/v1
      LLM_MODEL: gpt-3.5-turbo-0125   # 选择你想使用的模型
      RELATED_QUESTIONS: 1             # 启用相关问题推荐
      BACKEND: SEARCH1API              # 选择搜索引擎
      SEARCH1API_KEY: xxxx             # 填写对应搜索引擎的 API 密钥
```

修改完成后，在 docker-compose.yml 文件所在的目录执行：

```bash
docker compose up -d
```

容器会在后台运行，同样通过 http://你的IP:8800 访问应用。

**端口冲突处理**

如果 8800 端口已被占用，你可以修改配置文件中的 ports 字段，例如改为 8801:8800，这样就会使用 8801 端口来访问应用。

本篇教程到此结束。现在你已经拥有了一个功能强大的 AI 搜索平台，尽情享受智能搜索带来的便利吧！

---

原文：<https://www.passnat.com/posts/search4all-nas-docker-deployment-guide-intranet-penetration-remote-access>
