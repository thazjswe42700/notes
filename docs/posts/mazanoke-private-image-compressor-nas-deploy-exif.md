---
title: "MAZANOKE 飞牛 NAS 部署：浏览器端图片压缩与 EXIF 抹除"
date: 2026-10-04
description: "浏览器里跑的图片压缩站：按画质或目标大小压缩、限尺寸、转格式，照片全程不离开设备。包含单容器编排与飞牛 PassNAT 插件隧道步骤。"
canonical: https://www.passnat.com/posts/mazanoke-private-image-compressor-nas-deploy-exif
---

## 项目介绍

MAZANOKE 是一个跑在浏览器里的图片压缩站，可以部署在自己的 NAS 上。照片拖进网页就能压缩：按画质压、压到指定的目标大小、限制最大宽高，JPG、PNG、WebP、ICO 之间互相转换，也能从 HEIC、AVIF、TIFF、GIF、SVG 转过来。

它的处理全部在你自己的浏览器里完成，照片不经过任何服务器；下载下来的图片默认抹掉 EXIF 信息，例如定位和拍摄时间。实测版本为 v1.1.5，界面为英文，本文按界面上的英文按钮名说明。

项目源码：[MAZANOKE](https://github.com/civilblur/mazanoke)

官方站点：[MAZANOKE.com](https://mazanoke.com)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 在外网浏览器中访问。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 mazanoke；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  mazanoke:
    container_name: mazanoke
    image: ghcr.io/civilblur/mazanoke:latest
    network_mode: bridge
    ports:
      - "3475:80"
    restart: unless-stopped
```

3475 是 NAS 对外的端口，80 是镜像里固定的容器端口，不用改。这个应用不需要数据库、不需要配置管理员，也没有任何需要填写密钥的环境变量。network\_mode 使用 bridge，与其他容器的编排互不影响。

勾选创建项目后立即启动，确认，等待镜像拉取完成后容器开始运行。

## 二、在飞牛 PassNAT 插件创建隧道

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，本地 IP 保持默认（127.0.0.1），本地端口填 3475，其余按页面默认设置创建。

按隧道这一行的电源，等待状态显示运行中。打开详情，使用分配的公网地址访问 MAZANOKE；手机在外面也打开这个地址，同一份工具随身带着走。应用本身没有账号体系，打开就能用。

## 三、压缩与转换

把照片拖进网页（或直接粘贴），右侧即出现设置：Quality 调整画质，一般拉到 75 上下；压缩方式选 Target file size 可以填一个目标大小，例如 300，它会把照片压到接近这个值；Dimensions 里填最大宽高，例如 1920，导出的图不会超过这个尺寸。

Convert to 里选 WebP、JPG、PNG 或 ICO 可以转格式。一次可以拖入多张，处理完点 Download all 一起下载。所有处理都在浏览器本地完成，断网状态下页面照样能工作。

## 数据与隐私

上传的照片只存在于当前浏览器标签页里，关闭页面即清空；下载的图片默认不带 EXIF 定位和拍摄时间。项目为 GPL-3.0 开源，编排文件可从本文直接复制。

---

原文：<https://www.passnat.com/posts/mazanoke-private-image-compressor-nas-deploy-exif>
