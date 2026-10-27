---
title: "在线编译器探索平台Compiler Explorer，支持30+编程语言实时代码编译查看汇编代码"
date: 2025-10-16
description: "Compiler Explorer是支持30+编程语言的在线编译器平台，实时展示源代码到汇编代码的转换过程。提供直观界面、多种编译器优化选项，每周处理300万+编译请求。完全开源，支持Docker快速部署到本地NAS或服务器，是学习编译原理和代码性能优化的理想工具。"
canonical: https://www.passnat.com/posts/compiler-explorer-online-ide-30-programming-languages
---

## 项目介绍

Compiler Explorer 是一个功能强大的在线编译器探索平台，支持超过 30 种编程语言，包括 C、C++、Rust、Go、Python、Java 等。这个项目让开发者能够实时编写代码并观察其编译过程，直观地看到源代码如何被转换为汇编代码。

该平台自 2012 年创立以来，已经成为全球开发者学习编译原理、优化代码性能的必备工具。每周处理超过 300 万次编译请求，拥有庞大的用户基数。无论你是想深入理解编译器的工作原理，还是想优化你的代码性能，Compiler Explorer 都是一个理想的选择。

界面直观易用，支持多种编译器和优化选项，让你可以轻松对比不同编译器的输出结果。项目完全开源，可以自行部署到本地或内网环境中使用。

项目地址： [点我跳转](https://github.com/compiler-explorer/compiler-explorer)

## 部署安装

如果你需要在公网上访问本地部署的 Compiler Explorer，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作指南可以 [参考这里](https://www.passnat.com/posts/p) 了解全平台内网穿透的快速上手方法。

### Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统，进入 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
compilerexplorer/compiler-explorer:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

勾选 **开机自动启动** 后进入下一步。

### 端口配置

在端口设置界面，配置以下映射：

容器端口：10240（这是应用内部使用的端口，请保持不变）

主机端口：可设置为 10240 或其他未被占用的端口

如果你的系统提示 10240 端口已被占用，可以修改主机端口为其他值（如 8080、9090 等），但容器内的端口必须保持为 10240。

### 存储配置

如果你需要保存自定义的编译器配置或其他数据，建议创建一个本地文件夹并映射到容器内：

主机路径：'/your/local/path/compiler-explorer'

容器路径：'/compiler-explorer/etc/config'

这样可以确保你的配置在容器重启后不会丢失。

### 环境变量设置

Compiler Explorer 提供了多个可配置的环境变量，以下是常用选项：

可选设置项：

NODE\_ENV：运行环境（默认：production）

PORT：应用监听端口（默认：10240）

LANGUAGES：限制加载的编程语言（默认：全部）

大多数情况下，你无需修改这些变量。使用默认配置即可正常运行。

其他参数保持默认即可，点击下一步启动容器。

### 访问应用

容器启动成功后，你可以通过以下地址访问 Compiler Explorer：

```
http://你的设备IP:10240
```

例如，如果你的 NAS 地址是 '192.168.1.100'，则访问地址为 'http://192.168.1.100:10240'

打开浏览器访问上述地址，你就能看到 Compiler Explorer 的界面，开始探索编译过程了。

### 部署完成

到此为止，Compiler Explorer 已经成功部署在你的本地环境中。你现在可以在任何支持网络连接的设备上访问它，进行代码编译和汇编分析。

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/compiler-explorer-online-ide-30-programming-languages>


## 相关文章

- [直播录制神器DouyinLiveRecorder，50+平台一键自动守护，NAS循环值守永不错过精彩](douyin-live-recorder-50-platforms-automatic-recording-nas.md)
- [code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码](codeserver.md)
- [无代码可视化爬虫平台 Spider-Flow，流程图定义任务无需编程，NAS 一键部署内网穿透远程访问](no-code-web-scraper-spider-flow-flowchart-nas-deployment.md)
- [Swagger Editor：NAS Docker 部署 + 内网穿透](swagger-editor-nas-docker-openapi-api-docs-intranet-penetration.md)
- [录播姬BililiveRecorder开源B站直播录制工具Docker一键部署指南，NAS远程访问。](bilibili-recorder-docker-nas-remote-access-guide.md)
