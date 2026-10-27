---
title: "企业IT运维管理系统CAT一键部署方案，Docker容器快速启动NAS设备应用"
date: 2025-10-16
description: "CAT是专为IT运维打造的一站式管理平台，集资产管理、工单系统、工作流引擎、仓储管理于一身。基于PHP Laravel框架开发，支持Docker Compose快速部署，开箱即用。完全开源遵循GPL3.0协议，帮助运维团队提升工作效率，适配各规模企业需求。"
canonical: https://www.passnat.com/posts/enterprise-it-operations-management-system-cat-one-click-deployment-docker-container-nas
---

## 项目介绍

CAT 是一个专为 IT 运维从业者精心打造的一站式解决方案平台。它集资产管理、工单系统、工作流引擎、仓储管理等强大功能于一身，旨在帮助运维团队从繁重无序的日常工作中解脱出来，显著提升管理效率。

这个项目采用现代化的技术栈构建，基于 PHP Laravel 框架开发，提供了开箱即用的部署方案。无论你是小型企业还是中大型组织，CAT 都能为你的 IT 运维工作带来质的飞跃。项目完全开源，遵循 GPL3.0 协议，源代码 100% 公开透明，欢迎社区贡献和反馈。

项目地址： [点我跳转](https://github.com/celaraze/cat)

## 部署安装

如果你需要在办公室外部也能访问 CAT 系统，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样你就能随时随地远程管理你的运维工作。具体的配置步骤可以参考 [这份详细攻略](https://www.passnat.com/posts/p)，涵盖了全平台的上手指南。

### 使用 Docker Compose 快速启动（推荐）

这是最简单快速的部署方式，特别适合想要一键启动的用户。

**第一步：准备配置文件**

复制下列的 docker-compose 配置内容到你的服务器或 NAS 设备上，保存为 `docker-compose.yml` 文件：

```yaml
version: '3.8'

services:
  cat:
    image: celaraze/cat:latest
    container_name: cat
    restart: unless-stopped
    ports:
      - "8000:8000"
    volumes:
      - cat_database:/var/www/database/data
      - cat_storage:/var/www/storage/app/public

volumes:
  cat_database:
  cat_storage:
```

**第二步：启动容器**

打开你的 NAS 设备或服务器的 Docker 管理界面（比如飞牛 NAS、群晖等），找到 Docker 应用。

如果你使用的是命令行，直接在配置文件所在目录执行：

```
docker-compose up -d
```

等待镜像下载完成并自动启动容器。系统会自动创建必要的数据库和存储目录，无需手动干预。

**第三步：访问应用**

部署完成后，在浏览器中访问：

```
http://你的服务器IP:8000
```

替换 `你的服务器IP` 为你实际的服务器或 NAS 的 IP 地址。首次访问时，系统会自动进行初始化配置，按照提示完成即可。

### 环境变量说明

CAT 支持多种数据库配置，默认使用 SQLite（无需额外配置），如需更高级的配置，可以参考以下环境变量：

**必须配置的环境变量：**

APP\_NAME: 应用名称（默认值：CAT）

APP\_DEBUG: 调试模式（默认值：false，生产环境保持 false）

**可选配置的环境变量：**

DB\_CONNECTION: 数据库类型，支持 sqlite 或 mysql（默认值：sqlite）

DB\_HOST: MySQL 数据库主机地址（仅在使用 MySQL 时需要）

DB\_PORT: MySQL 数据库端口（默认值：3306）

DB\_DATABASE: 数据库名称

DB\_USERNAME: 数据库用户名

DB\_PASSWORD: 数据库密码

DEMO\_MODE: 演示模式（默认值：false）

**关于端口配置：**

默认配置中，CAT 运行在容器内的 8000 端口。如果你的服务器上 8000 端口已被占用，可以修改 docker-compose.yml 中的端口映射部分。例如，如果想改为 8080 端口访问：

```
ports:
  - "8080:8000"
```

修改前面的数字即可（冒号后面的 8000 是容器内部端口，不要改动）。

**关于数据持久化：**

配置文件中已经设置了两个数据卷用于持久化存储：

cat\_database: 存储数据库文件

cat\_storage: 存储上传的文件和资源

这样即使容器重启，你的所有数据也不会丢失。

### 一键启动完成

配置好 docker-compose.yml 后，执行启动命令，CAT 就会自动完成所有初始化工作。无需复杂的环境变量配置，开箱即用的体验让你快速投入使用。

本篇教程到此结束。祝你使用愉快！如有问题，欢迎前往 [官方 GitHub Issues](https://github.com/celaraze/cat/issues) 反馈。

---

原文：<https://www.passnat.com/posts/enterprise-it-operations-management-system-cat-one-click-deployment-docker-container-nas>


## 相关文章

- [开源个人关系管理系统Monica私密部署指南，NAS一键Docker快速安装配置教程](open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install.md)
- [自建短链接服务YOURLS Docker部署教程，NAS一键搭建私有短链系统](self-hosted-yourls-docker-deployment-guide-nas-private-short-link-system.md)
- [开源MediaWiki本地部署教程，Docker快速搭建个人维基百科系统，内网穿透远程访问](open-source-mediawiki-local-deployment-docker-personal-wiki-intranet-penetration.md)
- [自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问](personal-navigation-server-monitoring-docker-jump-intranet-penetration.md)
- [独角数卡开源数字商品售货系统Docker部署指南，支持多种支付方式NAS快速安装](unicorn-shuaka-open-source-digital-goods-vending-system-docker-deployment-guide-supports-multiple-payment-methods-nas-quick-installation.md)
