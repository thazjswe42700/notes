---
title: "自建文件服务器 GoHTTPServer Docker极简部署，NAS内网穿透远程访问文件"
date: 2025-10-16
description: "GoHTTPServer是用Go语言开发的功能强大HTTP文件服务器，提供简洁友好的Web界面。支持文件上传下载删除、二维码生成、APK/IPA快速安装、多种认证方式及Nginx反向代理。开箱即用，特别适合NAS设备部署，可实现内网文件共享和管理。支持Docker一键启动，安全便捷。"
canonical: https://www.passnat.com/posts/self-hosted-file-server-gohttpserver-docker-deployment-nas-intranet-penetration-remote-access
---

## 项目介绍

GoHTTPServer 是一个功能强大的 HTTP 文件服务器，采用 Go 语言开发，提供了一个简洁而人性化的网页界面。无论你是想要快速共享文件、搭建内部文件库，还是需要一个支持上传下载的文件管理系统，GoHTTPServer 都能轻松胜任。

项目具有开箱即用的特性，内置了精美的 Web UI，支持文件上传、下载、删除、二维码生成等多项实用功能。特别是对于 APK 和 IPA 安装包，它可以直接生成二维码，让用户通过手机扫描即可快速安装。支持多种认证方式（HTTP Basic Auth、OpenID、OAuth2），满足不同的安全需求。项目还支持在 Nginx 反向代理后运行，非常适合在企业内网或 NAS 设备上部署。

项目地址：[点我跳转](https://github.com/codeskyblue/gohttpserver)

## 部署安装

如果你需要在外网访问部署在家里的 GoHTTPServer，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册。具体的操作指南可以参考 [这篇详细教程](https://www.passnat.com/posts/p)，涵盖全平台的快速上手步骤。

### 最快速部署方案

打开你的 NAS 设备（飞牛 NAS、群晖或其他支持 Docker 的 NAS），使用以下 Docker 单命令快速启动 GoHTTPServer：

```bash
docker run -it --rm -p 8000:8000 -v $PWD:/app/public --name gohttpserver codeskyblue/gohttpserver
```

这个命令会：

将本地当前目录映射到容器内的 `/app/public` 目录，作为文件服务的根目录

将容器内的 8000 端口映射到主机的 8000 端口

启动 GoHTTPServer 服务

**端口说明**：如果你的 NAS 上 8000 端口已被占用，可以修改命令中的第一个 `8000` 为其他端口号（如 `8001`、`8002` 等），格式为 `-p 新端口:8000`。容器内部始终使用 8000 端口，只需修改宿主机映射的端口即可。

**文件存储**：映射的 `/app/public` 目录是 GoHTTPServer 用来存放和分享文件的位置。你可以根据实际需求修改 `$PWD` 为你想要共享的目录路径。

### 启用文件上传功能

如果你希望通过 Web 界面上传文件，可以在启动命令后添加 `--upload` 参数：

```bash
docker run -it --rm -p 8000:8000 -v $PWD:/app/public --name gohttpserver codeskyblue/gohttpserver --upload
```

### 启用认证保护

为了保护你的文件安全，可以添加 HTTP Basic Auth 认证。使用以下命令启动：

```bash
docker run -it --rm -p 8000:8000 -v $PWD:/app/public --name gohttpserver \
  codeskyblue/gohttpserver \
  --auth-type http --auth-http username:password --upload
```

将 `username` 和 `password` 替换为你自己的用户名和密码。如需添加多个用户，可以继续添加 `--auth-http user2:pass2` 参数。

### 启用文件删除功能

如果需要通过 Web 界面删除和创建文件夹，添加 `--delete` 参数：

```bash
docker run -it --rm -p 8000:8000 -v $PWD:/app/public --name gohttpserver codeskyblue/gohttpserver --upload --delete
```

### 访问你的文件服务

部署完成后，在浏览器中访问：

```
http://你的NAS_IP:8000
```

将 `你的NAS\_IP` 替换为你的 NAS 设备实际 IP 地址。如果修改了端口号，相应地在 URL 中更改端口号即可。

此时你就能看到一个功能完整、界面友好的文件管理界面，可以浏览、下载、上传文件，还能生成二维码供他人快速访问。

---

原文：<https://www.passnat.com/posts/self-hosted-file-server-gohttpserver-docker-deployment-nas-intranet-penetration-remote-access>
