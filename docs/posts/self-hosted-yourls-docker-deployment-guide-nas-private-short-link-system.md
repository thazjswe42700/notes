---
title: "自建短链接服务YOURLS Docker部署教程，NAS一键搭建私有短链系统"
date: 2025-10-16
description: "YOURLS是开源免费的自建短链接系统，采用PHP开发，让您在自己的服务器上搭建专属短链接服务。支持自定义格式、访问统计、数据分析和插件扩展，适合个人博客、企业营销和团队协作。本文详细介绍Docker快速部署方法、容器配置步骤和环境变量设置，帮助您轻松实现远程访问和数据持久化管理。"
canonical: https://www.passnat.com/posts/self-hosted-yourls-docker-deployment-guide-nas-private-short-link-system
---

## 项目介绍

YOURLS（Your Own URL Shortener）是一款功能完整的自建短链接系统，采用PHP语言开发。这是一个开源免费的项目，让你能够在自己的服务器上搭建专属的短链接服务，完全掌控所有数据。

相比于依赖第三方短链接服务，YOURLS给予你完整的自主权——你可以自定义短链接格式、获取详细的访问统计数据、使用强大的分析功能、安装丰富的插件扩展等等。无论是个人博客、企业营销还是团队协作，YOURLS都能提供一个稳定可靠的短链接解决方案。

项目地址：[点我跳转](https://github.com/YOURLS/YOURLS)

## 部署安装

如需在公网环境下访问你的短链接服务，建议配置内网穿透功能。推荐使用[帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以参考[内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

### 使用 Docker 快速部署

打开你的 NAS 设备或云服务器的Docker管理面板。

点击**本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
yourls:latest
```

等待镜像下载完成后，点击**启动**按钮创建容器。

### 容器配置

**勾选开机自动启动**，然后进行端口映射设置。

**端口映射配置：**

容器内部端口：`80`（这是YOURLS应用运行的端口，请勿修改）

宿主机端口：`8080`（如果该端口已被占用，可改为其他未使用的端口，如 `8081`、`8082` 等）

**存储配置：**

YOURLS 需要数据库来存储短链接和访问记录。建议在NAS上创建一个专用文件夹用于数据持久化，例如 `/volume1/yourls`，然后将其映射到容器内的 `/var/www/html` 目录。这样即使容器重启，你的所有短链接数据也不会丢失。

**环境变量设置（可选）：**

以下是YOURLS支持的主要环境变量配置：

环境变量 | 说明 | 默认值
'YOURLS\_DB\_HOST' | 数据库主机地址 | localhost
'YOURLS\_DB\_USER' | 数据库用户名 | yourls
'YOURLS\_DB\_PASS' | 数据库密码 | yourls
'YOURLS\_DB\_NAME' | 数据库名称 | yourls
'YOURLS\_SITE' | 你的短链接服务地址 | http://localhost

其他变量无需特殊配置，保持默认即可。

配置完成后，点击**启动**，容器将自动运行。

### 访问应用

创建成功后，在浏览器中访问：

```
http://你的NAS地址:8080
```

（如果你修改了宿主机端口，请相应地替换上面的 `8080`）

稍等片刻，YOURLS 的管理后台将完整加载。你现在可以开始创建短链接、查看统计数据，享受完全属于自己的短链接服务了。

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/self-hosted-yourls-docker-deployment-guide-nas-private-short-link-system>


## 相关文章

- [开源MediaWiki本地部署教程，Docker快速搭建个人维基百科系统，内网穿透远程访问](open-source-mediawiki-local-deployment-docker-personal-wiki-intranet-penetration.md)
- [自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问](personal-navigation-server-monitoring-docker-jump-intranet-penetration.md)
- [自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南](self-hosted-rss-reader-selfoss-docker-deployment-guide.md)
- [PHP动态二维码生成系统部署指南，Docker一键启动内网穿透远程访问管理平台](php-dynamic-qr-code-generator-docker-deployment-guide.md)
- [AgenDAV 网页日历客户端 NAS 自建部署指南，内网穿透远程访问日程管理系统](agendav-nas-deployment-guide-calendar-scheduling.md)
