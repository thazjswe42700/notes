---
title: "在线图像格式转换工具Webp2jpg，支持PSD转JPG、PNG压缩、视频字幕拼接，Docker一键部署NAS"
date: 2025-10-16
description: "Webp2jpg-online是功能强大的在线图像处理工具箱，支持PSD、HEIC、JPG、PNG等20多种格式转换，提供图片压缩、水印、批量处理等功能。还包含视频字幕拼接工具，完全前端实现，无需后端，速度快隐私安全，支持20种语言，可Docker快速部署。"
canonical: https://www.passnat.com/posts/webp2jpg-online-converter-psd-png-compress-video-subtitle-docker-nas
---

## 项目介绍

Webp2jpg-online 是一款功能强大的在线图像处理工具箱，完全基于前端实现，无需后端支持，速度快、隐私安全、支持离线使用，并提供 20 种语言版本。这是一个开箱即用的解决方案，让你能够轻松处理各种图像格式转换和编辑需求。

项目主要包含两大功能模块：**多功能图像转换器**和**视频字幕拼接工具**。

**图像转换功能** 支持 PSD、PSB、HDR、HEIC、HEIF、TIFF、JPG、GIF、PNG、SVG、BMP、TGA 等多种格式的输入，并可转换为 JPG、WebP、PNG、GIF、AVIF 等格式。同时支持 JPG 压缩、PNG 压缩、SVG 压缩、图片水印、文字水印、尺寸调整、动画转换、批量处理（最多 1000 张图片）等高级功能。

**视频字幕拼接工具** 可将视频字幕截图拼接成长图，支持屏幕录制、导入 MP4 文件、快捷键截图、图片裁剪排序、竖屏模式、自定义边距和边框样式等功能。

项目地址：[点我跳转](https://github.com/renzhezhilu/webp2jpg-online)

## 部署安装

若要在外部网络访问该项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作指南可 [点击查看](https://www.passnat.com/posts/p) 内网穿透的全平台快速上手教程。

### 使用 Docker 快速部署

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可一键启动：

```bash
docker run -d -p 3000:3000 renzhezhilu/webp2jpg-online:latest
```

### 详细部署步骤

**第一步**：在飞牛 NAS 或其他 NAS 设备上，进入容器管理界面

**第二步**：选择 '本地镜像' → '添加镜像' → '从 URL 添加'

**第三步**：在镜像 URL 字段填入：renzhezhilu/webp2jpg-online:latest，等待镜像下载完成

**第四步**：点击启动按钮启动容器

**第五步**：在容器配置中勾选 '开机自动启动'，然后进入下一步

**第六步**：端口配置

容器内端口：3000

宿主机端口：3000（如果该端口被占用，可修改为其他端口，如 3001、3002 等，但容器内端口保持 3000 不变）

**第七步**：存储位置配置

此项目为纯前端应用，无需特殊的数据持久化目录配置

**第八步**：环境变量配置

此项目无需设置任何环境变量，保持默认即可

**第九步**：点击启动，等待容器运行成功

部署完成后，在浏览器中访问 http://你的设备IP:3000 即可打开应用。如果修改了端口号，请相应调整访问地址。

本篇教程结束

---

原文：<https://www.passnat.com/posts/webp2jpg-online-converter-psd-png-compress-video-subtitle-docker-nas>


## 相关文章

- [VTracer开源栅格转矢量工具Docker部署指南，无公网内网穿透远程访问高分辨率图像转SVG](vtracer-docker-deployment-guide-intranet-access-high-resolution-image-to-svg.md)
- [高效WebP图片服务器Go版，自动格式转换降低带宽，Docker一键部署加速网站，内网穿透远程访问优化方案](efficient-webp-image-server-go-auto-format-conversion-bandwidth-reduction-docker-one-click-deployment-website-acceleration-intranet-penetration-remote-access-optimization.md)
- [飞牛 NAS 部署 drawio 流程图工具：浏览器画图、保存图稿并导出 PNG](drawio-nas-flowchart-docker-guide.md)
- [Excalidraw 白板：NAS Docker 部署 + 内网穿透](excalidraw-nas-docker-whiteboard-diagrams-intranet-penetration.md)
- [RawTherapee开源原始照片处理软件，专业级RAW格式编辑工具，支持600+相机型号](rawtherapee-raw-photo-editor-open-source.md)
