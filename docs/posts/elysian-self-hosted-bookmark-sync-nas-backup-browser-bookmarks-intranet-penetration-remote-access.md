---
title: "自托管书签同步工具Elysian，NAS私密备份浏览器书签，内网穿透远程访问同步"
date: 2025-10-16
description: "Elysian是自托管书签同步工具，自动备份浏览器书签到家庭NAS。支持实时同步创建、排序、更新、删除操作，完全私有化保护数据安全。Docker快速部署，轻松搭建个人书签管理系统，告别隐私泄露风险。"
canonical: https://www.passnat.com/posts/elysian-self-hosted-bookmark-sync-nas-backup-browser-bookmarks-intranet-penetration-remote-access
---

## 项目介绍

Elysian 是一款自托管的书签同步工具，专门为那些经常使用浏览器书签栏的用户而设计。它能够将你浏览器中常用的书签自动备份并同步到你的家庭实验室（家用NAS）中。

与市面上其他书签管理服务（如 Linkding、Linkwarden 等）不同，Elysian 的核心优势在于它专注于实时同步你浏览器书签栏中的常用书签。通过配套的浏览器扩展程序，你可以轻松地将书签导入/导出到你的家庭服务器。一旦成功连接，你在浏览器中对书签进行的任何操作（创建、排序、更新、删除）都会自动同步到服务器端，确保你的书签数据始终保持一致。

这是一个完全私有化的解决方案，你的所有书签数据都掌握在自己手中，无需担心隐私泄露问题。

项目地址：[点我跳转](https://github.com/Aadityajoshi151/Elysian)

## 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透服务。你可以前往 [帕斯内网穿透](https://passnat.com) 注册账号备用。具体的使用方法可以 [点击这里查看](https://www.passnat.com/posts/p) 详细的上手攻略。

### Docker 快速部署

打开你的飞牛 NAS 或其他 NAS 设备的管理界面。

进入本地镜像管理，选择'添加镜像'，然后从 URL 添加。

在镜像地址栏填入：aadityajoshi151/elysian:latest

等待镜像下载完成后，点击启动按钮来创建容器。

在容器配置界面，勾选'开机自动启动'选项，然后进入下一步。

**端口配置**

在端口设置中，将容器的 6161 端口映射到宿主机的某个端口（推荐保持为 6161）。如果你的系统中 6161 端口已被占用，可以修改为其他可用端口号，但请记住映射后的端口号，这样才能正确访问应用。

**存储配置**

如果你需要持久化保存书签数据，建议在 NAS 上创建一个文件夹（例如 '/data/elysian'），然后将其映射到容器内的 '/Elysian' 目录，确保数据在容器重启后不会丢失。

**环境变量设置**

根据项目需求，你可以设置以下环境变量：

API\_KEY：用于安全认证的 API 密钥（可选，但建议设置以增强安全性）

其他配置项保持默认即可。

完成所有配置后，点击启动按钮。

创建成功后，你就可以通过浏览器访问 'http://你的NAS地址:6161' 来打开 Elysian 了。

到这里，Elysian 的部署就全部完成了！你现在可以安装浏览器扩展程序，并开始同步你的书签了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/elysian-self-hosted-bookmark-sync-nas-backup-browser-bookmarks-intranet-penetration-remote-access>


## 相关文章

- [自托管书签管理工具Servas无公网部署指南 NAS极简配置内网穿透远程访问](servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access.md)
- [mTab书签管理工具私有部署指南，跨平台同步无广告，内网穿透远程访问](mtab-bookmark-manager-private-deployment-guide-cross-platform-sync-no-ads-intranet-penetration-remote-access.md)
- [自建私人书签管理库 一处部署随处访问，OneNav Docker极简快速部署指南](onenav-docker-bookmark-manager-deployment-guide.md)
- [轻量级书签管理工具Shiori，NAS一键部署，支持内网穿透远程访问书签](lightweight-bookmark-manager-shiori-nas-one-click-deployment-intranet-penetration-remote-access.md)
- [自托管个人知识库Karakeep，AI智能标签书签管理，NAS极简部署内网穿透远程访问](karakeep-ai-bookmark-manager-nas-deployment.md)
