---
title: "NAS搭建PanSou网盘资源搜索引擎完整教程：TG频道54插件并发搜索Docker快速部署内网穿透远程访问"
date: 2025-11-12
description: "PanSou是一款开源高性能网盘资源搜索引擎（4.7k Stars），支持从Telegram频道和54+插件并发搜索百度网盘、阿里云盘、夸克等13种主流网盘资源。采用智能排序和二级缓存技术，Docker一键部署，配合内网穿透实现远程访问。本教程详解NAS部署全流程。"
canonical: https://www.passnat.com/posts/pansoutg54nasdocker
---

## 项目介绍

PanSou 是一款开源的高性能网盘资源搜索引擎，目前已获得 4.7k Stars。它通过爬取 Telegram 资源分享频道和 54+ 个搜索插件，实现对百度网盘、阿里云盘、夸克网盘、天翼云盘等 13 种主流网盘的资源快速检索。

项目采用智能并发搜索技术，配合二级缓存机制，大幅提升搜索速度。搜索结果按网盘类型自动分类，并根据插件等级、时间新鲜度和关键词匹配度进行智能排序。

部署极其简单，Docker 一键启动即可使用，集成了前后端界面，开箱即用。

项目地址：[点我跳转](https://github.com/fish2018/pansou)

## 部署安装

如果你想在办公室或家里随时随地访问这个项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)

来实现远程访问，先注册账号备用。具体的详细操作步骤可以参考 [这份完整的上手攻略](https://www.passnat.com/posts/p)

，里面有针对各个平台的详细说明。

### 使用 Docker 快速部署

这是最推荐的部署方式，只需一条命令即可启动整个应用。

**第一步：**通过SSH访问nas设备

```
ssh 用户名@NAS的IP地址
# 例如: ssh admin@192.168.1.100
```

**第二步：切换至root：**

```bash
sudo -i
```

**第二步：复制以下内容：**

```bash
docker run -d --name pansou -p 8088:80 ghcr.io/fish2018/pansou-web
```

如果没法下载，那么则使用镜像加速站

```bash
docker run -d --name pansou -p 8088:80 ghcr.nju.edu.cn/fish2018/pansou-web
```

## 功能特性

🔍 多源并发搜索 - 同时搜索多个TG频道和40+个插件源

📦 网盘类型识别 - 自动识别百度、阿里、夸克等13种网盘链接

⚡ 智能排序算法 - 基于插件等级、时间新鲜度和关键词匹配度排序

🔄 异步插件系统 - 快速响应+后台持续处理，解决慢速源问题

💾 二级缓存机制 - 分片内存+磁盘缓存，大幅提升重复查询速度

🔐 JWT安全认证 - 可选的API认证保护，支持多用户配置

📊 结果分类展示 - 按网盘类型自动归类，支持多维度筛选

🎯 灵活的API接口 - 支持GET/POST请求，丰富的查询参数

🔌 插件化架构 - 轻松扩展新的搜索源，插件可按需启用

到这里，PanSou 的核心功能就全部搞定了。现在你可以开始体验这个功能完整、设计精美的网盘搜索平台了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/pansoutg54nasdocker>


## 相关文章

- [自动签到脚本DailyCheckIn Docker部署指南，支持NAS群晖青龙面板内网穿透远程管理](auto-checkin-script-dailycheckin-docker-deployment-nas-synology-qinglong-panel-intranet-penetration-remote-management.md)
- [自托管任务管理系统tududi Docker极简部署 本地数据完全私密可控](self-hosted-task-management-tududi-docker-deployment.md)
- [私密日记极简部署，纯文本记录无数据库，Hibiscus.txt Docker内网穿透随处访问](hibiscus-txt-private-diary-docker-deployment.md)
- [MeiliSearch闪电搜索引擎Docker极速部署，内网穿透远程访问一键搭建](meilisearch-docker-deployment-intranet-penetration-remote-access.md)
- [自建私密搜索引擎SearXNG，聚合245个搜索源零追踪，NAS极简Docker部署内网穿透远程访问](searxng-private-search-engine-245-sources-nas-docker-deployment.md)
