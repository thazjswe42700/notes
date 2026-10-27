---
title: "开源RSS阅读器一键部署，聚合订阅内容无限浏览，NAS自建私人阅读平台内网穿透远程访问"
date: 2025-10-16
description: "RSS 聚合阅读器是一款基于 Laravel 和 Vue.js 构建的开源内容聚合平台，支持 RSS 和 ATOM 格式订阅。提供自定义源名称、标签分类、多种阅读布局、深色主题和智能搜索功能，可一键部署到 NAS 或服务器上，让你轻松拥有属于自己的内容聚合中心，自动定时抓取各类博客、新闻和播客内容。"
canonical: https://www.passnat.com/posts/open-source-rss-reader-one-click-deploy-aggregate-subscriptions-unlimited-browsing-nas-self-hosted-private-reading-platform-intranet-penetration-remote-access
---

## RSS 聚合阅读器 - 一键部署属于你的内容聚合平台

### 项目介绍

RSS 是一款简洁高效的 RSS 源聚合阅读器，专为热爱订阅内容的用户设计。它基于 Laravel + Vue.js + TailwindCSS 构建，开箱即用，无需复杂配置。

这个项目支持 RSS 和 ATOM 两种常见的订阅格式，能够自动定时抓取你订阅的各类内容源。无论是技术博客、新闻网站还是播客，都可以统一聚合到一个简洁美观的界面中。支持自定义源名称和颜色、标签分类、多种阅读布局、深色主题适配，以及智能搜索功能。最棒的是，它完全开源免费，可以部署在自己的服务器或 NAS 上，让你真正拥有自己的阅读平台。

项目地址：[点我跳转](https://github.com/ssddanbrown/rss)

### 部署安装

想要随时随地访问你的 RSS 阅读器？建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，注册账号后即可轻松实现远程访问。详细的配置步骤可以参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)。

#### 最快速上手方案 - Docker 单命令启动

打开你的 NAS 设备或 Docker 环境，执行以下命令即可一键启动 RSS 阅读器：

```bash
docker run -d \
    --restart unless-stopped \
    -p 8080:80 \
    -v /home/barry/rss:/app/storage \
    ghcr.io/ssddanbrown/rss:latest
```

命令说明：

-p 8080:80：将容器内的 80 端口映射到主机的 8080 端口。如果 8080 端口已被占用，可修改为其他端口号（如 8081、8082 等），冒号前的数字即为主机访问端口

-v /home/barry/rss:/app/storage：将本地 `/home/barry/rss` 目录挂载到容器内的 `/app/storage` 目录，用于持久化存储配置和数据。请根据实际情况修改本地路径

启动完成后，在浏览器中访问 `http://localhost:8080` 即可打开 RSS 阅读器。

#### 或者使用 Docker Compose 方案

如果你更习惯使用 Docker Compose，可以复制下列配置文件：

```yaml
---
version: "2"
services:
    rss:
        image: ghcr.io/ssddanbrown/rss:latest
        container_name: rss
        environment:
            - APP_NAME=RSS
        volumes:
            - ./rss-files:/app/storage
        ports:
            - "8080:80"
        restart: unless-stopped
```

将上述内容保存为 `docker-compose.yml` 文件，然后在文件所在目录执行：

```
docker-compose up -d
```

容器会自动创建 `rss-files` 文件夹用于存储配置和数据。同样，如果 8080 端口冲突，修改 `ports` 字段中冒号前的数字即可。

### 配置你的订阅源

启动后，需要在 `rss-files/feeds.txt` 文件中添加你要订阅的 RSS 源。文件格式如下：

```
https://feed.url.com/feed.xml 源名称 #标签1 #标签2
https://example.com/feed.xml 技术新闻 #编程 #资讯

# 以 # 开头的行为注释，会被忽略
# 空行也会被忽略

# 源名称中的下划线会被转换为空格
https://example.com/feed-b.xml 科技资讯 #科技

# 可以为源设置自定义颜色（CSS 颜色值）
https://example.com/feed-c.xml 蓝色新闻[#0078b9] #新闻 #蓝色

# 以 - 开头的源默认隐藏，但可通过标签过滤查看
- https://example.com/feed-d.xml 隐藏源 #分类
```

编辑保存后，刷新页面即可看到新添加的订阅源。

### 环境变量配置（可选）

如果需要进一步定制应用，可在 Docker 启动时添加以下环境变量：

**必须设置：**

无（所有配置都有合理的默认值）

**可选设置：**

APP\_NAME：应用名称，默认为 'RSS'，仅在浏览器标签页显示

APP\_FEED\_UPDATE\_FREQUENCY：订阅源更新频率（分钟），默认 60 分钟，最低 5 分钟

APP\_LOAD\_POST\_THUMBNAILS：是否加载文章缩略图，默认 true（开启）

APP\_PRUNE\_POSTS\_AFTER\_DAYS：自动删除旧文章的天数，默认不删除。设置为 30 表示 30 天后自动清理

这些变量都有合理的默认值，新手用户无需修改，直接启动即可正常使用。

### 完成部署

创建容器成功后，访问 `http://你的IP:8080` 即可打开 RSS 阅读器。

首次进入会看到一个干净的界面，此时需要在 `feeds.txt` 中添加你的订阅源。应用会每小时自动更新一次订阅内容（可配置），你也可以手动刷新来立即获取最新文章。

支持三种阅读模式（卡片、列表、紧凑），可根据个人习惯切换。深色主题会自动跟随系统设置，移动设备也完美适配。

本篇教程结束。祝你享受自由的内容聚合体验！

---

原文：<https://www.passnat.com/posts/open-source-rss-reader-one-click-deploy-aggregate-subscriptions-unlimited-browsing-nas-self-hosted-private-reading-platform-intranet-penetration-remote-access>


## 相关文章

- [开源个人关系管理系统Monica私密部署指南，NAS一键Docker快速安装配置教程](open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install.md)
- [自建 RSS 阅读器 FreshRSS：飞牛 NAS Docker 部署，关注的更新汇到一页，配内网穿透在外面也能刷](self-hosted-rss-reader-freshrss-nas-docker-deployment-intranet-penetration.md)
- [自建RSS阅读聚合中心，内网穿透远程订阅，Selfoss极简Docker部署完全指南](self-hosted-rss-reader-selfoss-docker-deployment-guide.md)
- [使用Morss完整抓取RSS全文内容，NAS Docker极速部署，内网穿透远程订阅](morss-nas-docker-rss-remote-subscription.md)
- [轻量级自托管仪表板Glance，极简部署NAS内网穿透远程访问信息聚合平台](glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform.md)
