---
title: "深度网络研究工具本地部署指南，NAS一键启动Deep Research Web UI，支持多AI提供商"
date: 2025-10-16
description: "Deep Research Web UI是基于Nuxt 3的网络研究工具，集成OpenAI、DeepSeek等多家AI服务商和搜索引擎。支持树形结构展示研究过程、多语言搜索、导出Markdown或PDF报告，所有配置和API请求在本地浏览器进行，确保数据安全。适合知识工作者、研究人员和内容创作者提升工作效率。"
canonical: https://www.passnat.com/posts/deep-network-research-tool-local-deployment-guide-nas-one-click-launch-deep-research-web-ui-multi-ai-providers
---

## 项目介绍

Deep Research Web UI 是一款强大的网络研究工具，它为深度研究提供了一个优雅的可视化界面。基于 Nuxt 3 框架打造，该项目集成了多家 AI 服务商和网络搜索引擎，让用户能够进行深层次的信息检索和分析。

项目采用树形结构实时展示研究过程，支持多语言搜索，可将研究报告导出为 Markdown 或 PDF 格式。最棒的是，所有配置和 API 请求都在浏览器本地进行，确保了数据的安全性和隐私性。无论你是知识工作者、研究人员还是内容创作者，都能通过这个工具大幅提升工作效率。

支持的 AI 提供商包括 OpenAI、DeepSeek、Ollama、SiliconFlow 等多家主流服务商，网络搜索则支持 Tavily 和 Firecrawl。

项目地址：[点我跳转](https://github.com/AnotiaWang/deep-research-web-ui)

## 部署安装

如果需要在外网环境下访问你的研究工具，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了全平台的穿透解决方案，具体的上手步骤可以 [点我查看](https://www.passnat.com/posts/p)。

### 最快速部署方案

打开你的 NAS 或服务器，使用以下 Docker 命令一键启动：

```bash
docker run -p 3000:3000 \
  -e NUXT_PUBLIC_SERVER_MODE=true \
  -e NUXT_AI_API_KEY=your-ai-api-key \
  -e NUXT_WEB_SEARCH_API_KEY=your-search-api-key \
  -e NUXT_PUBLIC_AI_PROVIDER=openai-compatible \
  -e NUXT_PUBLIC_AI_MODEL=gpt-4o-mini \
  -e NUXT_PUBLIC_WEB_SEARCH_PROVIDER=tavily \
  anotia/deep-research-web:latest
```

如果你更倾向于使用 .env 文件管理配置，也可以这样做：

```bash
docker run -p 3000:3000 --env-file .env anotia/deep-research-web:latest
```

### 通过 NAS 界面部署

打开飞牛 NAS 或其他 NAS 设备的容器管理界面，点击'本地镜像'-'添加镜像'-'从 URL 添加'。

在镜像地址栏填入：anotia/deep-research-web:latest，等待镜像下载完成后点击启动按钮。

进入容器配置界面，勾选'开机自动启动'以便重启后自动恢复服务。

**端口映射设置**

容器内部使用 3000 端口。如果你的 NAS 上 3000 端口已被占用，可以修改主机端口（如改为 3001、3002 等），但容器内部端口务必保持为 3000。

**环境变量配置**

必须设置的环境变量：

变量名 | 说明 | 示例
NUXT\_PUBLIC\_SERVER\_MODE | 启用服务器模式 | true
NUXT\_AI\_API\_KEY | AI 服务的 API 密钥 | sk-xxxxx
NUXT\_WEB\_SEARCH\_API\_KEY | 网络搜索服务的 API 密钥 | tvly-xxxxx

可选设置的环境变量：

变量名 | 说明 | 默认值
NUXT\_PUBLIC\_AI\_PROVIDER | AI 提供商类型 | openai-compatible
NUXT\_PUBLIC\_AI\_MODEL | 使用的 AI 模型 | gpt-4o-mini
NUXT\_PUBLIC\_AI\_CONTEXT\_SIZE | 上下文长度 | 128000
NUXT\_PUBLIC\_WEB\_SEARCH\_PROVIDER | 搜索提供商 | tavily
NUXT\_PUBLIC\_WEB\_SEARCH\_CONCURRENCY\_LIMIT | 最大并发搜索数 | 2
NUXT\_PUBLIC\_WEB\_SEARCH\_SEARCH\_LANGUAGE | 搜索语言 | en
NUXT\_PUBLIC\_TAVILY\_ADVANCED\_SEARCH | 使用 Tavily 高级搜索 | false
NUXT\_PUBLIC\_TAVILY\_SEARCH\_TOPIC | Tavily 搜索主题 | general

其他变量保持默认即可，无需特殊配置。

**完成部署**

配置完成后点击启动容器。稍等片刻，打开浏览器访问 http://你的NAS\_IP:3000，就能看到 Deep Research Web UI 的界面了。

到此为止，部署就全部完成了。你现在可以开始使用这个强大的研究工具进行深度网络信息检索。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/deep-network-research-tool-local-deployment-guide-nas-one-click-launch-deep-research-web-ui-multi-ai-providers>


## 相关文章

- [免费DeepSeek API接口一键部署，OpenAI兼容格式，NAS本地化私有部署方案](free-deepseek-api-deployment-openai-compatible-nas-local-private.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [轻量级全文搜索引擎ZincSearch快速部署指南，NAS极简配置Docker一键启动](zinc-search-nas-docker-deployment-guide.md)
- [Open WebUI 自建 AI 对话站：飞牛 NAS Docker 部署接云端模型不用显卡，内网穿透出门手机也能聊](open-webui-self-hosted-private-ai-chat-platform-docker-simple-deployment-offline-privacy.md)
- [本地部署AI大模型Ollama，离线运行Gemma Llama DeepSeek，支持内网穿透远程访问](local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling.md)
