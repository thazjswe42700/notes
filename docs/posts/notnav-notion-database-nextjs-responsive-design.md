---
title: "NotNav导航网站Notion数据库驱动，Next.js打造响应式设计支持内外网切换"
date: 2025-10-16
description: "NotNav 是基于 Next.js 14 和 Notion API 的现代化导航网站解决方案。支持响应式设计、深色模式、实时搜索、多视图模式和内外网链接切换。本文详细讲解 Docker 部署步骤、环境变量配置、Notion API 密钥获取方法，帮助用户快速在 NAS 设备上搭建个性化导航平台。"
canonical: https://www.passnat.com/posts/notnav-notion-database-nextjs-responsive-design
---

## 项目介绍

NotNav 是一个基于 Next.js 14 和 Notion API 打造的现代化导航网站解决方案。这是一款功能丰富、开箱即用的导航聚合平台，完全利用 Notion 作为后端数据库，让你可以轻松管理和展示各类网站导航。

项目采用响应式设计，完美支持桌面端和移动设备，内置自动深色模式适配，提供实时搜索、分类导航、多视图模式（网格/紧凑/列表）等强大功能。最棒的是，你可以灵活切换内外网链接，无论在家还是在外都能畅快访问。

项目地址： [点我跳转](https://github.com/TWO-ICE/notnav)

## 部署安装

如需在外网环境下访问你的导航网站，建议配置内网穿透服务。可以访问 [帕斯内网穿透](https://passnat.com) 进行注册，完整的部署指南可以 [点击这里查看](https://www.passnat.com/posts/p)，助你快速上手全平台内网穿透解决方案。

### 使用 Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备，进入本地镜像管理界面。

点击 **添加镜像** → **从 URL 添加**，在镜像地址栏填入：

```
twoice/notion-nav:latest
```

等待镜像下载完成后，点击启动按钮创建容器。

#### 端口映射设置

在容器配置中，将容器内部的 **3000** 端口映射到宿主机的 **10212** 端口（或根据你的需要修改宿主机端口，如果出现端口占用提示，只需修改左侧的宿主机端口号即可，容器内部端口保持 **3000** 不变）。

#### 环境变量配置

以下是必须设置的环境变量，直接影响应用是否能正常运行：

环境变量 | 说明 | 是否必填
NOTION\_TOKEN | Notion API 密钥 | ✅ 必填
NOTION\_DATABASE\_ID | 导航数据库 ID | ✅ 必填
NOTION\_CONFIG\_DATABASE\_ID | 配置数据库 ID | ✅ 必填

可选配置项（一般无需修改）：

环境变量 | 说明 | 默认值
NODE\_ENV | 运行环境 | production
REVALIDATE\_INTERVAL | 缓存更新间隔（秒） | 3600

#### 获取 Notion API 配置

1. 访问 [Notion Integrations](https://www.notion.so/my-integrations) 创建新的集成应用
2. 获取 API 密钥作为 **NOTION\_TOKEN**
3. 在 Notion 中创建两个数据库：
 - **导航数据库**：包含字段 **title**（标题）、**description**（描述）、**category**（分类）、**icon**（图标）、**link**（外网链接）、**lanlink**（内网链接）
 - **配置数据库**：包含字段 **type**（值为'order'）、**title**（分类名称）、**value**（排序值）
4. 分别获取这两个数据库的 ID 作为 **NOTION\_DATABASE\_ID** 和 **NOTION\_CONFIG\_DATABASE\_ID**

填入所有必需的环境变量后，点击下一步启动容器。

#### 存储持久化

该应用主要依赖 Notion 作为数据存储，无需特殊的本地存储映射。

启动完成后，打开浏览器访问 **http://你的NAS\_IP:10212**，即可看到你的个性化导航网站正常运行。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/notnav-notion-database-nextjs-responsive-design>


## 相关文章

- [开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问](open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access.md)
- [AI盘网盘搜索聚合平台，Next.js打造智能资源管理，NAS一键部署无需复杂配置，内网穿透远程访问。](ai-pan-wangpan-search-aggregation-platform-nextjs-intelligent-resource-management-nas-one-click-deployment-no-complex-configuration-intranet-penetration-remote-access.md)
- [开源日程管理系统Cal.com自托管部署指南，NAS极简配置内网穿透远程访问](open-source-calendar-management-system-cal-com-self-hosted-deployment-guide-nas-simple-configuration-intranet-penetration-remote-access.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
