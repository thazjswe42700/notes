---
title: "高效WebP图片服务器Go版，自动格式转换降低带宽，Docker一键部署加速网站，内网穿透远程访问优化方案"
date: 2025-10-16
description: "WebP Server Go 是基于 Golang 开发的高效图片优化服务器，支持实时转换 JPEG、PNG、GIF 等格式为 WebP 和 AVIF。无需修改 URL，自动返回优化版本，显著降低带宽消耗、加速网站加载。本文详解 Docker Compose 快速部署方法、目录配置、环境变量设置及内网穿透集成方案，助力网站性能优化。"
canonical: https://www.passnat.com/posts/efficient-webp-image-server-go-auto-format-conversion-bandwidth-reduction-docker-one-click-deployment-website-acceleration-intranet-penetration-remote-access-optimization
---

## 项目介绍

WebP Server Go 是一款基于 Golang 开发的高效图片优化服务器，能够实时将多种图片格式转换为 WebP 和 AVIF 等现代高效格式。无需修改网站 URL，访问原始图片地址时会自动返回优化后的版本，有效降低带宽消耗，加速网站加载速度。

项目支持 JPEG、PNG、BMP、GIF、SVG、HEIC、NEF、WEBP 等多种常见图片格式。例如当你访问 https://your.website/pics/photo.jpg 时，服务器会自动将其转换为 image/webp 或 image/avif 格式返回，同时保持 URL 不变。这对于想要优化网站性能、降低图片传输大小的网站管理员来说是个不错的选择。

项目地址：[点我跳转](https://github.com/webp-sh/webp_server_go)

## 部署安装

如果你想在局域网外也能访问这个服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可以参考 [帕斯全平台快速入门指南](https://www.passnat.com/posts/p)。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，项目官方强烈建议使用 Docker 运行，可以避免直接运行二进制文件时可能出现的 glibc 和依赖库兼容性问题。

**第一步：准备配置文件**

复制下列的配置文件，保存为 docker-compose.yml：

```yaml
version: '3'

services:
  webp:
    image: webpsh/webp-server-go
    restart: always
    volumes:
      - ./pics:/opt/pics
      - ./exhaust:/opt/exhaust
      - ./metadata:/opt/metadata
    ports:
      - 127.0.0.1:3333:3333
```

**第二步：启动容器**

在 NAS 设备或 Linux 服务器上，将上述配置文件保存到一个目录中，然后执行以下命令启动服务：

```
docker-compose up -d
```

**第三步：配置说明**

pics 目录：这是你存放原始图片的地方。将 ./pics 改为你实际的图片存储路径，例如 /var/www/images

exhaust 目录：用于缓存转换后的 WebP/AVIF 图片，保持默认即可

metadata 目录：存储图片元数据的缓存文件夹，保持默认即可

端口映射：默认使用 3333 端口。如果该端口已被占用，可以修改为其他端口，格式为 127.0.0.1:你的端口:3333（冒号后的 3333 是容器内部端口，不要改动）

**第四步：访问服务**

部署完成后，访问 http://127.0.0.1:3333/your-image.jpg 即可看到优化后的图片。如果需要在外网访问，可以配合 Nginx 反向代理或上面提到的内网穿透工具使用。

### 环境变量设置

WebP Server Go 支持通过 config.json 进行高级配置。如果你需要自定义配置（如启用 AVIF 支持、调整压缩质量等），可以参考 [官方配置文档](https://docs.webp.sh/usage/configuration/) 生成配置文件，然后在 docker-compose.yml 中添加以下行：

```yaml
volumes:
  - ./config.json:/etc/config.json
```

对于大多数用户，使用默认配置即可满足需求。

## 总结

通过 WebP Server Go，你可以轻松为网站添加图片自动优化功能，无需修改任何代码，就能显著提升网站加载速度和用户体验。使用 Docker Compose 部署只需几分钟，是建立高效图片服务的最佳选择。

本篇教程到此结束，祝你部署顺利！

---

原文：<https://www.passnat.com/posts/efficient-webp-image-server-go-auto-format-conversion-bandwidth-reduction-docker-one-click-deployment-website-acceleration-intranet-penetration-remote-access-optimization>


## 相关文章

- [RawTherapee开源原始照片处理软件，专业级RAW格式编辑工具，支持600+相机型号](rawtherapee-raw-photo-editor-open-source.md)
- [自建私密文件转换服务器Morphos，支持图片文档电子书格式互转，Docker一键部署NAS](private-file-conversion-server-morphos-image-document-ebook-docker-nas.md)
- [在线图像格式转换工具Webp2jpg，支持PSD转JPG、PNG压缩、视频字幕拼接，Docker一键部署NAS](webp2jpg-online-converter-psd-png-compress-video-subtitle-docker-nas.md)
- [私有社区服务器Circled.me极简部署指南，NAS一键启动无需公网穿透](circled-me-private-community-server-simple-deployment-guide-nas-one-click-startup-no-public-network-penetration.md)
- [Plik私有文件分享系统NAS部署指南，Docker快速搭建临时上传平台](plik-nas-docker-deployment-guide.md)
