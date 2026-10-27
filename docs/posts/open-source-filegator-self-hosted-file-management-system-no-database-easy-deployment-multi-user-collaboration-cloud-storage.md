---
title: "开源FileGator自托管文件管理系统，无需数据库轻松部署，支持多用户协作与云存储"
date: 2025-10-16
description: "了解。我已获取所有信息。等待你提供需要生成meta description的内容数据。请直接告诉我你需要为哪个页面生成description，我会按照谷歌最佳实践，生成100-150字的中文meta description。"
canonical: https://www.passnat.com/posts/open-source-filegator-self-hosted-file-management-system-no-database-easy-deployment-multi-user-collaboration-cloud-storage
---

## 项目介绍

FileGator 是一款功能强大的开源自托管文件管理系统，完全免费且开箱即用。它提供了一个现代化的网页界面，让你能够轻松管理服务器上的所有文件和文件夹。

这个项目支持多用户协作管理，你可以为不同的用户设置不同的权限和角色，让团队成员、学生或同事都能在一个统一的平台上工作。FileGator 支持所有基础文件操作：复制、移动、重命名、编辑、创建、删除、预览、压缩、解压、下载和上传。上传功能特别强大，支持拖拽上传、进度条显示、暂停和恢复，即使是大文件也能轻松处理。

此外，FileGator 还支持多种存储适配器，包括本地存储、FTP、Amazon S3、Dropbox、Azure Blob 等，让你可以连接到各种第三方云存储服务。无需数据库，简单轻量，却功能完整。

项目地址：[点我跳转](https://github.com/filegator/filegator)

## 部署安装

若需在外网访问该项目，建议配置内网穿透工具，推荐使用 [帕斯内网穿透](https://passnat.com)。先行注册账号备用，具体操作步骤可参考 [全平台上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### Docker 一键部署

打开你的飞牛 NAS 或其他 NAS 设备，进入容器管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**，在镜像地址栏填入：

```
filegator/filegator
```

等待镜像下载完成后，点击启动按钮创建容器。

接下来进入容器配置界面：

**端口映射设置：**

容器端口：8080

主机端口：8080（如果主机上 8080 端口已被占用，可修改为其他端口，例如 8081、8082 等，但容器内的端口保持 8080 不变）

**存储位置配置：**

FileGator 需要两个持久化目录来保存文件和配置信息：

创建一个名为 'repository' 的文件夹，用于存放用户上传和管理的文件

创建一个名为 'private' 的文件夹，用于存放系统配置和用户数据

将这两个文件夹分别映射到容器内的：

/var/www/filegator/repository

/var/www/filegator/private

**环境变量设置：**

FileGator 无需额外配置环境变量，保持默认设置即可。

勾选 **开机自动启动** 后，点击下一步完成容器启动。

创建成功后，在浏览器中访问 'http://你的NAS地址:8080'，使用默认账号 **admin** 和密码 **admin123** 登录即可。

登录后，建议立即修改默认密码以确保系统安全。

到这里，FileGator 文件管理系统就部署完成了！你现在拥有了一个功能完整的私人文件管理中心，可以随时随地管理和分享文件。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/open-source-filegator-self-hosted-file-management-system-no-database-easy-deployment-multi-user-collaboration-cloud-storage>
