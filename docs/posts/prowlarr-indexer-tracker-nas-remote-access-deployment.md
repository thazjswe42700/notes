---
title: "统一管理索引器追踪器，Prowlarr内网穿透远程访问，NAS一键部署高效下载"
date: 2025-10-16
description: "Prowlarr是一个索引器管理和代理工具，基于.NET/ReactJS技术栈打造。它能统一管理种子追踪器和Usenet索引器，原生支持24个Usenet索引器和500+个种子追踪器。通过Prowlarr可轻松集成Lidarr、Radarr、Sonarr等多个应用，实现一站式索引器配置管理，提供索引器同步、搜索统计、代理支持等丰富功能，让媒体下载工作流更加高效便捷。"
canonical: https://www.passnat.com/posts/prowlarr-indexer-tracker-nas-remote-access-deployment
---

## 项目介绍

Prowlarr 是一个强大的索引器管理和代理工具，专为 PVR（个人视频录制）应用打造。它基于流行的 \*arr .NET/ReactJS 技术栈构建，能够统一管理你的所有种子追踪器和 Usenet 索引器。通过 Prowlarr，你可以轻松集成 Lidarr、Mylar3、Radarr、Readarr 和 Sonarr 等多个应用，实现一站式的索引器配置和管理，无需在每个应用中重复设置。

Prowlarr 原生支持 24 个 Usenet 索引器以及超过 500 个种子追踪器，并支持自定义 YML 定义来扩展更多资源。它提供索引器同步、搜索历史统计、手动搜索、代理支持等丰富功能，让你的媒体下载工作流更加高效便捷。

项目地址：[点我跳转](https://github.com/Prowlarr/Prowlarr)

## 部署安装

如果你需要在家外也能访问 Prowlarr，建议配置内网穿透功能。推荐使用 [帕斯内网穿透](https://passnat.com) 服务，先注册好账号。详细的配置步骤可以参考 [内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，让你随时随地都能安全地访问家中的应用。

### 快速部署方式

打开你的 NAS 设备（飞牛 NAS 或其他品牌），进入 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
hotio/prowlarr:latest
```

等待镜像下载完成后，点击启动按钮来创建容器。

### 容器配置步骤

**第一步：基础设置**

勾选 **开机自动启动** 选项，确保 NAS 重启后 Prowlarr 能够自动运行，然后点击下一步。

**第二步：端口映射**

Prowlarr 默认运行在容器内的 **9696 端口**。在端口设置中，将容器端口 9696 映射到你的 NAS 端口（建议保持为 9696，如果该端口已被占用，可以修改为其他可用端口如 9697）。记住你设置的外部端口号，后续访问时会用到。

**第三步：存储配置**

Prowlarr 需要持久化存储配置数据。在存储位置设置中，创建一个新文件夹（建议命名为 `prowlarr`），然后将其映射到容器内的 `/config` 目录，这样即使容器重启，你的所有配置和数据也能保留。

**第四步：环境变量设置**

根据你的需求，可以配置以下环境变量：

**必须设置：**

TZ: 时区设置（默认：`UTC`，建议改为 `Asia/Shanghai` 或你所在地区）

**可选设置：**

PUID: 运行用户 ID（默认：`911`）

PGID: 运行用户组 ID（默认：`911`）

其他环境变量保持默认即可，无需额外配置。

**第五步：启动应用**

完成以上配置后，点击启动按钮。容器会自动拉起并运行 Prowlarr。

### 访问应用

创建成功后，在浏览器中访问：

```
http://你的NAS地址:9696
```

（如果你修改了外部端口，请将 9696 替换为你设置的端口号）

稍等片刻，Prowlarr 的管理界面就会加载完成，你可以开始配置你的索引器和追踪器了。

至此，Prowlarr 的部署就全部完成了。现在你可以在这个统一的平台上管理所有的索引器资源，让你的媒体下载体验更加顺畅高效。

---

原文：<https://www.passnat.com/posts/prowlarr-indexer-tracker-nas-remote-access-deployment>


## 相关文章

- [Jellyseerr开源影视请求管理系统，NAS一键部署Jellyfin媒体库，内网穿透远程访问审批影视资源](jellyseerr-nas-jellyfin-media-request-approval-system.md)
- [MeiliSearch闪电搜索引擎Docker极速部署，内网穿透远程访问一键搭建](meilisearch-docker-deployment-intranet-penetration-remote-access.md)
- [自建私密搜索引擎SearXNG，聚合245个搜索源零追踪，NAS极简Docker部署内网穿透远程访问](searxng-private-search-engine-245-sources-nas-docker-deployment.md)
- [容器管理神器Yacht一键部署指南，Docker应用模板化管理简单易用，支持内网穿透远程访问管理](yacht-docker-container-management-deployment-guide.md)
- [开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程](focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial.md)
