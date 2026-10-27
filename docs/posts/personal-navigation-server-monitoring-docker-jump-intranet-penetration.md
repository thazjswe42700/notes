---
title: "自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问"
date: 2025-10-16
description: "Jump是一个自托管的个人导航页面和服务器状态监控工具，基于PHP 8.1+构建。支持快速部署、美观界面、灵活分类、实时监控、天气集成、快速搜索、背景定制、Docker集成和多语言支持。开箱即用，响应式设计，支持内网和云端应用访问。"
canonical: https://www.passnat.com/posts/personal-navigation-server-monitoring-docker-jump-intranet-penetration
---

## 项目介绍

Jump 是一个自托管的个人导航页面和服务器实时状态监控工具。它以简洁、优雅、高效和安全为设计理念，基于 PHP 8.1+ 构建，可以快速部署在任何服务器上。

Jump 让你能够轻松创建一个美观的个人起始页，集中管理你所有常用的网站和服务。无论是内网服务还是云端应用，都可以在一个界面中访问。项目支持自定义图标、多标签分类、实时状态监控、天气显示、随机背景图片等丰富功能，让你的浏览体验更加高效和舒适。

项目地址：[点我跳转](https://github.com/daledavies/jump)

## 核心功能特性

⚡ 快速部署 - 开箱即用，支持 Docker 一键启动
🎨 美观界面 - 响应式设计，支持多种布局选项
🏷️ 灵活分类 - 使用标签对网站进行分类管理
📊 实时监控 - 自动检测每个网站的在线状态
🌤️ 天气集成 - 集成 OpenWeatherMap 显示本地时间和天气
🔍 快速搜索 - 支持按名称、标签、URL 搜索，支持快捷键
🖼️ 背景定制 - 支持自定义背景或从 Unsplash 获取随机背景
🐳 Docker 集成 - 自动发现并展示 Docker 容器中的服务
🌍 多语言支持 - 支持中文、英文、德文等多种语言

## 部署安装

如果你需要在外网也能访问 Jump，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，轻松实现远程访问。

### Docker 单命令启动（推荐）

打开你的 NAS 设备或服务器，使用以下命令快速启动 Jump：

```bash
docker run -d -p 8123:8080 \
  --volume /path/to/backgrounds:/backgrounds \
  --volume /path/to/favicon:/favicon \
  --volume /path/to/sites:/sites \
  --volume /path/to/search:/search \
  --env SITENAME='我的导航页' \
  --env OWMAPIKEY='你的OpenWeatherMap密钥' \
  --env LATLONG='51.509865,-0.118092' \
  --name jump docker.io/daledavies/jump
```

参数说明：

-p 8123:8080 - 将容器的 8080 端口映射到主机的 8123 端口（如果 8123 被占用，可改为其他端口）
--volume - 挂载本地目录用于保存配置和数据
SITENAME - 你的导航页名称
OWMAPIKEY - OpenWeatherMap API 密钥（可选）
LATLONG - 地理位置坐标（可选）

### Docker Compose 方式

如果你更习惯使用 Docker Compose，可以创建以下配置文件：

复制下列配置内容，保存为 docker-compose.yml 文件：

```yaml
version: '3'
services:
    jump:
        image: daledavies/jump
        ports:
            - 8123:8080
        volumes:
            - ./backgrounds:/backgrounds
            - ./favicon:/favicon
            - ./search:/search
            - ./sites:/sites
        environment:
            SITENAME: '我的导航页'
            OWMAPIKEY: ''
            LATLONG: ''
        restart: unless-stopped
```

打开飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

在本地创建一个文件夹用于存放 Jump 的配置文件，然后将上述 docker-compose.yml 文件放入该文件夹。

通过 SSH 或 NAS 的命令行工具，进入该文件夹所在目录，执行以下命令启动容器：

```
docker-compose up -d
```

### 环境变量配置

必须设置的环境变量：无（所有配置都是可选的）

推荐设置的环境变量：

变量名 | 说明 | 默认值
SITENAME | 导航页的名称 | Jump
OWMAPIKEY | OpenWeatherMap API 密钥 | 无
LATLONG | 地理位置坐标，格式：纬度,经度 | 无
SHOWCLOCK | 是否显示时钟 | true
SHOWSEARCH | 是否显示搜索功能 | true
CHECKSTATUS | 是否检查网站在线状态 | true

其他可选环境变量：

变量名 | 说明 | 默认值
AMPMCLOCK | 是否使用 12 小时制 | false
SHOWGREETING | 是否显示问候语 | true
ALTLAYOUT | 使用备用布局 | false
BGBLUR | 背景模糊程度（0-100） | 0
BGBRIGHT | 背景亮度（0-100） | 100
UNSPLASHAPIKEY | Unsplash API 密钥 | 无
METRICTEMP | 温度单位（true=摄氏度，false=华氏度） | true
LANGUAGE | 界面语言代码 | en
DEBUG | 调试模式 | false

### 数据持久化

Jump 需要以下目录来存储配置和数据，建议创建对应的本地文件夹进行映射：

/backgrounds - 存放自定义背景图片
/favicon - 存放网站图标
/sites - 存放网站配置文件（sites.json）
/search - 存放搜索引擎配置文件（searchengines.json）

在 NAS 中创建这些文件夹，然后在 Docker 配置中将它们映射到容器内对应的目录。

### 访问应用

部署完成后，在浏览器中访问：

```
http://你的NAS地址:8123
```

即可看到 Jump 的界面。如果端口被占用，请修改映射端口号（冒号前面的数字），但容器内的端口（8080）保持不变。

### 配置网站列表

首次访问后，编辑本地映射的 /sites/sites.json 文件，添加你想要显示的网站。例如：

```
{
    'sites': [
        {
            'name': 'GitHub',
            'url': 'https://github.com',
            'tags': ['开发']
        },
        {
            'name': 'Docker Hub',
            'url': 'https://hub.docker.com',
            'tags': ['开发']
        },
        {
            'name': 'Nextcloud',
            'url': 'https://cloud.example.com',
            'tags': ['云存储']
        }
    ]
}
```

修改后刷新浏览器即可看到更新。

## 总结

Jump 是一个功能丰富又易于部署的个人导航页面工具。通过简单的 Docker 命令，你就能在几分钟内搭建起一个属于自己的网站导航中心。无论是管理内网服务还是收藏常用网站，Jump 都能提供优雅的解决方案。结合内网穿透工具，你还可以随时随地访问你的导航页面。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/personal-navigation-server-monitoring-docker-jump-intranet-penetration>


## 相关文章

- [开源MediaWiki本地部署教程，Docker快速搭建个人维基百科系统，内网穿透远程访问](open-source-mediawiki-local-deployment-docker-personal-wiki-intranet-penetration.md)
- [自建短链接服务YOURLS Docker部署教程，NAS一键搭建私有短链系统](self-hosted-yourls-docker-deployment-guide-nas-private-short-link-system.md)
- [自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南](self-hosted-rss-reader-selfoss-docker-deployment-guide.md)
- [PHP动态二维码生成系统部署指南，Docker一键启动内网穿透远程访问管理平台](php-dynamic-qr-code-generator-docker-deployment-guide.md)
- [Tellor轻量级任务看板应用自托管部署教程，Docker快速安装配置指南](tellor-lightweight-task-board-self-hosted-deployment-docker-installation-guide.md)
