---
title: "开源网站分析平台Matomo自建部署教程 NAS Docker快速安装配置指南"
date: 2025-10-16
description: "Matomo是开源网站分析平台，Google Analytics完全替代品。采用PHP和MySQL构建，数据完全自主，支持电商追踪、目标转化、营销活动追踪等功能。Docker快速部署，配置数据库和端口映射即可使用，全球140万网站信任的分析工具。"
canonical: https://www.passnat.com/posts/matomo-open-source-analytics-platform-nas-docker-deployment-guide
---

## 项目介绍

Matomo 是一个领先的开源网站分析平台，也是 Google Analytics 的完全开源替代品。它采用 PHP 和 MySQL 构建，具有完全的数据所有权和隐私保护。用户可以在自己的服务器上安装并运行 Matomo，所有访客数据都存储在自己的数据库中，不会被第三方追踪。

Matomo 功能强大，支持电商追踪、目标转化追踪、营销活动追踪、自定义变量、邮件报表、地理定位、实时访问地图等高级分析功能。已被全球超过 140 万个网站使用，是真正意义上'掌握数据命运'的分析工具。

项目地址：[点我跳转](https://github.com/matomo-org/matomo)

## 部署安装

若需要在外网访问你的 Matomo 分析平台，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。详细的操作步骤可参考 [全平台上手指南](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理界面，进入本地镜像管理，选择从 URL 添加镜像。

在镜像地址栏填入：**matomo:latest**

等待镜像下载完成后，点击启动按钮创建容器。

**端口配置说明：**

Matomo 默认运行在容器内的 80 端口（HTTP）和 443 端口（HTTPS）。在创建容器时，你需要将这些端口映射到宿主机。建议将容器的 80 端口映射到宿主机的 8080 端口（如果 80 端口已被占用）。如果出现端口冲突提示，可以自行修改宿主机端口号，但容器内的端口号需保持不变。

**数据持久化配置：**

Matomo 需要持久化存储数据库和配置文件。建议在宿主机创建以下目录用于数据挂载：

/data/matomo/data - 用于存储 Matomo 数据和配置

/data/matomo/mysql - 用于存储 MySQL 数据库文件

将这些目录分别映射到容器内的对应位置。

**环境变量设置：**

**必须设置：**

MATOMO\_DATABASE\_HOST - MySQL 数据库主机地址（通常为 localhost 或 127.0.0.1）

MATOMO\_DATABASE\_USERNAME - 数据库用户名

MATOMO\_DATABASE\_PASSWORD - 数据库密码

MATOMO\_DATABASE\_DBNAME - 数据库名称

**可选设置：**

MATOMO\_ASSUME\_SECURE\_PROTOCOL - 如果使用了 HTTPS 代理，设置为 1

MATOMO\_PROXY\_URI\_HEADER - 代理 URI 头部设置

如果没有特殊需求，其他环境变量可以保持默认值。完成配置后点击启动即可。

**访问应用：**

容器启动成功后，在浏览器中访问 **http://你的IP:8080**（如果你映射的是 8080 端口），即可进入 Matomo 的安装向导。

按照向导完成以下步骤：

1. 系统检查 - 确保服务器满足 Matomo 的运行要求

2. 数据库配置 - 输入 MySQL 数据库信息

3. 创建超级用户 - 设置管理员账号和密码（请妥善保管）

4. 添加网站 - 配置你要追踪的第一个网站

5. 安装追踪代码 - 复制生成的 JavaScript 代码到你的网站页面

完成安装后，Matomo 就可以开始收集和分析你网站的访客数据了。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/matomo-open-source-analytics-platform-nas-docker-deployment-guide>
