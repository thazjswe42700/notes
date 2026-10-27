---
title: "个人财务管理系统BudgetBee，Docker一键部署，NAS极简配置，多账户记账工具"
date: 2025-10-16
description: "BudgetBee是一款功能强大的个人财务管理系统，采用Docker容器技术部署。支持记录收支、管理多账户、设置预算分类、导入Excel和JSON数据，适合个人和家庭使用。本文提供完整的Docker Compose部署指南，包括环境变量配置、用户创建和访问方式，助你轻松实现财务数据的精细化管理和随时随地访问。"
canonical: https://www.passnat.com/posts/budget-bee-docker-deployment-nas-multi-account-accounting
---

## 项目介绍

BudgetBee 是一款功能强大的个人财务管理系统，专为那些想要掌控自己财务状况的用户设计。它采用现代化的 Docker 容器技术部署，基于 PHP 后端和现代前端框架构建，提供了一个开箱即用的财务管理解决方案。

通过 BudgetBee，你可以轻松记录每一笔收支、创建多个账户、管理不同的货币、设置分类预算，甚至支持从 Excel 和 JSON 文件导入历史账单数据。系统还支持多用户管理，非常适合家庭或小团队使用。无论你是想要精细化管理个人开支，还是需要一个简洁高效的家庭记账工具，BudgetBee 都能满足你的需求。

项目地址：[点我跳转](https://github.com/budgetbee/budgetbee)

**友情提示：** 若要在外网访问你的 BudgetBee 应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册。具体操作步骤可参考 [全平台内网穿透上手指南](https://www.passnat.com/posts/p)，轻松实现随时随地访问你的财务数据。

## 部署安装

### 准备工作

打开你的 NAS 设备（飞牛 NAS、群晖或其他支持 Docker 的 NAS）或本地 Docker 环境，我们将通过 Docker Compose 一键部署 BudgetBee。

### 配置文件部署

复制以下 Docker Compose 配置文件：

```yaml
version: '3.8'
services:
  nginx:
    image: ghcr.io/budgetbee/budgetbee/proxy:latest
    command: nginx -g "daemon off;"
    ports:
      - "${APP_PORT}:80"
    depends_on:
      - webserver
      - web
    restart: unless-stopped
    networks:
      - skynet
  webserver:
    image: ghcr.io/budgetbee/budgetbee/api:latest
    working_dir: /var/www/html
    command: sh entrypoint.sh
    environment:
      DB_HOST: db
      DB_DATABASE: ${DB_DATABASE}
      DB_USERNAME: ${DB_USERNAME}
      DB_PASSWORD: ${DB_PASSWORD}
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    networks:
      - skynet
  web:
    image: ghcr.io/budgetbee/budgetbee/web:latest
    restart: unless-stopped
    networks:
      - skynet
  db:
    image: mysql:8.2.0
    command: --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_DATABASE: ${DB_DATABASE}
      MYSQL_USER: ${DB_USERNAME}
      MYSQL_PASSWORD: ${DB_PASSWORD}
    healthcheck:
      test: ["CMD", "/usr/bin/mysql", "--user=${DB_USERNAME}", "--password=${DB_PASSWORD}", "--execute", "SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = '${DB_DATABASE};'"]
      timeout: 20s
      retries: 100
    restart: unless-stopped
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - skynet
networks:
  skynet:
volumes:
  db_data:
```

### 环境变量配置

将上述配置文件保存后，需要配置以下环境变量。在 NAS 的 Portainer 或 Docker 管理面板中进行设置：

**必须设置的环境变量：**

APP\_PORT: 应用访问端口（默认值：80，如果与其他服务冲突可改为 8080、3000 等）
DB\_DATABASE: 数据库名称（默认值：budgetbee）
DB\_USERNAME: 数据库用户名（默认值：budgetbee）
DB\_PASSWORD: 数据库密码（请设置强密码）
DB\_ROOT\_PASSWORD: MySQL root 密码（请设置强密码）

**环境变量说明：**

APP\_PORT 是容器对外暴露的端口，如果你的 NAS 上 80 端口已被占用，可以改为其他端口（如 8080）。容器内部端口保持不变。
数据库相关的密码请务必设置为复杂密码，确保安全性。
其他配置项保持默认即可。

### 启动应用

在 NAS 的 Docker 管理面板中：

将上述配置文件粘贴到 docker-compose 编辑区域
填入上述环境变量的具体值
点击启动或部署

等待所有容器启动完成（通常需要 30-60 秒）。

### 创建用户账户

容器全部启动后，需要创建你的登录账户。在本地机器或 NAS 终端执行以下命令：

```bash
docker exec budgetbee-webserver-1 php scripts/create_user.php 你的名字 你的邮箱 你的密码
```

例如：

```bash
docker exec budgetbee-webserver-1 php scripts/create_user.php "张三" "zhangsan@example.com" "MySecurePassword123"
```

### 访问应用

部署完成后，打开浏览器访问：

```
http://你的NAS_IP:APP_PORT
```

例如，如果你的 NAS IP 是 192.168.1.100，APP\_PORT 设置为 8080，则访问地址为：

```
http://192.168.1.100:8080
```

使用刚才创建的账户邮箱和密码登录，即可开始使用 BudgetBee 管理你的财务！

## 总结

通过以上步骤，你已经成功部署了 BudgetBee 财务管理系统。现在你可以：

📊 记录每一笔收支交易
💰 管理多个账户和货币
📂 按分类设置预算和跟踪
📥 从 Excel/JSON 导入历史账单
👥 创建多个用户账户

如需升级到最新版本，只需在 NAS 终端执行 `docker-compose up -d --build` 即可。

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/budget-bee-docker-deployment-nas-multi-account-accounting>
