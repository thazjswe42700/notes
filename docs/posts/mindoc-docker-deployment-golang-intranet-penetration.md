---
title: "MinDoc轻量级文档管理系统Docker极简部署，Golang高性能跨平台支持，内网穿透远程安全访问"
date: 2025-10-16
description: "MinDoc是轻量级Golang开发的IT团队文档管理系统，支持Markdown、富文本编辑，内置项目管理、权限控制、文档评论功能。跨平台运行，提供Docker快速部署，可统一管理API文档、技术手册等，开源稳定可靠，助力团队高效协作和知识共享。"
canonical: https://www.passnat.com/posts/mindoc-docker-deployment-golang-intranet-penetration
---

## 项目介绍

MinDoc 是一款专为IT团队量身打造的轻量级文档管理系统，操作简便，功能完整。它采用 Golang 开发，具有出色的性能和跨平台兼容性，可以轻松在 Windows、Linux、Mac 等多种系统上运行。

MinDoc 支持多种编辑器（Markdown、富文本、Cherry Markdown），内置项目管理、用户权限控制、文档评论等功能。无论是API接口文档、数据库字典还是技术手册，MinDoc 都能帮你统一管理和分享。项目采用开源模式，由社区维护，稳定可靠。

项目地址： [点我跳转](https://github.com/mindoc-org/mindoc)

## 部署安装

若要在公网环境中访问你的MinDoc实例，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，注册账号后即可快速搭建安全的远程访问通道。详细的配置步骤可参考 [帕斯官方教程](https://www.passnat.com/posts/p)，支持全平台快速上手。

### 使用Docker快速部署

MinDoc 提供了开箱即用的 Docker 镜像，让你无需复杂配置即可快速启动服务。

**第一步：拉取镜像并启动容器**

在你的 NAS 设备或服务器上打开终端，执行以下命令：

```bash
docker run -it --name=mindoc --restart=always \
  -v /var/www/mindoc/conf:/mindoc/conf \
  -v /var/www/mindoc/uploads:/mindoc/uploads \
  -v /var/www/mindoc/database:/mindoc/database \
  -p 8181:8181 \
  -e MINDOC_ENABLE_EXPORT=true \
  registry.cn-hangzhou.aliyuncs.com/mindoc-org/mindoc:v2.1
```

**第二步：访问应用**

打开浏览器，访问 `http://你的IP:8181`，即可看到 MinDoc 的登录界面。

**第三步：首次登录**

使用默认账号登录：

用户名：`admin`

密码：`123456`

登录后，请立即修改默认密码以确保安全。

**端口说明**

上述命令中的 `8181` 是 MinDoc 的默认访问端口。如果你的系统中该端口已被占用，可以修改前面的端口号（例如改为 `8182:8181`），但容器内的端口保持不变。

**数据持久化**

命令中的四个 `-v` 参数用于将容器内的关键目录映射到宿主机，确保你的数据和配置不会因容器重启而丢失：

/var/www/mindoc/conf - 配置文件目录

/var/www/mindoc/uploads - 上传文件目录

/var/www/mindoc/database - 数据库文件目录

请确保这些目录在宿主机上已存在，如果不存在可以提前创建。

### 使用Docker Compose部署（推荐）

如果你更倾向于使用 Docker Compose 来管理容器，可以按以下步骤操作：

**第一步：创建配置文件**

在你的服务器上创建一个 `docker-compose.yml` 文件，内容如下：

```yaml
version: "3"
services:
  mindoc:
    image: registry.cn-hangzhou.aliyuncs.com/mindoc-org/mindoc:v2.1
    container_name: mindoc
    restart: always
    ports:
      - 8181:8181
    volumes:
      - /var/www/mindoc/conf:/mindoc/conf
      - /var/www/mindoc/uploads:/mindoc/uploads
      - /var/www/mindoc/database:/mindoc/database
      - /var/www/mindoc/runtime:/mindoc/runtime
    environment:
      - MINDOC_RUN_MODE=prod
      - MINDOC_DB_ADAPTER=sqlite3
      - MINDOC_DB_DATABASE=./database/mindoc.db
      - MINDOC_CACHE=true
      - MINDOC_CACHE_PROVIDER=file
      - MINDOC_ENABLE_EXPORT=false
    dns:
      - 223.5.5.5
      - 223.6.6.6
```

**第二步：启动服务**

在 `docker-compose.yml` 所在目录执行：

```
docker-compose up -d
```

**第三步：验证部署**

稍等片刻，然后访问 `http://你的IP:8181`，即可进入 MinDoc 界面。

**常用操作命令**

启动服务：`docker-compose up -d`

停止服务：`docker-compose stop`

重启服务：`docker-compose restart`

停止并删除容器：`docker-compose down`

重新创建容器：`docker-compose down && docker-compose up -d`

### 环境变量配置说明

**必须设置的环境变量**

无 - MinDoc 使用默认的 SQLite 数据库，开箱即用。

**可选配置项**

环境变量 | 说明 | 默认值

---------|------|--------

MINDOC\_ENABLE\_EXPORT | 是否启用文档导出功能 | false

MINDOC\_RUN\_MODE | 运行模式（prod/dev） | prod

MINDOC\_DB\_ADAPTER | 数据库类型（sqlite3/mysql） | sqlite3

MINDOC\_CACHE | 是否启用缓存 | true

MINDOC\_CACHE\_PROVIDER | 缓存提供者（file/redis） | file

如果需要使用 MySQL 数据库，可添加以下环境变量：

```
environment:
  - MINDOC_DB_ADAPTER=mysql
  - MYSQL_PORT_3306_TCP_ADDR=你的MySQL地址
  - MYSQL_PORT_3306_TCP_PORT=3306
  - MYSQL_INSTANCE_NAME=mindoc
  - MYSQL_USERNAME=root
  - MYSQL_PASSWORD=你的密码
```

## 部署完成

到此，MinDoc 文档管理系统已成功部署完毕。你现在拥有了一个功能完整、易于使用的团队文档管理平台。

在 MinDoc 中，你可以：

创建多个项目来组织不同类型的文档

邀请团队成员协作编写和管理文档

使用多种编辑器灵活编写内容

设置项目权限，保护敏感信息

导出文档为多种格式

通过 Token 分享私有项目

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/mindoc-docker-deployment-golang-intranet-penetration>
