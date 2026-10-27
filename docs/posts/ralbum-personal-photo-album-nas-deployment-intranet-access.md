---
title: "Ralbum个人相册管理系统，NAS极简部署，内网穿透外网访问，照片库智能管理"
date: 2025-10-16
description: "Ralbum是轻量级个人相册管理系统，支持本地照片库自动生成和灯箱展示。提供搜索、地理位置地图、智能缩略图等功能，确保照片数据安全。支持电脑手机多设备浏览，Docker一键快速部署，内网穿透随时随地访问个人相册。"
canonical: https://www.passnat.com/posts/ralbum-personal-photo-album-nas-deployment-intranet-access
---

## Ralbum - 个人相册管理系统

### 项目介绍

Ralbum 是一款轻量级的个人相册管理系统，专为本地照片库管理而设计。它能够自动生成文件夹和图像列表，并通过精美的灯箱界面展示你的照片。无论是在电脑还是手机上，你都可以通过按钮、滑动手势或键盘快捷键轻松浏览照片。项目采用只读模式处理原始文件，确保你的照片数据安全，同时会自动生成缩略图和压缩版本，让你在慢速网络下也能流畅浏览。

Ralbum 提供强大的搜索功能、地理位置地图展示（基于 EXIF 信息）、智能缩略图生成等特性，是管理个人照片库的理想选择。

项目地址：[点我跳转](https://github.com/ralbum/ralbum)

### 部署安装

若想在外网也能访问你的相册库，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体操作步骤可参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你随时随地都能浏览个人相册。

#### Docker 单命令快速部署

打开你的 NAS 设备或服务器终端，执行以下命令即可快速启动 Ralbum：

```bash
docker run --name ralbum_live \
        -v /var/www/testfoto:/var/data \
        -v /var/ralbum/cache/live:/var/www/html/cache \
        -v /var/ralbum/data/live:/var/www/html/data \
        -d -p 1247:80 ralbum/ralbum
```

**参数说明：**

/var/www/testfoto - 替换为你服务器上存放照片的实际目录路径

-p 1247:80 - 容器映射端口，如果 1247 端口被占用，可修改为其他端口号（冒号前面的数字），但容器内部端口 80 保持不变

-v 参数用于挂载目录：

第一个 -v 挂载你的照片库目录

第二个 -v 挂载缓存目录（可选，便于后续升级）

第三个 -v 挂载数据目录（可选，便于后续升级）

确保 Docker 容器对缓存和数据文件夹具有写入权限。

#### 启用搜索功能

如需使用搜索和仪表板功能，需要定期运行索引任务。在宿主机上执行以下命令：

```
/usr/bin/docker exec ralbum_live /var/www/html/ralbum_cron.sh
```

建议将此命令添加到系统定时任务中（如 crontab），每天运行一次以保持索引最新。

#### 访问应用

部署完成后，在浏览器中访问：

```
http://你的服务器IP:1247
```

即可打开 Ralbum 相册界面，开始浏览和管理你的个人照片库。

本篇教程结束

---

原文：<https://www.passnat.com/posts/ralbum-personal-photo-album-nas-deployment-intranet-access>
