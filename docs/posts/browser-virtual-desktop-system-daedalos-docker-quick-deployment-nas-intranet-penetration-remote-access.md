---
title: "浏览器虚拟桌面系统daedalOS Docker快速部署 NAS内网穿透远程访问"
date: 2025-10-16
description: "daedalOS是创新的浏览器桌面环境模拟器，基于Node.js+React构建，提供完整操作系统体验。内置文件管理器、代码编辑器、游戏、模拟器等丰富应用，界面精美仿Windows风格。支持Docker快速部署，配置端口映射和数据持久化后即可在浏览器中享受虚拟桌面系统。"
canonical: https://www.passnat.com/posts/browser-virtual-desktop-system-daedalos-docker-quick-deployment-nas-intranet-penetration-remote-access
---

## 项目介绍

daedalOS 是一款创新的浏览器桌面环境模拟器，它将完整的操作系统体验直接带入你的网页浏览器。这个项目基于 Node.js + React 构建，采用现代化的 TypeScript 技术栈，为用户提供了一个功能丰富、开箱即用的虚拟桌面系统。

无论你身在何处，只需打开浏览器就能享受到完整的桌面操作体验。项目内置了丰富的应用程序和游戏，包括文件管理器、代码编辑器、多种模拟器、媒体播放器等。界面设计精美，完全模仿 Windows 操作系统的视觉风格，让你有种回到经典年代的感觉。

[点我跳转](https://github.com/DustinBrett/daedalOS)

项目地址：

## 部署安装

若想在公网上随时随地访问你的 daedalOS 系统，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号做好准备。具体操作步骤可参考 [帕斯全平台快速上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker 快速部署

这是最简便的部署方式，只需一条命令即可启动：

打开你的 NAS 设备或任何支持 Docker 的服务器，进入本地镜像管理界面。

点击 **本地镜像** → **添加镜像** → **从 URL 添加**

在镜像地址栏填入：dustinbrett/daedalos:latest

等待镜像下载完成后，点击启动按钮启动容器。

**端口映射设置**

在启动容器前需要配置端口映射。daedalOS 内部运行在 **3000** 端口，你可以将其映射到任意空闲的外部端口。比如想通过 http://你的IP:8080 访问，就将外部端口设为 8080。如果 3000 端口已被占用，可自行修改映射的外部端口号，但容器内部端口保持 3000 不变。

**存储配置**

daedalOS 会将用户数据和文件系统存储在 IndexedDB 中。如需持久化保存数据，建议创建一个本地文件夹（如 /data/daedalos）并将其挂载到容器内的 /root/.daedalos 目录，这样即使容器重启也不会丢失数据。

**环境变量设置**

daedalOS 无需特殊的环境变量配置，使用默认设置即可正常运行。如果遇到 Node 版本兼容性问题，系统已内置 NODE\_OPTIONS=--openssl-legacy-provider 来自动处理。

**开机自启**

勾选 **开机自动启动** 选项后，点击下一步完成启动。

创建成功后，在浏览器中访问 http://你的IP:3000（根据实际映射端口修改），即可看到精美的 daedalOS 桌面界面，开始体验虚拟操作系统的乐趣。

## 总结

通过上述步骤，你就能在几分钟内部署一个功能完整的浏览器桌面环境。daedalOS 不仅提供了经典的文件管理和应用程序功能，还集成了游戏、模拟器、代码编辑器等丰富工具，是一个非常有趣且实用的项目。赶快部署体验吧！

---

原文：<https://www.passnat.com/posts/browser-virtual-desktop-system-daedalos-docker-quick-deployment-nas-intranet-penetration-remote-access>


## 相关文章

- [帕斯内网穿透全平台上手攻略，PassNAT如何使用，飞牛NAS | 群晖NAS | Windows 系统 内网穿透](p.md)
- [无状态密码管理器Heimdallr，一密掌控所有账户，NAS本地部署零风险，内网穿透远程访问无忧](heimdallr-stateless-password-manager-nas-local-deployment.md)
- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [自建私人漫画库 Suwayomi 服务器，无限漫画源在线阅读，NAS 一键部署内网穿透远程访问](suwayomi-private-manga-server-nas-deployment.md)
