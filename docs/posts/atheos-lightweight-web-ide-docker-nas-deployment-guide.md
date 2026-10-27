---
title: "轻量级网页IDE Atheos极简部署指南，Docker一键启动NAS开发环境无需本地配置"
date: 2025-10-16
description: "Atheos是轻量级网页版IDE框架，Codiad的现代化升级。支持Docker一键部署，无需本地环境即可远程编码。资源占用极小，提供流畅交互体验。可在服务器、NAS设备上运行，通过浏览器随时随地进行代码编辑和项目管理。"
canonical: https://www.passnat.com/posts/atheos-lightweight-web-ide-docker-nas-deployment-guide
---

## 项目介绍

Atheos 是一个轻量级、功能强大的网页版 IDE 框架，它是对经典项目 Codiad 的现代化升级和维护。Atheos 专为追求简洁高效的开发者设计，具有极小的资源占用和最小化的系统需求。无论你是在远程服务器开发，还是想要一个随处可得的代码编辑环境，Atheos 都能为你提供流畅的、交互式的开发体验。

相比于 Eclipse、NetBeans 等庞大的桌面 IDE，Atheos 凭借其简洁的设计理念和强大的功能集赢得了众多开发者的青睐。项目致力于从代码底层到顶层的完整重写，以消除技术债务，最大化性能和功能。通过浏览器即可随时随地进行代码编辑和项目管理，无需安装任何本地开发环境。

项目地址：[点我跳转](https://github.com/Atheos/Atheos-Docker)

## 部署安装

如果需要在外网也能访问你的开发环境，建议配置内网穿透服务。你可以选择 [帕斯内网穿透](https://passnat.com) 这样的专业方案，先行注册账号以备后用。具体的部署步骤可以参考 [全平台内网穿透快速上手指南](https://www.passnat.com/posts/p)，帮助你快速建立起安全稳定的远程访问通道。

### Docker 单命令快速启动

Atheos 提供了最简单的开箱即用方案，只需一条命令即可启动：

```bash
docker run --rm -p 8080:80 -d hlsiira/atheos
```

启动完成后，打开浏览器访问 `http://localhost:8080` 即可进入 Atheos IDE 开发环境。

**参数说明：**

-p 8080:80 ：将容器内的 80 端口映射到主机的 8080 端口。如果 8080 端口已被占用，你可以修改前面的端口号（例如改为 8081、8082 等），但容器内的 80 端口保持不变。

### NAS 设备部署指南

如果你想在飞牛 NAS 或其他 NAS 设备上部署 Atheos，可以按照以下步骤操作：

**1. 添加 Docker 镜像**

打开 NAS 设备的管理界面

进入'本地镜像'模块

点击'添加镜像'，选择'从 URL 添加'

镜像地址填入：`hlsiira/atheos:latest`

等待镜像下载完成

**2. 启动容器**

镜像下载完毕后，点击'启动'按钮

勾选'开机自动启动'选项，确保 NAS 重启后容器自动运行

进入下一步进行端口配置

**3. 端口配置**

在端口设置界面，将主机端口设置为 `8080`（或其他未被占用的端口）

容器端口保持为 `80`

如遇端口冲突提示，修改主机端口号即可，容器内部端口无需改动

**4. 存储与环境变量**

本项目无需特殊的持久化存储配置

环境变量保持默认，无需手动设置

点击下一步直接启动容器

**5. 访问应用**

容器启动成功后，在浏览器中访问 `http://NAS\_IP:8080`

即可进入 Atheos IDE，开始你的代码编辑之旅

**小贴士**：如果需要扩展 Atheos 的功能，比如添加编译工具或特定的开发环境，可以基于官方镜像创建自定义 Dockerfile 进行扩展。

本篇教程结束

---

原文：<https://www.passnat.com/posts/atheos-lightweight-web-ide-docker-nas-deployment-guide>


## 相关文章

- [开源团队开发工具Team IDE一键Docker部署指南，多数据库中间件管理集成方案](open-source-team-ide-one-click-docker-deployment-guide-multi-database-middleware-management-integration.md)
- [轻量级工作流引擎Dagu无代码部署，NAS一键启动内网穿透远程管理任务](dagu-lightweight-workflow-engine-nas-remote-management-no-code.md)
- [code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码](codeserver.md)
- [轻量级全文搜索引擎ZincSearch快速部署指南，NAS极简配置Docker一键启动](zinc-search-nas-docker-deployment-guide.md)
- [开源Rust音乐服务器Diosic极简部署，NAS轻量级流媒体解决方案](diosic-rust-music-server-nas-deployment.md)
