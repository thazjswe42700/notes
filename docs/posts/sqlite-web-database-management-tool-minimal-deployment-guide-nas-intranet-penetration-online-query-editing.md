---
title: "SQLite Web数据库管理工具极简部署指南 NAS内网穿透在线查询编辑"
date: 2025-10-16
description: "SQLite Web是一款基于Python开发的网页版数据库管理工具，提供简洁直观的Web界面。支持表格创建删除、数据增删改查、SQL查询执行、JSON/CSV数据导入导出等功能。采用Flask框架构建，特别适合需要快速查看编辑SQLite数据库的用户。支持Docker一键部署、密码保护和只读模式。"
canonical: https://www.passnat.com/posts/sqlite-web-database-management-tool-minimal-deployment-guide-nas-intranet-penetration-online-query-editing
---

## SQLite Web 数据库管理工具部署指南

### 项目介绍

SQLite Web 是一款基于 Python 开发的网页版 SQLite 数据库管理工具。它提供了一个简洁直观的 Web 界面，让您无需命令行即可轻松管理 SQLite 数据库。无论是查看数据库结构、浏览表格数据、执行 SQL 查询，还是导入导出数据，SQLite Web 都能胜任。

该工具采用 Flask 框架构建，界面友好易用，特别适合那些需要快速查看和编辑 SQLite 数据库的用户。支持表格创建/删除、列的增删改、数据的增删改查、JSON/CSV 数据导入导出等丰富功能。

项目地址：[点我跳转](https://github.com/coleifer/sqlite-web)

### 部署安装

如果您需要在远程设备上访问该应用，建议配置内网穿透服务。您可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地从外网安全地访问您的数据库管理界面。具体的配置步骤可以参考 [这份完整教程](https://www.passnat.com/posts/p)。

#### Docker 单命令部署

打开您的 NAS 设备或服务器，使用以下 Docker 命令快速启动 SQLite Web：

```bash
docker run -it --rm \
    -p 8080:8080 \
    -v /path/to/your-data:/data \
    -e SQLITE_DATABASE=db_filename.db \
    ghcr.io/coleifer/sqlite-web:latest
```

参数说明：

-p 8080:8080：将容器内的 8080 端口映射到主机的 8080 端口。如果您的主机上 8080 端口已被占用，可以修改前面的端口号，比如 -p 9090:8080，这样就能通过 9090 端口访问应用

-v /path/to/your-data:/data：将您存放数据库文件的本地目录挂载到容器内的 /data 目录。请将 /path/to/your-data 替换为您实际的数据库文件所在路径

-e SQLITE\_DATABASE=db\_filename.db：指定要打开的数据库文件名。请将 db\_filename.db 替换为您的实际数据库文件名

环境变量配置：

环境变量 | 说明 | 是否必须
SQLITE\_DATABASE | 要打开的 SQLite 数据库文件名 | 必须

启动完成后，打开浏览器访问 http://localhost:8080/（如果修改了端口号，请使用对应的端口），即可进入 SQLite Web 的管理界面。

### 功能特性

📊 数据库结构管理：轻松创建、删除表格和索引

📝 数据编辑：支持插入、更新、删除行数据

🔍 SQL 查询：在 Web 界面执行任意 SQL 查询语句

📥📤 数据导入导出：支持 JSON 和 CSV 格式的双向转换

🔐 只读模式：可配置为只读模式保护数据安全

🔐 密码保护：支持设置访问密码

### 部署完成

至此，SQLite Web 已成功部署完毕。您现在可以通过浏览器直观地管理您的 SQLite 数据库，无需掌握复杂的命令行操作。祝您使用愉快！

---

原文：<https://www.passnat.com/posts/sqlite-web-database-management-tool-minimal-deployment-guide-nas-intranet-penetration-online-query-editing>
