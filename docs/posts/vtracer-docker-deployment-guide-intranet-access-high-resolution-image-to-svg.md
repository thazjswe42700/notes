---
title: "VTracer开源栅格转矢量工具Docker部署指南，无公网内网穿透远程访问高分辨率图像转SVG"
date: 2025-10-16
description: "VTracer是开源栅格转矢量图形工具，支持JPG、PNG等格式转SVG。采用Rust+WebAssembly技术，提供命令行和网页应用。相比Potrace和Adobe Illustrator，输出更紧凑高效。可处理彩色扫描、蓝图和像素艺术，完整的图像处理管道。Docker一键部署，支持参数自定义调整。"
canonical: https://www.passnat.com/posts/vtracer-docker-deployment-guide-intranet-access-high-resolution-image-to-svg
---

## 项目介绍

VTracer 是一款强大的开源栅格转矢量图形转换工具，由视觉皮层研究小组精心打造。它能够将 JPG、PNG 等光栅图像智能转换为 SVG 矢量图形，支持彩色高分辨率扫描、历史蓝图处理，甚至可以处理低分辨率像素艺术。

相比传统的 Potrace（仅支持黑白二值图像），VTracer 拥有完整的图像处理管道，能够处理彩色图像。与 Adobe Illustrator 的 Image Trace 相比，VTracer 输出的矢量文件更加紧凑，包含的形状更少。VTracer 采用了堆叠策略，避免生成带孔洞的形状，使输出更加优雅。

项目采用 Rust + WebAssembly 技术构建，提供了高效的命令行工具和网页应用，让你可以轻松将任何光栅图像转换为专业的矢量图形。无论是处理吉比字节级的高分辨率扫描，还是复古像素艺术，VTracer 都能胜任。

项目地址： [点我跳转](https://github.com/visioncortex/vtracer)

## 部署安装

如果你需要在外网也能访问本地部署的 VTracer，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的配置教程请 [点我查看](https://www.passnat.com/posts/p)，全平台都有详细的上手指南。

### 快速部署（Docker 方式）

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可一键启动 VTracer：

```bash
docker run -d -p 8080:8080 visioncortex/vtracer:latest
```

等待镜像下载完成后，容器会自动启动。

### 详细配置说明

**端口映射设置**

上述命令中的 `8080:8080` 表示将容器内部的 8080 端口映射到你的主机。如果你的主机上 8080 端口已被占用，可以修改前面的数字。例如修改为 `8888:8080`，这样就可以通过 `http://你的IP:8888` 访问 VTracer。

**访问应用**

部署完成后，在浏览器中访问：

```
http://你的IP地址:8080
```

如果是本地访问，直接输入 `http://localhost:8080` 即可。

**环境变量设置**

VTracer 的 Docker 镜像已经预配置好了所有必要的参数，通常无需额外设置环境变量。如果需要自定义配置，可以根据实际需求进行调整。

### 使用说明

VTracer 网页应用提供了直观的用户界面：

上传你想要转换的图片（支持 JPG、PNG 等格式）

根据需要调整转换参数：

色彩模式：选择 'color'（彩色）或 'bw'（黑白）

角度阈值：调整识别角落的灵敏度

拟合模式：选择 'pixel'、'polygon' 或 'spline'

色彩精度：控制输出的色彩细节程度

点击转换按钮，稍等片刻即可获得 SVG 矢量文件

下载转换后的矢量图形文件

### 命令行工具使用

如果你更喜欢命令行操作，也可以在容器内使用 VTracer 的 CLI 工具：

```bash
docker run --rm -v /your/image/path:/data visioncortex/vtracer:latest vtracer --input /data/input.jpg --output /data/output.svg
```

将 `/your/image/path` 替换为你的本地图片目录路径。

本篇教程结束。VTracer 是一款功能强大且易于使用的图像矢量化工具，无论你是设计师、开发者还是内容创作者，都能从中受益。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/vtracer-docker-deployment-guide-intranet-access-high-resolution-image-to-svg>
