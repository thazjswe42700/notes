---
title: "本地部署AI大模型Ollama，离线运行Gemma Llama DeepSeek，支持内网穿透远程访问"
date: 2025-10-16
description: "Ollama 是轻量级本地大语言模型框架，支持离线运行 Gemma、Llama、DeepSeek 等开源模型。无需云服务，提供命令行和 REST API，适合文本生成、代码编写、图像理解。本指南介绍 Docker 快速部署方法，包括镜像配置、端口映射、存储持久化和模型下载详细步骤。"
canonical: https://www.passnat.com/posts/local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling
---

## 项目介绍

Ollama 是一个轻量级、高效的本地大语言模型运行框架，让你能够在自己的电脑上快速部署和运行各类先进的 AI 模型。无需依赖云服务，完全离线运行，支持 Gemma、Llama、DeepSeek、Phi 等众多开源模型。无论是文本生成、代码编写，还是图像理解，Ollama 都能轻松胜任。它提供了简洁的命令行界面和强大的 REST API，让开发者和普通用户都能轻松上手。

项目地址：[点我跳转](https://github.com/ollama/ollama)

## 部署安装

如果你需要在外部网络访问本地部署的 Ollama 服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以 [点我查看](https://www.passnat.com/posts/p)帕斯内网穿透全平台快速入门指南。

### Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统，进入本地镜像管理界面，选择'从 URL 添加镜像'选项。

在镜像地址栏中填入官方镜像：

```
ollama/ollama
```

等待镜像下载完成后，点击启动按钮创建容器。

**容器配置说明：**

勾选'开机自动启动'确保服务在重启后自动运行，然后进入下一步。

**端口映射设置：**

Ollama 默认使用 11434 端口提供服务。在端口映射配置中，将容器内的 11434 端口映射到主机端口（建议也使用 11434）。如果你的主机上已有应用占用该端口，可以修改为其他端口号，例如 11435 或 11436，但请记住修改后的端口号以便后续访问。

**存储配置：**

Ollama 会在容器中存储下载的模型文件。建议创建一个持久化存储目录来保存这些模型，避免容器重启后数据丢失。在存储挂载配置中，将容器内的 /root/.ollama 目录映射到主机的一个文件夹（例如 /data/ollama），这样模型文件就能被永久保存。

**环境变量设置：**

基础部署不需要特别设置环境变量，保持默认配置即可。如果你需要高级配置，以下是可选的环境变量：

OLLAMA\_HOST：服务绑定地址，默认为 0.0.0.0:11434

OLLAMA\_MODELS：模型存储路径，默认为 /root/.ollama/models

OLLAMA\_NUM\_PARALLEL：并行加载模型数，默认为 1

大多数用户无需修改这些设置，直接保持默认即可。

完成以上配置后，点击启动按钮即可启动容器。

**访问服务：**

容器启动成功后，你可以通过以下方式访问 Ollama：

本地访问：http://localhost:11434

网络访问：http://你的设备IP:11434

例如，在命令行中运行：

```bash
curl http://localhost:11434/api/tags
```

如果返回模型列表信息，说明服务已正常运行。

**首次使用：**

Ollama 启动后需要下载模型文件才能使用。你可以通过 REST API 或命令行下载模型。例如，下载轻量级的 Gemma 3 1B 模型：

```bash
curl -X POST http://localhost:11434/api/pull -d '{"name":"gemma3:1b"}'
```

或者使用官方客户端下载。不同规模的模型需要不同的内存：7B 模型需要至少 8GB RAM，13B 模型需要 16GB RAM，33B 模型需要 32GB RAM。

到这里，Ollama 就已经成功部署并可以使用了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/local-deploy-ai-ollama-offline-gemma-llama-deepseek-intranet-tunneling>
