---
title: "自动签到脚本DailyCheckIn Docker部署指南，支持NAS群晖青龙面板内网穿透远程管理"
date: 2025-10-16
description: "DailyCheckIn是一款基于Python开发的自动化签到脚本工具，支持Docker、青龙面板、群晖NAS等多平台部署。集成20+个热门网站应用的自动签到功能，包括B站、爱奇艺、阿里云盘等，一键部署后系统自动完成每日签到任务，支持钉钉、企业微信、Telegram等多种通知方式，帮助用户轻松获取积分和各类奖励。"
canonical: https://www.passnat.com/posts/auto-checkin-script-dailycheckin-docker-deployment-nas-synology-qinglong-panel-intranet-penetration-remote-management
---

## 项目介绍

DailyCheckIn 是一款功能强大的自动化签到脚本工具，专为互联网用户量身打造。它基于 Python 开发，支持在 Docker、青龙面板、群晖 NAS、本地等多个平台灵活部署。无论您是技术爱好者还是普通用户，都能轻松上手。

该项目集成了超过 20+ 个主流网站和应用的自动签到功能，包括全民 K 歌、有道云笔记、百度贴吧、B站、爱奇艺、阿里云盘等热门平台。一键部署后，系统会在指定时间自动为您完成每日签到任务，帮您轻松获取积分、会员时长、存储空间等各类奖励。同时支持钉钉、企业微信、Telegram、微信等多种通知方式，让您实时掌握签到动态。

项目地址：[点我跳转](https://github.com/sitoi/dailycheckin)

## 部署安装

如果您需要在外网也能访问该应用，建议配置内网穿透服务。可前往 [帕斯内网穿透](https://passnat.com) 注册账号，按照 [全平台快速上手指南](https://www.passnat.com/posts/p) 进行配置，即可随时随地管理您的签到任务。

**使用 Docker 一键部署**

打开您的 NAS 设备或本地 Docker 环境，执行以下命令快速启动 DailyCheckIn：

```bash
docker run -d \
  --name dailycheckin \
  -e TZ=Asia/Shanghai \
  -v /root/dailycheckin:/app/config \
  -p 5000:5000 \
  sitoi/dailycheckin:latest
```

**参数说明：**

--name dailycheckin - 容器名称

-e TZ=Asia/Shanghai - 设置时区为中国时间

-v /root/dailycheckin:/app/config - 配置文件持久化存储目录，请根据您的 NAS 实际情况修改前面的路径

-p 5000:5000 - 端口映射，左侧为外部访问端口，如遇端口冲突可修改为其他端口（如 5001:5000），右侧 5000 为容器内部端口，保持不变

**环境变量设置（可选）：**

变量名: TZ | 说明: 时区设置 | 默认值: Asia/Shanghai

变量名: ENABLE\_NOTIFY | 说明: 是否启用通知功能 | 默认值: true

部署完成后，在浏览器中访问 http://你的IP:5000 即可打开 DailyCheckIn 管理面板。首次使用时，您可以在面板中添加各平台的登录信息，系统会自动在设定的时间执行签到任务。

## 功能特性

✨ 多平台支持 - 支持 20+ 个热门网站和应用的自动签到

💬 智能通知 - 集成钉钉、企业微信、Telegram、微信等多种通知渠道

🔄 定时执行 - 灵活设置签到时间，自动定时运行

📱 多账号管理 - 支持同时管理多个账号的签到任务

🚀 项目自动更新 - 脚本自动更新，无需手动干预

本篇教程结束

---

原文：<https://www.passnat.com/posts/auto-checkin-script-dailycheckin-docker-deployment-nas-synology-qinglong-panel-intranet-penetration-remote-management>
