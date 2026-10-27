---
title: "自然语言SQL对话工具，AI驱动数据库客户端，支持多数据库NAS快速部署"
date: 2025-10-16
description: "SQL Chat是创新的聊天式数据库客户端，用自然语言替代SQL语句。支持MySQL、PostgreSQL、MSSQL等多种数据库，集成OpenAI语言模型，让数据查询、修改、新增、删除操作变得简单直观。基于Next.js构建的现代化应用，适合所有技术水平用户轻松驾驭数据库。"
canonical: https://www.passnat.com/posts/natural-language-sql-chat-ai-database-client-multi-database-nas-deployment
---

## SQL Chat - 用自然语言与数据库对话

### 项目介绍

SQL Chat 是一款创新的聊天式数据库客户端，它彻底改变了你与数据库交互的方式。告别繁琐的 SQL 语句编写，只需用自然语言描述你的需求，AI 就能帮你自动生成对应的数据库操作。无论是数据查询、修改、新增还是删除，一切都变得简单直观。

这是开发工具 2.0 时代的典型代表——基于 Next.js 构建的现代化应用，支持 MySQL、PostgreSQL、MSSQL、TiDB Cloud 和 OceanBase 等多种主流数据库。它集成了 OpenAI 的强大语言模型能力，让数据库操作不再是技术人员的专属，任何人都能轻松驾驭。

项目地址：[点我跳转](https://github.com/sqlchat/sqlchat)

### 部署安装

如果你需要在公网访问这个应用，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，注册账号后即可使用。详细的配置步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)，让你随时随地都能访问自己的 SQL Chat。

#### Docker 快速启动

打开你的 NAS 设备或任何支持 Docker 的系统。

在本地镜像管理中，选择「添加镜像」→「从 URL 添加」，输入以下镜像地址：

```
sqlchat/sqlchat
```

等待镜像下载完成后，点击启动按钮创建容器。

#### 容器配置说明

端口映射

将容器的 3000 端口映射到宿主机的 3000 端口（或任意你想使用的端口）。如果 3000 端口已被占用，可以修改宿主机端口号，但容器内部端口必须保持为 3000。

环境变量设置

必须设置：

NEXTAUTH\_SECRET：身份验证密钥，可以填入任意随机字符串（系统会自动生成）

OPENAI\_API\_KEY：你的 OpenAI API 密钥，[从这里获取](https://platform.openai.com/api-keys)

可选设置：

OPENAI\_API\_ENDPOINT：OpenAI API 端点，默认为 'https://api.openai.com'。如果你使用 Ollama 等本地 AI 模型，可以指向本地端点

NEXT\_PUBLIC\_ALLOW\_SELF\_OPENAI\_KEY：设为 'true' 允许用户输入自己的 OpenAI API 密钥

NEXT\_PUBLIC\_USE\_DATABASE：设为 'true' 启用数据库功能（账户系统、配额管理、支付功能等）

DATABASE\_URL：PostgreSQL 连接字符串（仅在启用数据库时需要），格式如 'postgresql://postgres:YOUR\_PASSWORD@localhost:5432/sqlchat?schema=sqlchat'

重要提示

如果你在同一主机上连接数据库，需要在数据库连接设置中使用 'host.docker.internal' 作为主机地址。

勾选「开机自动启动」确保容器在系统重启后自动运行，然后点击启动。

#### 访问应用

创建成功后，打开浏览器访问 'http://你的IP:3000'，就能看到 SQL Chat 的界面了。输入你的数据库连接信息，就可以开始用自然语言与你的数据库对话！

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/natural-language-sql-chat-ai-database-client-multi-database-nas-deployment>


## 相关文章

- [Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透](mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration.md)
- [单文件PHP数据库管理工具Adminer，支持MySQL、PostgreSQL等多种数据库，NAS极简部署内网穿透远程管理](adminer-php-database-management-tool-mysql-postgresql-nas-deployment-internal-penetration-remote-management.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
- [WhoDB轻量级数据库管理工具，自然语言对话查询，NAS极简部署，无公网内网穿透远程访问。](whodb-lightweight-database-management-natural-language-query-nas-simple-deployment-private-network-remote-access.md)
- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
