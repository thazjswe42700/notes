---
title: "无状态密码管理器Heimdallr，一密掌控所有账户，NAS本地部署零风险，内网穿透远程访问无忧"
date: 2025-10-16
description: "Heimdallr是创新的无状态密码管理器，只需记住一个主密码即可为任何网站生成对应密码。支持Windows、Linux、macOS命令行工具和浏览器网页版，无需安装软件。本文详细介绍了在NAS上通过Docker快速部署Heimdallr的步骤，包括镜像配置、端口映射和访问方法，帮助用户轻松实现密码的安全管理。"
canonical: https://www.passnat.com/posts/heimdallr-stateless-password-manager-nas-local-deployment
---

## 项目介绍

Heimdallr 是一个创新的无状态密码管理器和生成器。厌倦了不断记住各个网站和应用的密码？担心在多个设备间同步密码时遗漏或忘记更新？Heimdallr 完美解决这些问题！这款工具只需你记住一个主密码，即可基于服务名称和账户名为任何网站生成对应的密码，完全避免了密码泄露的风险。

Heimdallr 提供了灵活的使用方式——既可以通过命令行工具在 Windows、Linux 和 macOS 上快速生成密码，也可以直接在浏览器中使用网页版应用，无需安装任何软件。无论你是技术爱好者还是普通用户，都能轻松上手。

项目地址：[点我跳转](https://github.com/AMEST/Heimdallr)

## 部署安装

如果你需要在公网上访问 Heimdallr，建议配置内网穿透工具。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，通过简单配置即可实现远程访问。详细的操作步骤可以 [点击这里查看](https://www.passnat.com/posts/p)。

### 最快速上手方案（Docker 单命令）

打开你的飞牛 NAS 或其他 NAS 设备，找到 Docker 应用管理界面。

在本地镜像中选择 **添加镜像** → **从 URL 添加**，输入以下镜像地址：

```
eluki/heimdallr-password-manager
```

等待镜像下载完成后，点击启动按钮。

### 容器配置步骤

**1. 基础设置**

勾选 **开机自动启动** 以确保服务持续运行

**2. 端口映射**

Heimdallr 默认使用容器内部的 **80 端口**。你需要将其映射到主机的一个可用端口，建议使用 **30003** 端口（如果该端口已被占用，可修改为其他未使用的端口）。

映射规则如下：

容器内端口：`80`

主机端口：`30003`（可根据需要修改）

**3. 环境变量**

Heimdallr 无需特殊的环境变量配置，保持默认即可。

**4. 存储配置**

该应用为无状态设计，无需挂载额外的存储卷。

完成上述配置后，点击启动容器。

### 访问应用

容器启动成功后，在浏览器中访问：

```
http://你的NAS地址:30003
```

即可打开 Heimdallr 密码管理器的网页界面。输入你的主密码和服务信息，就能快速生成对应的密码了。

## 总结

通过以上步骤，你已经成功在 NAS 上部署了 Heimdallr 密码管理器。从此以后，只需记住一个强大的主密码，就能为所有网站和应用生成安全的密码，再也不用担心密码管理的烦恼。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/heimdallr-stateless-password-manager-nas-local-deployment>


## 相关文章

- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
- [装忙神器Genact：Rust编写跨平台终端动画，NAS一键部署模拟工作场景](genact-rust-terminal-animation-nas-deployment.md)
- [自建私人漫画库 Suwayomi 服务器，无限漫画源在线阅读，NAS 一键部署内网穿透远程访问](suwayomi-private-manga-server-nas-deployment.md)
- [DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维](dweebui-container-management-panel-docker-podman-deployment.md)
- [自建RustDesk远程桌面服务器,NAS极简部署无公网穿透远程控制,开源跨平台屏幕共享工具](rustdesk-nas-remote-desktop-server-self-hosted.md)
