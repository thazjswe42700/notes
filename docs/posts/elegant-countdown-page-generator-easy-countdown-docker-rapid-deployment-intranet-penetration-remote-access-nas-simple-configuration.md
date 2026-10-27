---
title: "精美倒计时页面生成工具Easy Countdown，Docker快速部署，内网穿透远程访问，NAS极简配置"
date: 2025-10-16
description: "Easy Countdown是简洁易用的倒计时页面生成工具，支持倒计时和正计时模式，适用于活动、纪念日、项目截止等场景。基于现代技术栈构建，开箱即用无需复杂配置，只需设置目标日期、背景图片和标题即可生成精美页面。支持Docker快速部署，完美适配NAS设备和各类服务器环境，轻松搭建专业倒计时展示。"
canonical: https://www.passnat.com/posts/elegant-countdown-page-generator-easy-countdown-docker-rapid-deployment-intranet-penetration-remote-access-nas-simple-configuration
---

## 项目介绍

Easy Countdown 是一款简洁易用的倒计时页面生成工具。它不仅支持传统的倒计时模式，还可以灵活切换为正计时模式，完全满足各类活动、纪念日、项目截止日期等多种场景的需求。项目基于现代化技术栈构建，开箱即用，无需复杂配置，只需简单设置目标日期、背景图片和标题，就能生成一个精美的倒计时页面。无论是用于品牌宣传、活动预告、还是个人纪念，都能轻松胜任。

项目地址：[点我跳转](https://github.com/Yooooomi/easy-countdown)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务，推荐使用 [帕斯内网穿透](https://passnat.com)。先完成账号注册并备好相关配置。详细操作指南可以 [点击这里查看](https://www.passnat.com/posts/p)内网穿透全平台使用教程。

### 使用 Docker 快速部署

打开您的 NAS 设备或 Docker 管理面板，进入本地镜像管理界面，选择从 URL 添加镜像。

在镜像地址栏填入：yooooomi/easy-countdown:latest，等待镜像下载完成。

下载完成后点击启动按钮启动容器。

**端口配置**：建议将容器内部端口 3000 映射到主机的 3000 端口。如遇端口占用冲突，可修改主机端口号（如改为 3001、3002 等），但容器内部端口需保持为 3000。

**环境变量设置**：

必须设置：

TIMER\_TARGET：倒计时的目标日期时间（格式：Fri Oct 01 2021 15:33:36 GMT+0200，可通过 [这个工具](https://esqsoft.com/javascript_examples/date-to-epoch.htm) 生成）

可选设置：

TIMER\_BACKGROUND：背景图片的 URL 地址（示例：https://wallpaperplay.com/walls/full/0/7/6/29912.jpg）

TIMER\_TITLE：倒计时页面的标题文字（可为空）

其他环境变量无需配置，直接启动即可。

创建成功后，在浏览器中访问 http://你的IP:3000，即可看到精美的倒计时页面。

### 使用 Docker Compose 部署

如您更习惯使用 Docker Compose，可复制以下配置文件：

```yaml
version: '3.8'

services:
  web:
    stdin_open: true
    image: yooooomi/easy-countdown
    environment:
      TIMER_BACKGROUND: https://wallpaperplay.com/walls/full/0/7/6/29912.jpg
      TIMER_TARGET: 'Fri Oct 01 2021 15:33:36 GMT+0200'
      TIMER_TITLE: '我的倒计时'
    ports:
      - '3000:3000'
```

将上述内容保存为 docker-compose.yml 文件，根据您的实际需求修改环境变量值。其中 TIMER\_TARGET 是必须项，其他两项可根据需要调整或留空。

在终端中执行以下命令启动服务：

```
docker-compose up -d
```

启动完成后，访问 http://你的IP:3000 即可使用倒计时页面。

## 总结

Easy Countdown 提供了极简的部署体验，无论是 Docker 单命令启动还是 Compose 编排部署，都能在几分钟内快速上线。只需配置好目标日期和背景图片，就能拥有一个专业美观的倒计时页面。非常适合技术小白用户快速搭建个人或商业用途的倒计时展示。

本篇教程结束

---

原文：<https://www.passnat.com/posts/elegant-countdown-page-generator-easy-countdown-docker-rapid-deployment-intranet-penetration-remote-access-nas-simple-configuration>
