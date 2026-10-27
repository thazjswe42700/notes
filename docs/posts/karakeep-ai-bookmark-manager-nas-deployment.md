---
title: "自托管个人知识库Karakeep，AI智能标签书签管理，NAS极简部署内网穿透远程访问"
date: 2025-10-16
description: "Karakeep是功能强大的自托管书签管理应用，集书签、笔记、AI智能标签、全文搜索、RSS订阅于一身。支持浏览器插件快速保存、移动端随时访问，集成OpenAI实现自动标签和内容总结。基于NextJS、Drizzle ORM和Meilisearch构建，是信息收集爱好者的理想知识库解决方案。"
canonical: https://www.passnat.com/posts/karakeep-ai-bookmark-manager-nas-deployment
---

## Karakeep - 打造你的个人知识库

### 项目介绍

Karakeep（前身为 Hoarder）是一款专为信息收集爱好者打造的自托管书签管理应用。如果你经常在社交媒体、新闻网站上发现有趣的文章、工具或内容，却苦于没有好的方式来管理和查找这些'宝藏'，那么 Karakeep 就是为你而生的。

这款应用基于 NextJS、Drizzle ORM 和 Meilisearch 等现代技术栈构建，集合了书签管理、笔记记录、AI 智能标签、全文搜索、RSS 订阅等强大功能于一身。无论是收藏网页链接、保存 PDF 文档，还是存储图片和笔记，Karakeep 都能帮你井井有条地管理这些信息。更棒的是，它支持浏览器插件快速保存、移动端应用随时访问，以及与 OpenAI 的集成实现自动标签和内容总结。

[点我跳转](https://github.com/MohamedBassem/hoarder-app)

项目地址：

### 部署安装

如果你想在办公室、咖啡厅或其他地方也能访问你的 Karakeep 应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备不时之需。详细的操作步骤可以参考 [这份完整指南](https://www.passnat.com/posts/p)，涵盖了全平台的快速上手方法。

#### 使用 Docker Compose 快速部署

这是最推荐的部署方式，只需复制下面的配置文件即可：

```yaml
version: '3.8'

services:
  hoarder:
    image: ghcr.io/hoarder-app/hoarder:latest
    container_name: karakeep
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NEXTAUTH_SECRET=your-secret-key-here
      - NEXTAUTH_URL=http://localhost:3000
      - DATABASE_URL=postgresql://hoarder:hoarder@postgres:5432/hoarder
      - MEILI_URL=http://meilisearch:7700
      - MEILI_MASTER_KEY=your-meili-key
    depends_on:
      - postgres
      - meilisearch
    volumes:
      - hoarder_data:/app/data

  postgres:
    image: postgres:15-alpine
    container_name: karakeep_db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=hoarder
      - POSTGRES_PASSWORD=hoarder
      - POSTGRES_DB=hoarder
    volumes:
      - postgres_data:/var/lib/postgresql/data

  meilisearch:
    image: getmeili/meilisearch:latest
    container_name: karakeep_search
    restart: unless-stopped
    ports:
      - "7700:7700"
    environment:
      - MEILI_MASTER_KEY=your-meili-key
    volumes:
      - meilisearch_data:/meili_data

volumes:
  hoarder_data:
  postgres_data:
  meilisearch_data:
```

打开你的 NAS 设备或服务器的管理面板，找到 Docker 或容器管理功能。

**关键配置说明：**

1. NEXTAUTH\_SECRET：这是应用的加密密钥，请替换为一个安全的随机字符串

2. NEXTAUTH\_URL：这是你访问应用的地址，如果在本地运行就保持 http://localhost:3000

3. MEILI\_MASTER\_KEY：搜索引擎的主密钥，也请设置为一个安全的值

**端口映射说明：**

- 3000：Karakeep 应用的访问端口

- 7700：Meilisearch 搜索引擎的端口（可选，仅内部使用）

如果你的系统中 3000 端口已被占用，可以将第一个数字改为其他端口，比如 8080:3000，这样就能通过 http://你的IP:8080 访问应用。

**存储配置：**

应用会自动创建以下数据卷来持久化你的数据：

- hoarder\_data：应用数据存储

- postgres\_data：数据库存储

- meilisearch\_data：搜索索引存储

这些数据卷确保即使容器重启，你的所有书签和笔记都不会丢失。

启动容器后，等待 1-2 分钟让所有服务初始化完成。然后在浏览器中访问 http://你的服务器IP:3000，就能看到 Karakeep 的登录界面了。首次使用时需要创建账户，之后就可以开始收集和管理你的信息了。

### 核心功能速览

- 🔗 全能书签管理：不仅支持链接，还能保存笔记、图片和 PDF 文档

- ⬇️ 智能内容抓取：自动获取链接的标题、描述和预览图片

- 🔎 全文搜索：快速查找你保存过的任何内容

- ✨ AI 智能标签：集成 OpenAI 或本地 Ollama 模型，自动为内容分类和总结

- 📋 分类管理：将书签组织到不同的列表中

- 🖍️ 高亮标记：保存和标记重要内容片段

- 📱 跨平台支持：iOS、Android 应用随时随地访问

- 🔌 开放 API：支持第三方集成和自定义客户端

### 部署完成

现在你已经拥有了一个完整的个人知识库系统！尽情享受 Karakeep 带来的便利，让信息管理变得简单高效。

---

原文：<https://www.passnat.com/posts/karakeep-ai-bookmark-manager-nas-deployment>
