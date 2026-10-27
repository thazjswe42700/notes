---
title: "自建私密搜索引擎SearXNG，聚合245个搜索源零追踪，NAS极简Docker部署内网穿透远程访问"
date: 2025-10-16
description: "自由开源的隐私搜索引擎SearXNG，聚合超245个搜索服务，无追踪无分析。支持Docker一键部署到NAS，搭建个人私密搜索实例。保护您的搜索隐私，摆脱大型搜索引擎数据收集。完整部署配置指南，包含端口映射、存储设置、内网穿透方案。"
canonical: https://www.passnat.com/posts/searxng-private-search-engine-245-sources-nas-docker-deployment
---

## 项目介绍

SearXNG 是一个自由开源的互联网元搜索引擎，致力于保护用户隐私。它能够聚合来自超过 245 个搜索服务的搜索结果，用户既不会被追踪也不会被分析建档。无论你是想要一个隐私友好的搜索体验，还是希望摆脱大型搜索引擎的数据收集，SearXNG 都是完美的解决方案。

项目支持多种部署方式，可以轻松搭建属于自己的私密搜索引擎实例。即使你不信任任何第三方服务，也可以在自己的服务器上快速部署一个完全独立的搜索引擎。

项目地址：[点我跳转](https://github.com/searxng/searxng)

## 部署安装

如果你需要在外网也能访问自己部署的搜索引擎实例，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在外地也能随时使用自己的私密搜索引擎。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

### 使用 Docker Compose 部署

打开飞牛 NAS 的 Docker 应用，切到 Compose，点右上角的「新增项目」。

项目名称填 searxng，路径挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面的编排整段贴进去：

```yaml
services:
  searxng:
    image: searxng/searxng:latest
    container_name: searxng
    restart: unless-stopped
    network_mode: bridge
    ports:
      - "3486:8080"
```

勾选「创建后立即启动」，点「创建」，等列表变成「正在运行」就装好了。

编排里只认一处：冒号左边的 3486 是浏览器里访问的端口（可以换成别的没被占用的端口），右边的 8080 是 SearXNG 在容器里的默认端口，保持不变。它不需要数据库，也不用挂载目录，开箱即用。

### 访问你的搜索引擎

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:3486
```

稍等片刻，一个简洁高效的私密搜索引擎界面就会呈现在你面前。你现在拥有了一个完全属于自己的搜索引擎，所有的搜索记录都只保存在本地，不会被任何第三方追踪。

本篇教程到此结束，祝你享受私密搜索的体验！

---

原文：<https://www.passnat.com/posts/searxng-private-search-engine-245-sources-nas-docker-deployment>
