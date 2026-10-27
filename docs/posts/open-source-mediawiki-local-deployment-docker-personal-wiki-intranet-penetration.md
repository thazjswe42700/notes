---
title: "开源MediaWiki本地部署教程，Docker快速搭建个人维基百科系统，内网穿透远程访问"
date: 2025-10-16
description: "MediaWiki是维基百科官方开源维基引擎，采用PHP开发，支持350+语言。功能丰富易扩展，适配小型个人维基到大型企业知识库。本文详解Docker Compose快速部署步骤、环境变量配置及访问方式，助力快速搭建个人知识库和团队文档平台。"
canonical: https://www.passnat.com/posts/open-source-mediawiki-local-deployment-docker-personal-wiki-intranet-penetration
---

## 项目介绍

MediaWiki 是一款免费开源的维基百科引擎，采用 PHP 编写而成。作为维基百科及其他维基媒体项目的核心平台，每月被数亿用户访问。这个强大的系统已被本地化为超过 350 种语言版本，以其卓越的稳定性和丰富的功能特性赢得了全球开发者和用户的信赖。

MediaWiki 具备以下特点：

功能丰富且易于扩展，支持数百个第三方插件和自定义功能

高度可扩展，适用于小型个人维基到大型企业知识库

安装简便，兼容大多数硬件和软件环境

多语言支持，覆盖全球用户

无论你是想搭建个人知识库、企业内部文档系统还是社区百科全书，MediaWiki 都能满足你的需求。

项目地址：[点我跳转](https://github.com/wikimedia/mediawiki)

## 部署安装

如需在外网也能访问你的 MediaWiki 系统，建议配置内网穿透工具。可以前往[帕斯内网穿透](https://passnat.com)注册账号，这样就能随时随地访问你的维基系统。具体操作步骤可以[参考这个教程](https://www.passnat.com/posts/p)。

### 使用 Docker Compose 快速部署

MediaWiki 官方提供了开箱即用的 Docker Compose 配置，让技术小白也能轻松搭建。

**第一步：复制配置文件**

将以下配置保存为 `docker-compose.yml` 文件：

```yaml
version: '3'
services:
  mediawiki:
    image: docker-registry.wikimedia.org/dev/bookworm-php83-fpm:1.0.0
    user: "${MW_DOCKER_UID}:${MW_DOCKER_GID}"
    volumes:
      - ./:/var/www/html/w:cached
    env_file:
      - '.env'
    environment:
      COMPOSER_CACHE_DIR: '/var/www/html/w/cache/composer'
      MW_SERVER: 'http://localhost:${MW_DOCKER_PORT:-8080}'
      MW_DOCKER_PORT: "${MW_DOCKER_PORT:-8080}"
      MW_SCRIPT_PATH: '/w'
      MW_DBPATH: '/var/www/html/w/cache/sqlite'
      MW_DBTYPE: 'sqlite'
      MW_LANG: 'en'
      MW_USER: '${MEDIAWIKI_USER:-Admin}'
      MW_PASS: '${MEDIAWIKI_PASSWORD:-dockerpass}'
      MW_SITENAME: 'MediaWiki'
      MW_LOG_DIR: /var/www/html/w/cache
      XDEBUG_ENABLE: '${XDEBUG_ENABLE:-true}'
      XHPROF_ENABLE: '${XHPROF_ENABLE:-true}'

  mediawiki-web:
    image: docker-registry.wikimedia.org/dev/bookworm-apache2:1.0.1
    user: "${MW_DOCKER_UID}:${MW_DOCKER_GID}"
    ports:
      - "${MW_DOCKER_PORT:-8080}:8080"
    volumes:
      - ./:/var/www/html/w:cached
    env_file:
      - '.env'
    environment:
      MW_LOG_DIR: /var/www/html/w/cache
      MW_DOCKER_PORT: "${MW_DOCKER_PORT:-8080}"

  mediawiki-jobrunner:
    image: docker-registry.wikimedia.org/dev/bookworm-php83-jobrunner:1.0.0
    user: "${MW_DOCKER_UID}:${MW_DOCKER_GID}"
    volumes:
      - ./:/var/www/html/w:cached
    env_file:
      - '.env'
    environment:
      MW_LOG_DIR: /var/www/html/w/cache
      MW_INSTALL_PATH: /var/www/html/w
```

**第二步：打开你的 NAS 设备或服务器**

在飞牛 NAS、群晖或其他 NAS 设备上，进入 Docker 管理界面。

**第三步：启动容器**

将上述配置文件上传到你的服务器，然后运行以下命令启动 MediaWiki：

```
docker-compose up -d
```

等待容器启动完成即可。

**端口配置说明**

默认情况下，MediaWiki 会在 `8080` 端口运行。如果你的系统中该端口已被占用，可以修改 `docker-compose.yml` 中的以下部分：

```
ports:
  - "你的端口号:8080"
```

例如改为 `'9090:8080'` 表示外部访问 9090 端口，内部仍然是 8080。

**环境变量配置**

创建一个 `.env` 文件来配置 MediaWiki 的参数：

**必须设置：**

`MEDIAWIKI\_USER`: 管理员用户名（默认：Admin）

`MEDIAWIKI\_PASSWORD`: 管理员密码（默认：dockerpass）- **强烈建议修改为强密码**

**可选设置：**

`MW\_DOCKER\_PORT`: 访问端口（默认：8080）

`MW\_DOCKER\_UID`: Docker 用户 ID（默认：1000）

`MW\_DOCKER\_GID`: Docker 用户组 ID（默认：1000）

`MW\_LANG`: 系统语言（默认：en，可改为 zh 使用中文）

`MW\_SITENAME`: 维基网站名称（默认：MediaWiki）

`XDEBUG\_ENABLE`: 是否启用调试模式（默认：true）

其他参数保持默认即可，直接启动就好了。

**第四步：访问你的 MediaWiki**

容器启动成功后，打开浏览器访问：

```
http://你的服务器IP:8080
```

就能看到 MediaWiki 的欢迎页面了。使用你在环境变量中设置的用户名和密码登录，即可开始创建你的维基内容。

**数据持久化**

MediaWiki 的所有数据（包括页面内容和配置）都会保存在项目目录下的 `cache` 文件夹中。建议定期备份这个目录以防数据丢失。

本篇教程结束。现在你已经拥有了一个功能完整的 MediaWiki 系统，可以用来构建个人知识库或团队文档平台了！

---

原文：<https://www.passnat.com/posts/open-source-mediawiki-local-deployment-docker-personal-wiki-intranet-penetration>


## 相关文章

- [自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问](personal-navigation-server-monitoring-docker-jump-intranet-penetration.md)
- [自建短链接服务YOURLS Docker部署教程，NAS一键搭建私有短链系统](self-hosted-yourls-docker-deployment-guide-nas-private-short-link-system.md)
- [自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南](self-hosted-rss-reader-selfoss-docker-deployment-guide.md)
- [PHP动态二维码生成系统部署指南，Docker一键启动内网穿透远程访问管理平台](php-dynamic-qr-code-generator-docker-deployment-guide.md)
- [Tellor轻量级任务看板应用自托管部署教程，Docker快速安装配置指南](tellor-lightweight-task-board-self-hosted-deployment-docker-installation-guide.md)
