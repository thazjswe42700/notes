---
title: "免费私密本地AI搜索引擎，无需API密钥NAS一键部署，多LLM模型聚合智能问答"
date: 2025-10-16
description: "免费开源的本地AI搜索引擎FreeAskInternet完整部署教程。支持ChatGPT、Kimi、Qwen等多种LLM模型，无需API密钥和GPU硬件。提供Docker Compose一键部署方案，适配飞牛NAS和其他Docker环境，让你轻松构建私密、高效的企业级AI问答系统。"
canonical: https://www.passnat.com/posts/free-private-local-ai-search-engine-no-api-key-nas-one-click-deploy-multi-llm-aggregation-qa
---

## 项目介绍

FreeAskInternet 是一款完全免费、私密、本地运行的搜索聚合与AI问答系统。它完全模仿 Perplexity.ai 的功能，但无需任何API密钥、GPU硬件或网络服务费用。这个项目基于多搜索引擎聚合和先进的LLM技术，将网络搜索结果与AI智能问答完美结合，让你在本地就能体验企业级的AI搜索体验。

项目支持多种LLM模型选择，包括免费的ChatGPT3.5、Kimi、Qwen、ZhipuAI等，同时还支持自定义LLM（如ollama），给予用户最大的灵活性。无论是技术爱好者还是普通用户，都能轻松部署和使用。

项目地址：[点我跳转](https://github.com/nashsu/FreeAskInternet)

## 部署安装

若想在外网也能访问你的项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这是一个可靠的内网穿透解决方案。具体的部署指南可以 [点我查看](https://www.passnat.com/posts/p)，帮助你快速上手全平台的内网穿透配置。

### 最快速上手方式

FreeAskInternet 提供了开箱即用的 Docker Compose 部署方案，让你一条命令即可启动完整的AI搜索系统。

**复制下列的配置文件**

```yaml
services:
  backend:
    image: docker.io/nashsu/free_ask_internet:latest
    depends_on:
      - llm-freegpt35
    restart: on-failure
 

  freeaskinternet-ui:
    image: docker.io/nashsu/free_ask_internet_ui:latest
    ports:
      - "3000:80"
    environment:
      BACKEND_HOST: "backend:8000"
    depends_on:
      - backend
    restart: always

  chatgpt-next-web:
    image: yidadaa/chatgpt-next-web
    ports:
      - "3030:3000"
    environment:
      OPENAI_API_KEY: "FreeAskInternet"
      # CODE: "FreeAskInternet" # 如果你想要设置页面的访问密码，请修改这里
      BASE_URL: "http://backend:8000"
      CUSTOM_MODELS: "-all,+gpt-3.5-turbo"
    depends_on:
      - llm-freegpt35
    restart: always

  llm-freegpt35:
    image: missuo/freegpt35:latest
    restart: always

  llm-kimi:
    image: vinlic/kimi-free-api:latest
    restart: always
    environment:
      - TZ=Asia/Shanghai

  llm-glm4:
    image: vinlic/glm-free-api:latest
    restart: always
    environment:
      - TZ=Asia/Shanghai

  llm-qwen:
    image: vinlic/qwen-free-api:latest
    restart: always
    environment:
      - TZ=Asia/Shanghai

  searxng:
    image: docker.io/searxng/searxng:latest
    volumes:
      - ./searxng:/etc/searxng:rw
    environment:
      - SEARXNG_BASE_URL=https://${SEARXNG_HOSTNAME:-localhost}/
    cap_drop:
      - ALL
    cap_add:
      - CHOWN
      - SETGID
      - SETUID
    logging:
      driver: 'json-file'
      options:
        max-size: '1m'
        max-file: '1'
    restart: always
```

**打开飞牛 NAS 或其他的 NAS 设备**

进入 NAS 的 Docker 管理界面，选择 Docker Compose 选项，将上述配置文件粘贴到编辑框中。这个配置文件包含了 FreeAskInternet 的完整后端服务、前端UI、多个LLM模型接口和搜索引擎等所有必要的组件。

系统会自动拉取所有相关的 Docker 镜像并启动容器。整个过程会根据你的网络速度需要几分钟时间，请耐心等待。

**端口映射设置**

该配置默认映射了两个主要端口：
3000 端口：FreeAskInternet 主界面（推荐使用）
3030 端口：ChatGPT-Next-Web 备用界面

如果你的 NAS 上这些端口已被占用，可以修改左边的端口号。例如，将 '3000:80' 改为 '3001:80' 即可将服务映射到 3001 端口。容器内部的端口（冒号后的数字）需要保持不变。

**存储与数据持久化**

配置中的 ./searxng:/etc/searxng:rw 这一行会将搜索引擎的配置文件映射到本地。建议在 NAS 上创建一个名为 searxng 的文件夹，以便保存搜索引擎的配置和缓存数据。

**环境变量设置说明**

**环境变量必须设置：**

BACKEND\_HOST：后端服务地址，默认为 backend:8000（Docker 内部通信，保持不变）
BASE\_URL：API 基础地址，默认为 http://backend:8000（Docker 内部通信，保持不变）

**环境变量可选设置：**

CODE：如果你想为 ChatGPT-Next-Web 界面设置访问密码，可以取消注释 # CODE: 'FreeAskInternet' 这一行，并修改为你想要的密码
TZ：时区设置，已默认配置为 Asia/Shanghai（中国时区），其他地区可根据需要修改

其他环境变量都已按照最优配置预设，通常无需修改。

**启动容器**

配置完成后，点击启动或部署按钮。系统会自动下载所有必要的镜像并启动容器。由于涉及多个镜像和服务，首次启动可能需要 3-5 分钟。

**访问应用**

启动完成后，你可以通过以下地址访问 FreeAskInternet：

主界面：http://你的NAS地址:3000
备用界面：http://你的NAS地址:3030

打开任一地址，你就会看到 FreeAskInternet 的美观界面。在搜索框中输入你的问题，系统会自动调用多个搜索引擎进行聚合搜索，然后由 AI 模型基于搜索结果为你生成答案。整个过程完全本地化，无需任何外部 API 密钥。

到这里，FreeAskInternet 的部署就完成了！你现在拥有了一个私密、免费、强大的本地 AI 搜索系统。

**本篇教程结束**

---

原文：<https://www.passnat.com/posts/free-private-local-ai-search-engine-no-api-key-nas-one-click-deploy-multi-llm-aggregation-qa>
