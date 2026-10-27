---
title: "单文件PHP数据库管理工具Adminer，支持MySQL、PostgreSQL等多种数据库，NAS极简部署内网穿透远程管理"
date: 2025-10-16
description: "Adminer是功能完整的PHP数据库管理工具，支持MySQL、PostgreSQL、SQLite等多种数据库。本文详细介绍Adminer的功能特性、Docker快速部署方法、端口配置、存储设置等内容，帮助用户轻松在NAS设备上部署和使用Adminer进行数据库管理和操作。"
canonical: https://www.passnat.com/posts/adminer-php-database-management-tool-mysql-postgresql-nas-deployment-internal-penetration-remote-management
---

## 项目介绍

Adminer 是一款功能完整的数据库管理工具，采用 PHP 编写，以单个文件的形式部署到服务器上，开箱即用。无论你是数据库管理员还是开发者，都可以通过 Adminer 轻松管理和操作数据库。

Adminer 支持多种主流数据库系统，包括 MySQL、MariaDB、PostgreSQL、CockroachDB、SQLite、MS SQL 和 Oracle，让你可以在一个统一的界面中管理所有数据库。同时，Adminer Editor 为普通用户提供了数据操作功能，使得非技术人员也能轻松进行数据维护。

项目具有强大的插件系统，支持 Elasticsearch、SimpleDB、MongoDB、Firebird、ClickHouse、IMAP 等多种扩展，满足各种复杂的数据库管理需求。

项目地址：[点我跳转](https://github.com/vrana/adminer)

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

### 使用 Docker 快速部署

打开飞牛 NAS 或其他的 NAS 设备，点击本地镜像 - 添加镜像 - 从 URL 添加。

镜像填 'adminer:latest'，等待下载完成后点击启动按钮启动容器。

**端口设置**

勾选开机自动启动后下一步，进入端口设置界面。Adminer 默认运行在容器内的 8080 端口，你可以将其映射到宿主机的任意端口（如 8080、8888 等）。如果提示端口被占用，只需修改宿主机的映射端口即可，容器内的端口保持 8080 不变。

**存储位置**

Adminer 本身不需要特殊的持久化存储，所有数据都存储在你连接的数据库中。如果你需要保存配置文件或插件，可以创建一个文件夹映射到容器内的 '/var/www/html' 目录。

**环境变量设置**

Adminer 无需特殊的环境变量配置，保持默认即可。如果你需要自定义配置，可以参考官方文档进行高级设置。

**启动容器**

完成以上配置后，点击下一步启动容器。创建成功后可以访问 'http://你的NAS地址:8080' 来打开 Adminer 界面。

在登录界面中，选择你要连接的数据库类型（MySQL、PostgreSQL 等），输入数据库服务器地址、用户名和密码，即可连接并管理你的数据库。

到这里就可以正常使用 Adminer 进行数据库管理了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/adminer-php-database-management-tool-mysql-postgresql-nas-deployment-internal-penetration-remote-management>
