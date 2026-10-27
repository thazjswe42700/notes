---
title: "自建私密书签导航站，NeonLink 轻量部署 Docker 一键启动，内网穿透外网访问"
date: 2025-10-16
description: "轻量级开源自托管书签服务，支持Docker快速部署。提供标签分类、快速搜索、自动获取网站图标等功能，完全私密自主，所有数据本地存储。资源占用极低，适合树莓派和NAS运行。支持自定义背景主题，打造专属导航页面。"
canonical: https://www.passnat.com/posts/self-hosted-private-bookmark-navigation-neonlink-lightweight-docker-one-click-deployment-intranet-penetration-external-access
---

## 项目介绍

NeonLink 是一个轻量级、开源的自托管书签服务平台。它采用简洁的设计理念，系统依赖最少，通过 Docker 即可快速部署。由于资源占用极低，特别适合在树莓派等低配置设备上运行。

NeonLink 提供了完整的书签管理功能，包括标签分类、快速搜索、自动获取网站图标和描述等实用特性。你可以自定义背景主题，打造专属于自己的导航页面。整个应用完全私密自主，所有数据都存储在本地，无需担心隐私泄露问题。

无论你是想搭建个人导航站，还是为家庭网络构建共享书签库，NeonLink 都能轻松胜任。

项目地址： [点我跳转](https://github.com/AlexSciFier/neonlink)

## 部署安装

想要在外网也能访问你的书签服务？建议配置内网穿透功能。可以考虑使用 [帕斯内网穿透](https://passnat.com) 这样的专业服务，先完成账号注册。具体操作步骤可参考 [全平台上手指南](https://www.passnat.com/posts/p)，帮助你快速建立外网访问通道。

### 最快速部署方案（Docker 单命令）

打开你的 NAS 设备或服务器终端，执行以下命令即可一键启动 NeonLink：

```bash
docker run -p 80:3333 -v /path/to/data:/app/data -v /path/to/backgrounds:/app/public/static/media/background alexscifier/neonlink:latest
```

参数说明：

80 - 你想要访问的外部端口（可根据需要修改，如 8080、3000 等，如果提示端口被占用请修改此数字）

/path/to/data - 本地数据存储路径，用于保存你的书签和配置信息（请替换为实际路径，如 /mnt/data/neonlink）

/path/to/backgrounds - 背景图片存储路径（请替换为实际路径，如 /mnt/data/neonlink/backgrounds）

快速上手步骤：

在你的 NAS 或服务器上创建两个文件夹用于数据持久化：

```
mkdir -p /mnt/data/neonlink/data
mkdir -p /mnt/data/neonlink/backgrounds
```

执行 Docker 启动命令：

```bash
docker run -p 80:3333 -v /mnt/data/neonlink/data:/app/data -v /mnt/data/neonlink/backgrounds:/app/public/static/media/background alexscifier/neonlink:latest
```

等待容器启动完成后，在浏览器中访问 http://你的设备IP:80 即可打开 NeonLink 书签管理界面

容器内部端口说明：

容器内部服务运行在 3333 端口，这是固定的，无需修改

冒号前的端口号（本例中的 80）是你在外部访问时使用的端口，可以自由设置

关于数据持久化：

/app/data - 这个目录存储所有的书签数据和应用配置，非常重要，请确保映射正确

/app/public/static/media/background - 这个目录用于存放自定义背景图片，可选项

如果你的端口 80 已被占用，可以改为其他端口，比如：

```bash
docker run -p 8080:3333 -v /mnt/data/neonlink/data:/app/data -v /mnt/data/neonlink/backgrounds:/app/public/static/media/background alexscifier/neonlink:latest
```

然后访问 http://你的设备IP:8080 即可。

### Docker Compose 部署方案

如果你偏好使用 Docker Compose，可以按照以下步骤操作：

```bash
# 克隆项目仓库
git clone https://github.com/AlexSciFier/neonlink.git
cd neonlink

# 编辑 docker-compose.yml 文件（根据需要调整端口和路径）
# 然后启动服务
docker-compose up -d
```

环境配置说明：

NeonLink 的标准配置无需额外的环境变量设置，开箱即用。所有必要的参数都已在 docker-compose.yml 中预配置完毕，你只需要根据实际情况调整端口映射和存储路径即可。

### 访问你的 NeonLink

部署完成后，在浏览器中输入以下地址访问：

```
http://你的设备IP:3333
```

（如果你修改了外部端口映射，请使用对应的端口号）

页面加载成功后，你就可以开始添加书签、管理标签、自定义背景等操作了。

## 总结

通过以上步骤，你已经成功部署了 NeonLink 书签服务。这个轻量级应用特别适合在家庭 NAS、树莓派或其他低配置设备上运行。享受私密、高效的书签管理体验吧！

---

原文：<https://www.passnat.com/posts/self-hosted-private-bookmark-navigation-neonlink-lightweight-docker-one-click-deployment-intranet-penetration-external-access>


## 相关文章

- [自建私人书签管理库 一处部署随处访问，OneNav Docker极简快速部署指南](onenav-docker-bookmark-manager-deployment-guide.md)
- [自建书签管理器Linkding极简部署指南，NAS内网穿透远程访问书签收藏库](linkding-bookmark-manager-nas-remote-access-guide.md)
- [轻量级书签管理工具Shiori，NAS一键部署，支持内网穿透远程访问书签](lightweight-bookmark-manager-shiori-nas-one-click-deployment-intranet-penetration-remote-access.md)
- [轻量级个人导航页面 Flare 快速部署教程，飞牛 NAS Docker 部署 + 内网穿透远程访问](lightweight-personal-navigation-flare-quick-deploy-tutorial-nas-one-click-install-guide.md)
- [自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问](personal-navigation-server-monitoring-docker-jump-intranet-penetration.md)
