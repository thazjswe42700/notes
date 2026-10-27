---
title: "Oxker Docker容器管理工具TUI界面轻量级终端控制，Rust开发支持实时监控日志查看"
date: 2025-10-16
description: "Oxker是一款轻量级Docker容器管理工具，采用TUI文本界面设计，使用Rust开发。支持容器实时监控、日志查看、容器管理等功能。提供简洁强大的终端操作方式，支持多架构部署。通过Docker一键启动，配置灵活，快捷键操作高效便捷。"
canonical: https://www.passnat.com/posts/oxker-docker-container-management-tui-lightweight-terminal-rust-realtime-monitoring
---

## 项目介绍

Oxker 是一个轻量级的 Docker 容器管理工具，采用 TUI（文本用户界面）设计，使用 Rust 语言开发。它基于 ratatui 和 Bollard 库构建，提供了一个简洁而强大的方式来查看和控制 Docker 容器。

相比于复杂的 Docker 桌面应用，Oxker 提供了一个轻快的终端界面，支持容器实时监控、日志查看、容器管理等功能。无论你是在服务器上还是本地开发环境，都能通过简单的键盘快捷键快速完成各种 Docker 操作。

项目地址：[点我跳转](https://github.com/mrjackwills/oxker)

## 部署安装

如需在外网也能访问你的服务，建议配置内网穿透工具。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，参考 [内网穿透上手攻略](https://www.passnat.com/posts/p) 快速上手。

### 最快速上手方式 - Docker 单命令启动

Oxker 已发布到 Docker Hub 和 ghcr.io，支持 `linux/amd64`、`linux/arm64` 和 `linux/arm/v6` 架构。

**通过 Docker Hub 启动：**

```bash
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock:ro --pull=always mrjackwills/oxker
```

**或者通过 ghcr.io 启动：**

```bash
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock:ro --pull=always ghcr.io/mrjackwills/oxker
```

复制上述命令到你的 NAS 或服务器终端中执行即可。容器会自动拉取最新镜像并启动。

### 关键参数说明

--rm：容器退出后自动删除

-it：以交互模式运行，保持终端连接

-v /var/run/docker.sock:/var/run/docker.sock:ro：挂载 Docker 套接字（只读），这是 Oxker 与 Docker 通信的必要条件

--pull=always：每次启动时拉取最新镜像

### 进阶配置 - 使用自定义配置文件

如果需要自定义快捷键、颜色方案等设置，可以挂载配置文件：

```bash
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock:ro -v /your/config/path/config.toml:/config.toml:ro ghcr.io/mrjackwills/oxker
```

将 `/your/config/path/config.toml` 替换为你本地配置文件的实际路径。配置文件支持 `.toml`、`.json` 和 `.jsonc` 格式。

### 常用快捷键

启动后，你可以使用以下快捷键进行操作：

快捷键 | 功能

--------|------

`Tab` / `Shift+Tab` | 切换面板

`↑ ↓` / `j k` | 上下滚动

`Enter` | 执行选中的 Docker 命令

`1-9` | 按列排序容器

`F1` / `/` | 进入过滤模式

`#` | 搜索日志

`-` / `=` | 调整日志面板高度

`e` | 进入容器执行命令（不支持 Windows）

`s` | 保存日志到文件

`h` | 显示帮助菜单

`q` | 退出应用

### 其他安装方式

如果你的系统中已安装相应的包管理器，也可以使用以下方式安装：

**Cargo（Rust 包管理器）：**

```
cargo install oxker
```

**Homebrew（macOS）：**

```
brew install oxker
```

**AUR（Arch Linux）：**

```
paru -S oxker
```

**预编译二进制文件：**

```bash
curl https://raw.githubusercontent.com/mrjackwills/oxker/main/install.sh | bash
```

本篇教程结束。通过 Oxker，你可以在终端中优雅而高效地管理所有 Docker 容器，享受轻快的操作体验。

---

原文：<https://www.passnat.com/posts/oxker-docker-container-management-tui-lightweight-terminal-rust-realtime-monitoring>


## 相关文章

- [开源Rust音乐服务器Diosic极简部署，NAS轻量级流媒体解决方案](diosic-rust-music-server-nas-deployment.md)
- [MeiliSearch闪电搜索引擎Docker极速部署，内网穿透远程访问一键搭建](meilisearch-docker-deployment-intranet-penetration-remote-access.md)
- [自建联邦式博客引擎Plume NAS部署指南 内网穿透外网访问 Rust开源](plume-federated-blog-engine-nas-deployment-guide-intranet-penetration-external-access-rust-opensource.md)
- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
- [装忙神器Genact：Rust编写跨平台终端动画，NAS一键部署模拟工作场景](genact-rust-terminal-animation-nas-deployment.md)
