---
title: "自动聚合优质IPTV直播源，支持NAS极简部署与内网穿透远程访问"
date: 2025-10-16
description: "IPTV-Sources是功能强大的IPTV直播源聚合管理工具,自动整合iptv.org、fanmingming/live等权威资源。支持自动源更新、代理加速、直播源校验和EPG电子节目单,采用Node.js构建。无需手动维护,内置自定义回源功能,轻松获取国内外优质稳定直播源,打造完整直播生态系统。"
canonical: https://www.passnat.com/posts/auto-aggregate-quality-iptv-live-sources-nas-simple-deployment-intranet-penetration-remote-access
---

## 项目介绍

IPTV-Sources 是一个功能强大的 IPTV 直播源聚合管理工具。它能够自动化更新和整合来自多个知名开源项目的 IPTV 直播源，包括 iptv.org、fanmingming/live、YanG-1989/m3u 等权威资源库。项目采用 Node.js 构建，支持自动源更新、代理加速、直播源校验等功能，让你可以轻松获取稳定可靠的直播源资源。

无需手动维护，自动化聚合国内外优质直播源。支持自定义回源、GitHub 代理加速等高级功能，同时内置 EPG 电子节目单支持，为你打造一个完整的直播生态系统。

项目地址：[点我跳转](https://github.com/HerbertHe/iptv-sources)

## 部署安装

如果你需要在外网也能访问这个项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可以参考 [帕斯内网穿透全平台快速上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 最快速部署方式（Docker 单命令）

打开你的 NAS 设备或任何支持 Docker 的系统，执行以下命令即可快速启动：

```bash
docker run -d \
  --name iptv-sources \
  -p 8080:8080 \
  herberthe0229/iptv-sources:latest
```

**命令说明：**

`-d` 后台运行容器
`--name iptv-sources` 容器名称
`-p 8080:8080` 端口映射，本地 8080 端口映射到容器内 8080 端口（如果 8080 端口被占用，可改为其他端口，如 `-p 8081:8080`）
`herberthe0229/iptv-sources:latest` Docker 镜像

等待镜像下载完成后，容器会自动启动。

### 环境变量配置（可选）

如果需要高级功能配置，可在启动时添加环境变量：

**选择设置，可以不填：**

`ROLLBACK\_URLS` - 自定义回源地址（默认为空）
`CLOSE\_SOURCE\_PROXY` - 关闭源代理功能（默认 false）
`CUSTOM\_GITHUB\_RAW\_SOURCE\_PROXY\_URL` - 自定义 GitHub 代理地址（如 `https://ghp.ci/`）
`ENABLE\_IPTV\_CHECKER` - 启用直播源校验功能（默认 false）
`IPTV\_CHECKER\_URL` - 直播源校验服务地址（默认为空）

**使用示例：**

```bash
docker run -d \
  --name iptv-sources \
  -p 8080:8080 \
  -e ENABLE_IPTV_CHECKER=true \
  -e CUSTOM_GITHUB_RAW_SOURCE_PROXY_URL=https://ghp.ci/ \
  herberthe0229/iptv-sources:latest
```

其他默认配置无需修改，直接启动即可。

### 访问项目

容器启动成功后，在浏览器中访问：

```
http://你的NAS_IP:8080
```

即可看到 IPTV-Sources 的管理界面，开始享受自动化的直播源管理体验。

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/auto-aggregate-quality-iptv-live-sources-nas-simple-deployment-intranet-penetration-remote-access>
