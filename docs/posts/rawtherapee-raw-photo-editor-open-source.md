---
title: "RawTherapee开源原始照片处理软件，专业级RAW格式编辑工具，支持600+相机型号"
date: 2025-10-16
description: "RawTherapee是功能强大的跨平台开源原始照片处理程序，支持600+相机格式。提供专业级去马赛克算法、色彩平衡、曲线编辑、局部编辑等丰富工具，支持RAW、DNG、JPEG、TIFF、PNG等多种格式。适合摄影爱好者和专业摄影师进行精细图像处理和创意编辑。"
canonical: https://www.passnat.com/posts/rawtherapee-raw-photo-editor-open-source
---

## 项目介绍

RawTherapee 是一款功能强大的跨平台原始照片处理程序，以 GNU General Public License Version 3 开源协议发布。它采用 C++ 编写，搭配 GTK+ 前端界面，为摄影爱好者和专业摄影师提供了专业级的原始文件处理能力。

这款软件支持来自各大相机品牌的原始格式文件，同时兼容 HDR DNG、JPEG、TIFF 和 PNG 等多种图像格式。RawTherapee 以其卓越的去马赛克控制和开发流程灵活性而闻名，让用户能够对图像处理的每一个环节进行精细调整，从而获得最高质量的处理结果。

无论你是初学摄影的爱好者，还是想深入了解数字成像工作原理的学习者，亦或是专业摄影师，RawTherapee 都能为你提供完整的创意自由度和强大的处理工具。

项目地址：[点我跳转](https://github.com/Beep6581/RawTherapee)

官方网站：[RawTherapee.com](https://www.rawtherapee.com/)

## 部署安装

如果需要在外网访问你部署的应用，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号做好准备。详细的操作指南可以参考 [这份全平台上手攻略](https://www.passnat.com/posts/p)，帮助你快速掌握内网穿透的使用方法。

### 快速部署方式

**注意：** RawTherapee 是一个本地应用程序，主要用于离线图片处理。官方项目暂未提供官方的 Docker 镜像或 docker-compose 配置。

建议直接从官方渠道下载使用：

**Windows 用户：**

1. 访问 [RawTherapee 官方下载页面](https://www.rawtherapee.com/downloads)
2. 下载 Windows 版本安装程序
3. 运行安装程序，按照提示完成安装即可

**Linux 用户：**

1. 打开终端
2. 根据你的发行版，使用对应的包管理器安装：

```bash
# Ubuntu/Debian
sudo apt-get install rawtherapee

# Fedora
sudo dnf install RawTherapee

# Arch Linux
sudo pacman -S rawtherapee
```

**macOS 用户：**

1. 访问 [官方下载页面](https://www.rawtherapee.com/downloads)
2. 下载 macOS 版本
3. 拖拽应用到 Applications 文件夹即可

### 从源代码编译（高级用户）

如果你需要最新的开发版本或想自己编译，可以参考官方编译指南：

- **Linux 编译指南：**<https://rawpedia.rawtherapee.com/Linux>

- **Windows 编译指南：**<https://rawpedia.rawtherapee.com/Windows>

- **macOS 编译指南：**<https://rawpedia.rawtherapee.com/macOS>

详细的依赖项和编译步骤请查阅 [RawPedia 官方文档](https://rawpedia.rawtherapee.com/)。

## 功能特性

✨ **专业级图像处理**

- 支持超过 600 种相机型号的原始文件格式
- 高精度的去马赛克算法
- 完全可控的图像开发流程

🎨 **丰富的编辑工具**

- 色彩平衡与白平衡调整
- 曲线和直方图编辑
- 局部编辑和蒙版功能
- 降噪和锐化处理

📁 **多格式支持**

- 原始格式（RAW）
- HDR DNG 文件
- JPEG、TIFF、PNG 等标准格式

📚 **完整的学习资源**

- 详细的 [RawPedia 文档](https://rawpedia.rawtherapee.com/)
- 活跃的 [用户论坛](https://discuss.pixls.us/c/software/rawtherapee)
- 丰富的功能说明

## 总结

RawTherapee 是摄影爱好者和专业人士的理想选择，提供了强大而灵活的原始文件处理能力。无论你是初学者还是专家，都能在这款开源软件中找到适合自己的工具和功能。立即下载开始你的摄影创意之旅吧！

---

原文：<https://www.passnat.com/posts/rawtherapee-raw-photo-editor-open-source>


## 相关文章

- [高效WebP图片服务器Go版，自动格式转换降低带宽，Docker一键部署加速网站，内网穿透远程访问优化方案](efficient-webp-image-server-go-auto-format-conversion-bandwidth-reduction-docker-one-click-deployment-website-acceleration-intranet-penetration-remote-access-optimization.md)
- [自建相册管理系统Photoview，NAS极简部署内网穿透远程访问，摄影师RAW格式完美支持](photoview-nas---raw.md)
- [Excalidraw 白板：NAS Docker 部署 + 内网穿透](excalidraw-nas-docker-whiteboard-diagrams-intranet-penetration.md)
- [飞牛 NAS 部署 drawio 流程图工具：浏览器画图、保存图稿并导出 PNG](drawio-nas-flowchart-docker-guide.md)
- [VTracer开源栅格转矢量工具Docker部署指南，无公网内网穿透远程访问高分辨率图像转SVG](vtracer-docker-deployment-guide-intranet-access-high-resolution-image-to-svg.md)
