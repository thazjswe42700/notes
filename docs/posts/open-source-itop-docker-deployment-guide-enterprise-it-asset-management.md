---
title: "开源IT运维管理平台iTop Docker容器化部署指南，快速搭建企业级IT资产管理系统"
date: 2025-10-16
description: "iTop是开源IT运维管理平台，基于Docker容器化部署，提供IT资产、事件、变更和配置管理功能。支持快速部署开箱即用，适合小型团队和大型企业搭建IT运维体系。本文详解Docker和Docker Compose部署方案、配置说明及启动后操作流程。"
canonical: https://www.passnat.com/posts/open-source-itop-docker-deployment-guide-enterprise-it-asset-management
---

## 项目介绍

iTop 是一个功能强大的开源 IT 运维管理平台，基于 Docker 容器化部署。它提供了完整的 IT 资产管理、事件管理、变更管理和配置管理等功能，是企业 IT 部门不可或缺的运维工具。通过 Docker 容器化，iTop 可以快速部署，开箱即用，无需复杂的环境配置。无论你是小型团队还是大型企业，都可以轻松搭建属于自己的 IT 运维管理体系。

项目地址：[点我跳转](https://github.com/vbkunin/itop-docker)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了便捷的内网穿透解决方案。详细的操作指南可以 [点击这里](https://www.passnat.com/posts/p) 查看全平台上手攻略，让你随时随地都能管理你的 IT 运维系统。

### 最快速上手方案

打开你的 NAS 或服务器的 Docker 管理界面，执行以下命令快速启动 iTop：

```bash
docker run -d -p 8000:80 --name=my-itop vbkunin/itop
```

启动完成后，打开浏览器访问 `http://你的服务器IP:8000/` 即可进入 iTop 安装向导，按照提示完成初始化配置。

### Docker Compose 部署方案

如果你需要更加持久化和规范的部署方式，可以使用 Docker Compose。复制下列配置文件到你的服务器：

```yaml
name: 'itop-all-in-one'

services:
  itop:
    image: vbkunin/itop:latest
    ports:
      - '8000:80'
    volumes:
      - ./itop/conf:/var/www/html/conf
      - ./itop/extensions:/var/www/html/extensions
      - db-volume:/var/lib/mysql
    environment:
      MYSQL_PASS: change_me
    post_start:
      - command: chown -R www-data:www-data /var/www/html/conf
        user: root
    restart: unless-stopped

volumes:
  db-volume:
```

将上述内容保存为 `docker-compose.yml` 文件，然后在文件所在目录执行：

```
docker-compose up -d
```

### 配置说明

**端口映射：**

容器内部 iTop 运行在 80 端口，映射到宿主机的 8000 端口

如果 8000 端口已被占用，可以修改配置中的 `'8000:80'` 为其他端口，比如 `'8001:80'`，但容器内的 80 端口保持不变

**存储卷配置：**

./itop/conf - iTop 配置文件存储目录，需要创建此文件夹

./itop/extensions - iTop 扩展插件目录，可选

db-volume - MySQL 数据库数据持久化存储

建议在部署前先创建这些目录：

```
mkdir -p itop/conf itop/extensions
```

**环境变量设置：**

必须设置：

MYSQL\_PASS - MySQL 数据库管理员密码（请修改为安全的密码）

如果不设置 `MYSQL\_PASS`，系统会自动生成一个随机密码，可以通过以下命令查看：

```bash
docker logs my-itop | grep -A7 -B1 'Your MySQL user 'admin' has password:'
```

### 启动后的操作

部署完成后，访问 `http://你的服务器IP:8000/` 进入 iTop 安装向导。按照向导步骤完成以下操作：

1. 选择安装语言和数据库配置

2. 输入数据库连接信息（用户名通常为 admin，密码为你设置的 MYSQL\_PASS）

3. 完成初始化配置

4. 创建管理员账户

安装完成后，使用你创建的管理员账户登录系统即可开始使用 iTop。

### 其他实用命令

如果你需要安装 iTop 开发工具包：

```bash
docker exec my-itop /install-toolkit.sh
```

设置定时任务（Cron）：

```bash
docker exec my-itop /setup-itop-cron.sh Cron Pa$5w0rD
```

本篇教程结束。通过以上步骤，你已经成功部署了一个完整的 iTop IT 运维管理平台。如有任何问题，欢迎参考官方文档或社区支持。

---

原文：<https://www.passnat.com/posts/open-source-itop-docker-deployment-guide-enterprise-it-asset-management>
