---
title: "功能强大IP诊断工具MyIP Docker部署指南内网穿透远程访问配置"
date: 2025-10-16
description: "MyIP是一款功能强大的网络诊断工具箱，基于Node.js构建，提供IP检测、地理位置查询、网络速度测试、DNS泄露检测、WebRTC检测、MTR诊断、域名Whois查询等丰富功能。界面简洁易用，支持深色模式、PWA安装和多语言，支持Docker快速部署到NAS和服务器。"
canonical: https://www.passnat.com/posts/powerful-ip-diagnostic-tool-myip-docker-deployment-guide-intranet-penetration-remote-access-configuration
---

MyIP - 功能强大的IP工具箱

## 项目介绍

MyIP 是一款功能全面的网络诊断工具箱，基于 Node.js 和现代前端技术构建，提供了一站式的网络检测和信息查询服务。无论你是想了解自己的网络状况，还是需要进行深度的网络诊断，MyIP 都能满足你的需求。

这个项目涵盖了丰富的功能特性：可以检测你的本地 IP 和公网 IP（支持 IPv4 和 IPv6），查询任意 IP 地址的详细信息包括地理位置、运营商等，测试网站可访问性，进行 WebRTC 泄露检测，DNS 泄露测试，网络速度测试，全球延迟测试，MTR 诊断，DNS 解析查询，域名 Whois 查询，MAC 地址查询，浏览器指纹识别，以及网络审查检测等功能。

界面简洁易用，支持深色模式和移动优化模式，还支持 PWA 安装为桌面应用。支持多语言（英文、中文、法文），内置键盘快捷键支持。

项目地址：[点我跳转](https://github.com/jason5ng32/myip)

官方演示：<https://ipcheck.ing>

## 部署安装

如果需要在外网访问你部署的 MyIP 服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com) 服务，先完成账号注册以备后用。详细的配置教程可以参考 [内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或服务器的 Docker 管理面板。

在本地镜像管理中，选择'添加镜像'，然后选择'从 URL 添加'选项。

在镜像地址栏中填入以下镜像地址：

```
jason5ng32/myip:latest
```

```yaml
services:
  myip:
    image: jason5ng32/myip:latest
    container_name: myip
    restart: unless-stopped
    ports:
      - "3441:18966"
    environment:
      - TZ=Asia/Shanghai
      # 公网访问域名白名单：填你在穿透插件里拿到的那个地址（不带 http:// 和末尾斜杠）
      # 不填这一项，页面能打开但所有查询都会是空的
      - ALLOWED_DOMAINS=nasip.odn.cc
```

这里最后一行 ALLOWED\_DOMAINS 别省。MyIP 的后端对 /api/\* 做了 Referer 主机名白名单，默认只放行 localhost —— 你用 IP 或者一个没写进去的域名打开，页面照样能加载，但每一项查出来都是空的，控制台里是一串 403。填成你实际的公网地址（多个用英文逗号分隔）就好了。

等待镜像下载完成后，点击启动按钮启动容器。

**端口映射设置**：

容器运行需要映射端口 18966（这是 MyIP 前端服务的默认端口）。如果你的主机上 18966 端口已被占用，可以修改主机端口（冒号左侧的数字），但容器内部端口（冒号右侧）必须保持为 18966。

例如，如果要使用 8080 端口访问，可以设置为：8080:18966

**环境变量设置**（可选）：

如果需要自定义高级功能，可以设置以下环境变量：

**必须设置**：

通常情况下无需强制设置环境变量

**可选设置**：

BACKEND\_PORT: 后端服务运行端口（默认值：11966）

FRONTEND\_PORT: 前端服务运行端口（默认值：18966）

SECURITY\_RATE\_LIMIT: 限制每个 IP 每 60 分钟的请求次数（默认值：0，表示无限制）

SECURITY\_DELAY\_AFTER: 设置前 X 个请求不受速率限制，之后请求会产生延迟（默认值：0）

其他环境变量可保持默认，直接点击启动即可。

**访问应用**：

容器启动成功后，在浏览器中访问：

```
http://你的服务器IP:18966
```

（如果修改了主机端口，请使用修改后的端口号）

到此，MyIP 部署完成，你可以开始使用各项网络诊断功能了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/powerful-ip-diagnostic-tool-myip-docker-deployment-guide-intranet-penetration-remote-access-configuration>


## 相关文章

- [浏览器P2P文件传输工具FastSend，WebRTC加密传输，NAS极简Docker部署，局域网极速秒传无限制](fastend-p2p-file-transfer-webrtc-nas-docker.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
- [自建密码生成器Docker一键部署，支持离线模式安全检测泄露，NAS极简配置内网穿透远程访问](self-hosted-password-generator-docker-one-click-deploy-offline-mode-security-breach-detection-nas-simple-config-intranet-penetration-remote-access.md)
