---
title: "RetroArch复古游戏模拟器前端，支持NES、SNES、PS等多平台，NAS Docker极速部署，内网穿透远程畅玩经典游戏。"
date: 2025-10-16
description: "RetroArch是跨平台复古游戏模拟器前端，基于libretro API构建。支持NES、SNES、Genesis、PlayStation、N64等经典游戏主机平台模拟。提供Windows、Linux、macOS、Android、iOS等多系统支持，采用C语言开发确保高性能兼容性，为复古游戏爱好者提供开箱即用体验。"
canonical: https://www.passnat.com/posts/retroarch-nes-snes-ps-nas-docker-emulator
---

## 项目介绍

RetroArch 是一款跨平台的复古游戏模拟器前端，基于开源的 libretro API 构建。它是一个功能强大的统一游戏模拟器框架，支持 NES、SNES、Genesis、PlayStation、N64 等多个经典游戏主机平台的模拟。

无论你是复古游戏爱好者还是游戏开发者，RetroArch 都能为你提供开箱即用的游戏体验。通过 libretro 核心库的支持，你可以在 Windows、Linux、macOS、Android、iOS 等多个操作系统上无缝运行各类经典游戏。项目采用 C 语言开发，确保了极高的性能和兼容性。

项目地址：[点我跳转](https://github.com/libretro/RetroArch)

## 部署安装

如果你想在家庭网络外也能访问 RetroArch，建议配置内网穿透工具。可以选择 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。详细的配置步骤可以参考 [这份教程](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

RetroArch 官方并未提供官方的 Docker 镜像，但社区中有多个 Docker 实现版本可用。以下是最快速的部署方式：

**第一步：打开你的 NAS 设备**

登录飞牛 NAS 或其他支持 Docker 的 NAS 系统，进入 Docker 管理界面。

**第二步：添加镜像**

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像 URL 栏填入：retroarch/retroarch:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器。

**第三步：配置容器参数**

勾选 **开机自动启动**，然后进入下一步配置。

**端口映射设置**

RetroArch 默认使用以下端口：

容器内部端口：8080（Web 访问端口）

你可以将其映射到主机的任意端口，例如 8080:8080。如果 8080 端口已被占用，可以修改为其他端口，如 9090:8080，这样就能通过 http://你的NAS地址:9090 访问 RetroArch。

**存储位置配置**

建议创建以下目录用于数据持久化：

/home/retroarch - RetroArch 配置文件和数据存储目录

/home/retroarch/roms - 游戏 ROM 文件存储目录

在容器配置中，将这些本地目录映射到容器内部：

本地路径 /mnt/nas/retroarch → 容器路径 /home/retroarch

本地路径 /mnt/nas/roms → 容器路径 /home/retroarch/roms

**环境变量设置**

RetroArch 的环境变量配置相对简单，通常无需特殊设置。如有需要，可根据以下参数调整：

选择设置（可选）：

DISPLAY - 显示输出设置（默认：:0）

AUDIO\_DRIVER - 音频驱动选择（默认：alsa）

其他参数保持默认即可，直接点击 **启动** 完成部署。

**第四步：访问应用**

部署完成后，打开浏览器访问：http://你的NAS地址:8080

即可看到 RetroArch 的界面。首次启动时，系统会进行初始化配置，请耐心等待。

## 使用建议

ROM 文件管理：将你的游戏 ROM 文件放在映射的 /roms 目录中，RetroArch 会自动扫描识别

性能优化：根据你的硬件配置调整图形驱动（OpenGL、Vulkan 等）以获得最佳性能

手柄配置：通过 RetroArch 的菜单系统配置你的游戏手柄，支持多种输入设备

到此，RetroArch 的部署教程就全部完成了。祝你享受复古游戏的乐趣！

---

原文：<https://www.passnat.com/posts/retroarch-nes-snes-ps-nas-docker-emulator>
