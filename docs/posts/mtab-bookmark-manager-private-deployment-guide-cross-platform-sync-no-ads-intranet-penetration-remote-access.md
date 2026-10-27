---
title: "mTab书签管理工具私有部署指南，跨平台同步无广告，内网穿透远程访问"
date: 2025-10-16
description: "mTab是功能强大的跨平台书签管理工具，支持Chrome、Firefox、Edge、Safari等浏览器，提供书签收藏、笔记记录、在线工具功能。界面简洁易用，完全免费无广告，支持私有部署和多设备无缝同步，让你完全掌控数据安全。"
canonical: https://www.passnat.com/posts/mtab-bookmark-manager-private-deployment-guide-cross-platform-sync-no-ads-intranet-penetration-remote-access
---

## 项目介绍

mTab 是一款功能强大的跨平台书签管理工具，集书签收藏、笔记记录、在线工具于一身。它支持 Chrome、Firefox、Edge、Safari 等主流浏览器，让你的书签和笔记在所有设备间无缝同步。

这款应用采用简洁美观的界面设计，操作逻辑清晰直观，即使是技术小白也能轻松上手。无论你是想要跨设备管理收藏夹、记录工作笔记，还是需要一些实用的在线小工具，mTab 都能满足你的需求。最重要的是，它完全免费无广告，支持私有部署，让你完全掌控自己的数据安全。

项目地址：[点我跳转](https://github.com/tsxcw/mtab)

## 部署安装

如果你想在办公室、家里或任何地方都能访问 mTab，建议配置内网穿透服务。推荐使用[帕斯内网穿透](https://passnat.com)，注册账号后即可使用。详细的操作指南可以[点我查看](https://www.passnat.com/posts/p)。

### 快速部署（Docker 单命令）

mTab 提供了最简便的 Docker 部署方式，只需一条命令即可快速启动：

```bash
docker run -itd --name mtab -p 9200:80 -v /opt/mtab:/app itushan/mtab
```

**命令说明：**

9200 是宿主机的访问端口，如果与其他服务冲突，可以改为其他端口（如 8080、8888 等）

/opt/mtab 是宿主机的数据存储目录，建议提前创建好这个文件夹，用来保存应用数据

容器内的端口和目录必须保持为 80 和 /app，不需要修改

mtab 是容器的名称，可以自定义

### NAS 可视化部署

如果你使用的是群晖 NAS 或其他带有管理面板的设备：

打开 NAS 管理面板，找到容器/镜像管理功能

添加新镜像，填写镜像地址：itushan/mtab

等待镜像下载完成后，点击启动容器

在容器配置中：

**服务器端口**：填写你想要的访问端口（如 9200）

**容器端口**：填写 80

**存储位置**：选择或创建一个文件夹用于存储应用数据，映射到容器的 /app 目录

勾选'开机自动启动'后，点击启动

### 数据库配置

部署完成后，在浏览器中访问 http://你的IP:9200（将 9200 替换为你设置的实际端口）。

首次访问会看到安装向导，需要配置数据库：

**重要提示：**如果是容器部署，数据库地址**不要填写 127.0.0.1**，因为容器内的 127.0.0.1 不指向宿主机网络。根据你的实际环境填写正确的数据库地址。

填写完数据库配置后，点击'安装'按钮，等待安装完成即可。

### 外网访问配置

如果需要从外网访问 mTab，**强烈建议**使用 Nginx 反向代理或 CDN 来代理你设置的端口，并配置 SSL 证书启用 HTTPS，以确保数据传输的安全性。纯内网环境则无需额外配置。

## 总结

通过以上步骤，你就可以快速部署 mTab 书签管理工具了。无论是单机 Docker 部署还是 NAS 可视化部署，都能在几分钟内完成。享受跨设备同步、安全私密的书签管理体验吧！

---

原文：<https://www.passnat.com/posts/mtab-bookmark-manager-private-deployment-guide-cross-platform-sync-no-ads-intranet-penetration-remote-access>


## 相关文章

- [自建私人书签管理库 一处部署随处访问，OneNav Docker极简快速部署指南](onenav-docker-bookmark-manager-deployment-guide.md)
- [自建neko远程浏览器：飞牛NAS Docker部署+内网穿透外网访问](neko-remote-browser-nas-docker-deployment-intranet-penetration-external-access.md)
- [Cronicle Edge 定时任务：NAS Docker 部署 + 内网穿透](cronicle-edge-nas-docker-scheduled-tasks-web-ui-job-logs-intranet-penetration.md)
- [自托管书签同步工具Elysian，NAS私密备份浏览器书签，内网穿透远程访问同步](elysian-self-hosted-bookmark-sync-nas-backup-browser-bookmarks-intranet-penetration-remote-access.md)
- [轻量级书签管理工具Shiori，NAS一键部署，支持内网穿透远程访问书签](lightweight-bookmark-manager-shiori-nas-one-click-deployment-intranet-penetration-remote-access.md)
