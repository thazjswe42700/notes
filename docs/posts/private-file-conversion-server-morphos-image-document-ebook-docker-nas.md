---
title: "自建私密文件转换服务器Morphos，支持图片文档电子书格式互转，Docker一键部署NAS"
date: 2025-10-16
description: "开源自建文件转换服务器Morphos Server，采用Go语言开发，支持图片、文档、电子书等多格式相互转换。所有文件转换在本地服务器进行，完全保护隐私安全。提供Docker一键部署方案，支持NAS等设备自托管，数据永不上传第三方平台，是私密文件转换的最佳解决方案。"
canonical: https://www.passnat.com/posts/private-file-conversion-server-morphos-image-document-ebook-docker-nas
---

## 项目介绍

Morphos Server 是一款开源的自建文件转换服务器，致力于保护用户的隐私安全。在当今社会，我们经常被迫依赖第三方在线服务来进行文件格式转换，这对于包含敏感个人数据的文件来说存在严重的隐私风险。Morphos Server 通过提供自托管的私密转换解决方案，完美解决了这一问题。

项目采用 Go 语言开发，提供了友好易用的 Web 界面，支持多种文件格式的相互转换，包括图片格式（PNG、JPEG、GIF、WEBP、TIFF、BMP、AVIF）、文档格式（PDF、DOCX、XLSX、CSV）以及电子书格式（EPUB、MOBI）。无论是图片互转、图片转文档、文档转图片，还是文档与电子书之间的转换，Morphos Server 都能轻松胜任。

最重要的是，所有的文件转换都在你自己的服务器上进行，数据永远不会上传到任何第三方平台，真正实现了完整的隐私保护。

项目地址：[点我跳转](https://github.com/danvergara/morphos)

## 部署安装

如果你需要在外网也能访问这个文件转换服务，建议配置内网穿透功能。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的使用步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)，涵盖了全平台的上手指南。

### 最快速部署方案

打开你的 NAS 设备或任何支持 Docker 的服务器，使用以下单条命令即可快速启动 Morphos Server：

```bash
docker run --rm -p 8080:8080 -v /tmp:/tmp ghcr.io/danvergara/morphos-server:latest
```

执行上述命令后，Docker 会自动拉取最新的 Morphos Server 镜像并启动容器。稍等片刻，打开浏览器访问 `http://你的设备IP:8080`，你就能看到 Morphos 的 Web 界面了。

### 详细配置说明

**端口映射**

上述命令中的 `-p 8080:8080` 表示将容器内的 8080 端口映射到宿主机的 8080 端口。如果你的设备上 8080 端口已被占用，可以修改前面的端口号。例如，如果想使用 8888 端口访问，可以改为 `-p 8888:8080`，然后访问 `http://你的设备IP:8888` 即可。

**存储配置**

命令中的 `-v /tmp:/tmp` 表示将宿主机的 /tmp 目录挂载到容器内，用于存储临时转换的文件。如果你想使用其他目录，比如 NAS 上的特定文件夹，可以修改为 `-v /你的路径:/tmp`。

**环境变量配置**

Morphos Server 支持以下环境变量配置：

必须设置：

无必须环境变量

可选设置：

`MORPHOS\_PORT`：服务器监听的端口号（默认值：8080）

`MORPHOS\_UPLOAD\_PATH`：临时文件存储路径（默认值：/tmp）

如果需要自定义这些参数，可以在命令中添加 `-e` 参数，例如：

```bash
docker run --rm -p 8080:8080 -v /tmp:/tmp -e MORPHOS_PORT=9090 ghcr.io/danvergara/morphos-server:latest
```

**访问应用**

部署完成后，在浏览器中输入 `http://你的设备IP:8080`，你就能看到 Morphos 的转换界面。上传你需要转换的文件，选择目标格式，点击转换按钮，稍等片刻就能下载转换后的文件了。

本篇教程到此结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/private-file-conversion-server-morphos-image-document-ebook-docker-nas>


## 相关文章

- [私有社区服务器Circled.me极简部署指南，NAS一键启动无需公网穿透](circled-me-private-community-server-simple-deployment-guide-nas-one-click-startup-no-public-network-penetration.md)
- [code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码](codeserver.md)
- [跨平台音乐服务器Lyrion Music Server部署指南，NAS音乐流媒体服务器快速安装教程](lyrion-music-server-nas-deployment-guide.md)
- [自建私人音乐库服务器，Auddly Music Server 极简部署，内网穿透远程访问享受高品质音乐](self-hosted-private-music-server-auddly-music-server-minimal-deployment-internal-network-penetration-remote-access-high-quality-music.md)
- [高效WebP图片服务器Go版，自动格式转换降低带宽，Docker一键部署加速网站，内网穿透远程访问优化方案](efficient-webp-image-server-go-auto-format-conversion-bandwidth-reduction-docker-one-click-deployment-website-acceleration-intranet-penetration-remote-access-optimization.md)
