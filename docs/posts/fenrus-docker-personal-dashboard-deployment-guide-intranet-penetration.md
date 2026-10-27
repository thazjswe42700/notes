---
title: "定制化个人主页仪表板 Fenrus Docker 部署配置指南与内网穿透访问教程"
date: 2025-10-16
description: "Fenrus是一个功能强大的个人主页和仪表板应用，采用.NET+Blazor构建，支持快速打造定制化首页。提供应用快捷方式、网站链接、智能卡片、Docker终端等功能，可作为浏览器新标签页或导航面板使用，支持Docker部署和NAS安装。"
canonical: https://www.passnat.com/posts/fenrus-docker-personal-dashboard-deployment-guide-intranet-penetration
---

## 项目介绍

Fenrus 是一个功能强大的个人主页/仪表板应用，它让你可以快速打造一个属于自己的定制化首页。无论是作为浏览器新标签页还是个人导航面板，Fenrus 都能为你提供一个优雅、高效的访问入口。

这个项目采用 .NET + Blazor 构建，相比传统的 Node.js 应用具有更快的渲染性能。你可以轻松添加常用的应用快捷方式、网站链接、智能应用卡片，甚至还支持 Docker 终端、日志查看和 SSH 功能。无论你是想要一个简单的导航站点，还是需要一个功能完整的系统监控面板，Fenrus 都能满足你的需求。

项目地址：[点我跳转](https://github.com/revenz/fenrus)

## 部署安装

如果你需要在外网也能访问 Fenrus，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号。具体的使用方法可以参考 [这份详细指南](https://www.passnat.com/posts/p)，它涵盖了全平台的上手步骤。

### Docker 单命令部署

这是最快速的部署方式，只需一条命令即可启动 Fenrus：

```bash
docker run -d \
  --name=fenrus \
  -e TZ=Asia/Shanghai \
  -p 3000:3000 \
  -v /path/to/data:/app/data \
  --restart unless-stopped \
  revenz/fenrus:latest
```

**参数说明：**

`-p 3000:3000` - 将容器的 3000 端口映射到宿主机，如果端口冲突可改为 `-p 8080:3000` 等其他端口

`-v /path/to/data:/app/data` - 数据持久化目录，请将 `/path/to/data` 替换为你的实际路径（如 `/home/user/fenrus-data`）

`-e TZ=Asia/Shanghai` - 时区设置，可根据需要修改

启动后，打开浏览器访问 `http://你的IP:3000` 即可。

### 环境变量配置

**必须设置：**

无强制要求的环境变量

**可选设置：**

`PORT` - 自定义应用运行端口（默认 3000）

`TZ` - 时区设置（默认 Pacific/Auckland，建议改为 Asia/Shanghai）

**反向代理相关（高级用户）：**

`ReverseProxySettings\_\_UseForwardedHeaders` - 启用反向代理支持（默认 false）

`ReverseProxySettings\_\_KnownProxies` - 信任的代理 IP 列表

### 在 NAS 上部署

打开你的飞牛 NAS 或其他 NAS 设备的管理界面。

进入容器管理模块，点击'本地镜像'，然后选择'添加镜像'，再选择'从 URL 添加'。

在镜像地址栏填入：`revenz/fenrus:latest`，等待镜像下载完成。

下载完成后，点击该镜像的'启动'按钮来创建容器。

在容器配置界面中：

\*\*勾选开机自动启动\*\* - 这样 NAS 重启后应用会自动启动

\*\*端口映射\*\* - 将容器端口 3000 映射到宿主机（如 3000:3000），如果 3000 已被占用，可改为其他端口如 8080:3000

\*\*存储挂载\*\* - 创建一个文件夹（如 `/mnt/data/fenrus`）用于存储应用数据，将其挂载到容器内的 `/app/data` 路径

\*\*环境变量\*\* - 根据需要添加时区等变量，通常不需特殊配置

完成配置后点击启动，容器创建成功。

### 首次使用

部署完成后，在浏览器中访问 `http://你的NAS地址:3000`（如果修改了端口则使用对应端口号）。

首次进入时需要注册用户账号。在登录页面输入用户名和密码，然后点击'Register'按钮。第一个注册的用户会自动获得管理员权限，可以管理其他用户和系统设置。

注册完成后，你就可以开始配置你的个人仪表板了。通过添加应用、链接、智能卡片等组件，打造一个完全属于自己的首页体验。

本篇教程结束。希望 Fenrus 能为你的日常工作和生活带来便利！

---

原文：<https://www.passnat.com/posts/fenrus-docker-personal-dashboard-deployment-guide-intranet-penetration>


## 相关文章

- [个人应用书签仪表板DashMachine Docker部署教程 自托管NAS远程访问内网穿透配置方案](personal-bookmark-dashboard-dashmachine-docker-deploy-nas-self-hosting-remote-access-intranet-penetration.md)
- [自建图片社区平台Szurubooru，Docker极简部署NAS，内网穿透远程访问图片库](szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
- [轻量级自托管仪表板Glance，极简部署NAS内网穿透远程访问信息聚合平台](glance-lightweight-self-hosted-dashboard-nas-intranet-penetration-remote-access-information-aggregation-platform.md)
- [自建个人导航页面服务器实时监控工具，Docker快速部署Jump内网穿透远程访问](personal-navigation-server-monitoring-docker-jump-intranet-penetration.md)
