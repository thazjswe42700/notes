---
title: "浏览器免费数据库设计工具DrawDB，无需账户在线绘制ER图生成SQL脚本"
date: 2025-10-16
description: "DrawDB是免费简洁的数据库设计工具，支持浏览器ER图绘制、SQL脚本生成和多格式导出。无需注册即用，界面直观易操作，适合初学者和专业开发者快速构建数据库关系图和可视化设计。"
canonical: https://www.passnat.com/posts/browser-free-database-design-tool-drawdb-er-diagram-sql-script
---

## 项目介绍

DrawDB 是一款免费、简洁且直观的数据库架构设计工具，让你在浏览器中轻松完成数据库ER图的绘制和SQL脚本的生成。无需复杂的配置，无需创建账户，点开即用。

这是一个功能强大的数据库实体关系编辑器，支持通过简单的拖拽操作快速构建数据库关系图，自动生成对应的SQL脚本，支持多种数据库格式导出。界面简洁易用，特别适合数据库设计初学者和专业开发人员使用。无论你是在规划新项目的数据结构，还是需要可视化现有的数据库设计，DrawDB 都能为你提供便捷的解决方案。

项目地址： [点我跳转](https://github.com/drawdb-io/drawdb)

## 部署安装

如需在公网环境下访问你的设计工具，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的上手步骤可以 [参考这里](https://www.passnat.com/posts/p) 了解全平台的内网穿透配置方法。

### 使用 Docker 快速部署

打开你的 NAS 设备或 Docker 管理面板，选择本地镜像 - 添加镜像 - 从 URL 添加。

在镜像地址栏填入：**`drawdb`** （系统会自动拉取最新版本）

等待镜像下载完成后，点击启动按钮启动容器。

**基础配置步骤：**

1. 开机自动启动：勾选此项，确保容器随系统启动而自动运行

2. 端口映射设置：

- 容器内部端口：**`80`**

- 宿主机端口：**`3000`**（如果3000端口已被占用，可修改为其他未使用的端口，例如3001、3002等，但容器内的80端口保持不变）

3. 存储配置：

- DrawDB 主要用于设计和导出，本身不需要特殊的数据持久化存储

- 如需保存设计文件，建议在宿主机创建一个文件夹（如 **`/data/drawdb`**），映射到容器内的 **`/app/data`** 目录

4. 环境变量设置：

- 基础部署无需设置环境变量，直接下一步即可

- 若要启用文件分享功能，需要另外部署配套的服务器并配置相应的环境变量（可选功能）

配置完成后，点击启动，等待容器运行成功。

**访问应用：**

部署完成后，在浏览器中访问 **`http://你的设备IP:3000`** 即可打开 DrawDB 应用。你现在可以开始设计数据库架构、绘制ER图，并一键导出SQL脚本了。

到这里，DrawDB 的部署就完全搞定了，你可以立即开始使用这个强大的数据库设计工具！

---

原文：<https://www.passnat.com/posts/browser-free-database-design-tool-drawdb-er-diagram-sql-script>


## 相关文章

- [翻数据库不用再开客户端：飞牛 NAS 用一个容器装 DbGate，浏览器里开表写 SQL，配内网穿透在外面也能连](dbgate-database-management-tool-mysql-postgresql-mongodb-nas-docker-deployment.md)
- [Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透](mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration.md)
- [自然语言SQL对话工具，AI驱动数据库客户端，支持多数据库NAS快速部署](natural-language-sql-chat-ai-database-client-multi-database-nas-deployment.md)
- [SQLite Web数据库管理工具极简部署指南 NAS内网穿透在线查询编辑](sqlite-web-database-management-tool-minimal-deployment-guide-nas-intranet-penetration-online-query-editing.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
