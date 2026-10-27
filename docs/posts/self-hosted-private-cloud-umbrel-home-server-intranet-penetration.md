---
title: "自建私有云掌控数据，Umbrel家庭服务器系统轻松部署内网穿透远程访问"
date: 2025-10-16
description: "Umbrel是开源家庭服务器操作系统，让你拥有完全私有的云服务。支持树莓派、x86和虚拟机多平台部署，内置应用商店一键安装各类应用。完全本地存储数据，无需上传云端，掌控个人隐私和信息安全。提供详细安装指南和内网穿透配置方案。"
canonical: https://www.passnat.com/posts/self-hosted-private-cloud-umbrel-home-server-intranet-penetration
---

## 项目介绍

Umbrel 是一个革命性的家庭服务器操作系统，让你拥有属于自己的私有云。与其他云服务不同，Umbrel 不会将你的数据存储在他人的服务器上，也不会将你的信息出售给广告商。相反，它允许你在家中轻松搭建自己的服务器，完全掌控自己的数据和应用。

这是一个真正意义上'由你掌控的云'。Umbrel 基于开源技术构建，支持 Raspberry Pi、x86 系统和虚拟机等多种硬件平台。通过内置的应用商店，你可以轻松安装各种应用，实现文件存储、备份、NAS 功能等。界面简洁易用，即使是技术小白也能快速上手。

项目地址：[点我跳转](https://github.com/getumbrel/umbrel)

## 部署安装

如果你想在外网也能访问家中的 Umbrel 服务器，需要配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)帕斯内网穿透全平台快速上手指南。

Umbrel 提供了多种安装方式，以下是针对不同硬件的快速部署指南：

### Raspberry Pi 5 用户

打开 Umbrel 官方网站下载 Raspberry Pi 5 专用的系统镜像，使用 Raspberry Pi Imager 工具将镜像写入 SD 卡。插入树莓派，首次启动后系统会自动初始化，按照屏幕提示完成基础配置即可。

详细步骤请参考：[Raspberry Pi 5 安装指南](https://github.com/getumbrel/umbrel/wiki/Install-umbrelOS-on-a-Raspberry-Pi-5)

### x86 系统用户

如果你使用普通的 PC 或工控机（支持 x86 架构），可以下载 Umbrel x86 镜像，通过 U 盘启动安装。系统会引导你完成分区、安装和初始化配置。

详细步骤请参考：[x86 系统安装指南](https://github.com/getumbrel/umbrel/wiki/Install-umbrelOS-on-x86-Systems)

### 虚拟机用户

在 Proxmox、VMware 或 VirtualBox 等虚拟化平台上，可以直接使用 Umbrel 镜像创建虚拟机。分配足够的存储空间（建议 50GB 以上）和内存（建议 4GB 以上），然后启动虚拟机完成初始化。

详细步骤请参考：[虚拟机安装指南](https://github.com/getumbrel/umbrel/wiki/Install-umbrelOS-on-a-Linux-VM)

### 快速访问

安装完成后，在同一网络内打开浏览器，访问 `http://umbrel.local` 或 `http://你的设备IP` 即可进入 Umbrel 管理界面。首次登录需要设置管理员密码，建议使用强密码保护你的私有云。

### 应用商店

Umbrel 内置了官方应用商店，你可以一键安装各种应用，包括文件管理、备份工具、媒体服务器、开发工具等。所有应用都经过精心筛选，确保兼容性和安全性。

### 存储和备份

系统支持外接 USB 硬盘或 NAS 作为扩展存储，可以自动备份重要数据。所有备份都存储在本地，不会上传到任何云服务。

本篇教程到此结束，祝你成功搭建属于自己的私有云！

---

原文：<https://www.passnat.com/posts/self-hosted-private-cloud-umbrel-home-server-intranet-penetration>
