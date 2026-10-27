---
title: "自托管启动页Flame简易部署指南丨NAS应用中心一站式管理方案"
date: 2025-10-16
description: "自托管个人启动页面解决方案，简洁优雅的界面设计，开箱即用无需复杂配置。Flame提供可视化编辑器、应用管理、书签收藏、集成搜索、天气小部件、Docker容器自动识别等丰富功能，轻松为NAS或服务器搭建统一应用入口，支持内网穿透外网访问，随时随地管理应用中心。"
canonical: https://www.passnat.com/posts/flame-nas-deployment-guide
---

## 项目介绍

Flame 是一款自托管的个人启动页面解决方案，专为服务器而设计。它采用简洁优雅的界面设计，灵感来自于 SUI 项目，但在功能上进行了大幅扩展。Flame 开箱即用，无需复杂的文件编辑，通过内置的可视化编辑器，你可以在几分钟内搭建属于自己的应用中心。

无论你是想为家里的 NAS 创建一个统一的应用入口，还是为公司服务器搭建一个集中管理平台，Flame 都能轻松胜任。它支持应用管理、书签收藏、集成搜索、天气小部件、Docker 容器自动识别等丰富功能，让你的服务器管理变得井井有条。

项目地址：[点我跳转](https://github.com/pawelmalak/flame)

## 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地管理你的应用中心了。详细的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速入门指南。

### 最快速的部署方式

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
pawelmalak/flame
```

等待镜像下载完成后，点击启动按钮启动容器。

### 容器配置说明

**开机自动启动**：勾选此选项后，NAS 重启时容器会自动启动。

**端口映射**：

容器内部端口：`5005`

主机端口：建议保持为 `5005`（如果你的 NAS 中已有应用占用此端口，可以改为其他端口，比如 `5006`、`5007` 等，但容器内部端口必须保持为 `5005`）

**存储位置**：

容器内路径：`/app/data`

主机路径：建议在 NAS 上创建一个专用文件夹（如 `/mnt/data/flame`），用来存储 Flame 的配置文件和数据库

**环境变量设置**：

必须设置：

`PASSWORD`：访问 Flame 的登录密码（请设置一个安全的密码）

可选设置（无需配置也能正常使用）：

`PASSWORD\_FILE`：如果你使用 Docker Secrets 来管理密码，可以配置此项

其他环境变量都可以保持默认，直接下一步启动容器即可。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:5005
```

输入你设置的密码即可进入 Flame 的主界面。到这里，Flame 就已经部署完成了！

## 功能特性速览

📝 可视化管理：通过内置编辑器直接在界面上创建、编辑、删除应用和书签，无需接触任何代码

📌 快速访问：将常用的应用和网站固定到首页，一键快速打开

🔍 强大搜索：集成本地应用搜索和 11 种网络搜索引擎，还支持自定义搜索引擎

🔑 安全认证：内置身份验证系统，保护你的隐私和数据

🎨 丰富主题：提供 15 种内置主题和自定义主题构建器，支持自定义 CSS 样式

☀️ 天气小部件：实时显示当前温度、云量和天气状态

🐳 Docker 集成：自动识别和添加其他 Docker 容器中的应用

本篇教程结束

---

原文：<https://www.passnat.com/posts/flame-nas-deployment-guide>


## 相关文章

- [容器管理神器Yacht一键部署指南，Docker应用模板化管理简单易用，支持内网穿透远程访问管理](yacht-docker-container-management-deployment-guide.md)
- [自托管笔记应用Flatnotes极简部署，无数据库Markdown笔记库远程访问方案](flatnotes-self-hosted-markdown-notes-deployment.md)
- [个人应用书签仪表板DashMachine Docker部署教程 自托管NAS远程访问内网穿透配置方案](personal-bookmark-dashboard-dashmachine-docker-deploy-nas-self-hosting-remote-access-intranet-penetration.md)
- [企业IT运维管理系统CAT一键部署方案，Docker容器快速启动NAS设备应用](enterprise-it-operations-management-system-cat-one-click-deployment-docker-container-nas.md)
- [Yacht容器管理系统一键部署指南，Docker应用模板快速启动，NAS自建服务完整教程](yacht-container-management-one-click-deployment-guide-docker-application-templates-quick-start-nas-self-built-services-complete-tutorial.md)
