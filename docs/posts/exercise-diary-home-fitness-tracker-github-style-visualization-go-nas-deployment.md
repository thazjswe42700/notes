---
title: "居家健身记录神器，GitHub风格可视化展示，Go语言轻量级应用，NAS一键部署Exercise Diary"
date: 2025-10-16
description: "轻量级健身日记应用，GitHub风格年度可视化展示。采用Go语言开发，支持Docker快速部署。提供灵活主题配置、多色彩方案、用户认证功能。无论是健身爱好者还是日常运动记录者，都能直观追踪锻炼数据。支持NAS部署，数据安全私密。立即开箱即用，打造个人专属运动管理平台。"
canonical: https://www.passnat.com/posts/exercise-diary-home-fitness-tracker-github-style-visualization-go-nas-deployment
---

## 项目介绍

Exercise Diary 是一款轻量级的健身日记应用，采用 GitHub 风格的年度可视化展示方式。这是一个开箱即用的健身记录工具，用 Go 语言编写，提供了简洁美观的网页界面。无论你是健身爱好者还是想要记录日常运动情况，Exercise Diary 都能帮助你以直观的方式追踪和管理你的锻炼数据。

应用支持灵活的主题配置、多种颜色方案和本地化部署，让你可以根据个人喜好定制界面。同时还支持用户认证功能，确保你的健身数据安全私密。

项目地址：[点我跳转](https://github.com/aceberg/exercisediary)

## 部署安装

如果你想在家庭网络外也能访问这个应用，建议配置内网穿透工具，这样就能随时随地查看你的运动记录了。推荐使用 [帕斯网内穿透](https://passnat.com)，先注册账号备用。详细的配置指南可以 [点我查看](https://www.passnat.com/posts/p)。

### Docker 快速部署

打开你的 NAS 设备（飞牛 NAS 或其他品牌），进入容器管理界面。

点击 **本地镜像 - 添加镜像 - 从 URL 添加**，在镜像地址栏填入：

```
aceberg/exercisediary
```

等待镜像下载完成后，点击启动按钮。

**端口配置**：容器内部运行在 8851 端口，建议直接映射为 8851:8851。如果你的 NAS 上该端口已被占用，可以修改左侧的宿主机端口号（比如改为 8852:8851），但容器内部的 8851 端口保持不变。

**存储位置**：应用会在 /data/ExerciseDiary 目录下保存所有的健身记录数据。建议在 NAS 上创建一个专用文件夹（如 /mnt/data/ExerciseDiary），然后将其映射到容器内的 /data/ExerciseDiary 路径，这样即使容器重启，你的数据也不会丢失。

**环境变量设置**：

必须设置：

TZ：时区设置（如 Asia/Shanghai、Asia/Novosibirsk 等），确保记录的时间正确

可选设置（不填则使用默认值）：

HOST：监听地址，默认 0.0.0.0

PORT：Web 界面端口，默认 8851

THEME：UI 主题，默认 grass（可选：emerald、grayscale、ocean、sand、wood 等）

COLOR：背景颜色，默认 light（可选：dark）

HEATCOLOR：热力图颜色，默认 #03a70c（绿色）

PAGESTEP：每页显示的记录条数，默认 10

AUTH：是否启用身份验证，默认 false

AUTH\_USER：用户名（启用 AUTH 时需要设置）

AUTH\_PASSWORD：加密后的密码（启用 AUTH 时需要设置，需使用 bcrypt 加密）

AUTH\_EXPIRE：会话过期时间，默认 7d（可设置为 m/h/d/M 后缀）

如果你不需要身份验证功能，其他环境变量都可以不填，直接下一步启动即可。

启动完成后，在浏览器中访问 http://你的NAS\_IP:8851，就能看到 Exercise Diary 的界面了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/exercise-diary-home-fitness-tracker-github-style-visualization-go-nas-deployment>


## 相关文章

- [PicX免费开源图床工具，GitHub图片托管管理，在线即用无需安装，支持批量上传链接转换](picx-free-open-source-image-hosting-github-batch-upload.md)
- [轻量级Markdown笔记应用Note Mark，Docker极简部署NAS，支持内网穿透远程访问笔记](note-mark-markdown-docker-nas-intranet-penetration.md)
- [自建 Gitea 私有 Git 仓库：飞牛 NAS Docker 部署自己家的 GitHub，内网穿透在外面也能开](gitea-self-hosted-private-git-server-nas-docker-deployment-intranet-penetration.md)
- [ExerciseDiary 健身打卡：NAS Docker 部署 + 内网穿透](exercisediary-nas-docker-fitness-checkin-calendar-intranet-penetration.md)
- [轻量级全文搜索引擎ZincSearch快速部署指南，NAS极简配置Docker一键启动](zinc-search-nas-docker-deployment-guide.md)
