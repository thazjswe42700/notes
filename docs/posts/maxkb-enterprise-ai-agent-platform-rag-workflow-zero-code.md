---
title: "企业级智能体平台MaxKB一键部署，支持RAG工作流编排，零代码赋能企业系统智能问答"
date: 2025-10-16
description: "发现MaxKB是企业级智能体平台，支持RAG增强生成、工作流编排、多模态内容处理。集成DeepSeek、Claude等多种大模型，原生支持文本、图像、音频、视频。提供零代码智能问答、知识库构建、工作流自动化能力。Docker一键部署，适合智能客服、企业知识库、教育培训等场景。"
canonical: https://www.passnat.com/posts/maxkb-enterprise-ai-agent-platform-rag-workflow-zero-code
---

## MaxKB - 企业级智能体平台一键部署指南

### 项目介绍

MaxKB（Max Knowledge Brain）是一个开源的企业级智能体构建平台，专为需要强大AI能力的企业和开发者设计。这个项目集成了先进的检索增强生成（RAG）管道、支持复杂工作流编排、提供MCP工具调用能力，广泛应用于智能客服、企业知识库、学术研究和教育培训等多个领域。

与其他同类产品相比，MaxKB不仅支持丰富的大模型生态（包括DeepSeek、Llama、Qwen等私有模型和OpenAI、Claude、Gemini等公开模型），还原生支持文本、图像、音频、视频等多模态内容。通过强大的工作流引擎和函数库，你可以轻松编排复杂的AI业务流程，零代码集成到现有系统中，快速为企业系统赋能智能问答能力。

项目地址：[点我跳转](https://github.com/1Panel-dev/MaxKB)

### 部署安装

若需在外网访问你的MaxKB实例，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细操作步骤可参考 [内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

#### Docker 快速部署

MaxKB 提供了最简洁的 Docker 单命令部署方案，适合所有技术水平的用户。

**第一步：拉取并运行镜像**

打开你的 NAS 设备或服务器终端，执行以下命令：

```bash
docker run -d --name=maxkb --restart=always -p 8080:8080 -v ~/.maxkb:/var/lib/postgresql/data -v ~/.python-packages:/opt/maxkb/app/sandbox/python-packages 1panel/maxkb
```

这条命令会：

创建一个名为 'maxkb' 的容器

设置容器开机自动启动

将容器的 8080 端口映射到主机的 8080 端口

挂载数据存储目录以保证数据持久化

**第二步：端口配置说明**

如果你的系统中 8080 端口已被占用，可以修改上述命令中的端口映射。例如改为 8888 端口：

```
-p 8888:8080
```

前面的数字是你主机上要使用的端口，后面的 8080 是容器内部的端口，保持不变即可。

**第三步：存储配置说明**

命令中包含两个存储挂载：

'~/.maxkb:/var/lib/postgresql/data' - 数据库存储目录，保存所有知识库和配置数据

'~/.python-packages:/opt/maxkb/app/sandbox/python-packages' - Python 依赖包存储目录

这些目录会自动创建，无需手动干预。

**第四步：访问应用**

等待容器启动完成（通常需要 30-60 秒），然后在浏览器中访问：

```
http://你的服务器IP:8080
```

使用默认管理员账号登录：

用户名：'admin'

密码：'MaxKB@123..'

登录成功后，你就可以开始创建知识库、配置AI模型、搭建智能体工作流了。

**第五步：首次配置建议**

登录后建议立即修改默认密码，确保系统安全。然后根据你的需求配置对应的大模型API密钥（如OpenAI、Claude等），就可以开始构建你的企业级智能体了。

### 项目亮点

RAG 智能问答：支持文档上传和在线爬取，自动分割和向量化，显著降低大模型幻觉

工作流编排：强大的流程引擎让你无需编码即可构建复杂的AI业务逻辑

多模态支持：原生支持文本、图像、音频、视频的输入输出

模型无关：兼容各类主流大模型，灵活选择

企业级功能：内置SSO、访问控制等企业安全特性

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/maxkb-enterprise-ai-agent-platform-rag-workflow-zero-code>
