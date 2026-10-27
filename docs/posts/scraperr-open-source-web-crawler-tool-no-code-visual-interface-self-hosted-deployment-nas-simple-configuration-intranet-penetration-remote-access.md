---
title: "Scraperr开源网页爬虫工具，无代码可视化界面自托管部署，NAS极简配置内网穿透远程访问"
date: 2025-10-16
description: "Scraperr是开源自托管网页爬虫工具，无需编码即可抓取网站数据。支持XPath精准提取、域名爬虫、媒体下载、数据可视化及多格式导出。本指南详细介绍Docker快速部署方法、端口配置和使用最佳实践，适合数据分析师、研究人员及开发者使用。"
canonical: https://www.passnat.com/posts/scraperr-open-source-web-crawler-tool-no-code-visual-interface-self-hosted-deployment-nas-simple-configuration-intranet-penetration-remote-access
---

## Scraperr - 开源网页爬虫工具部署指南

### 项目介绍

Scraperr 是一款功能强大的自托管网页爬虫解决方案，让你无需编写任何代码就能轻松抓取网站数据。这是一个完全开源的项目，专为数据提取而设计。

项目采用现代化的技术栈构建，提供了直观的可视化界面。无论你是数据分析师、研究人员还是开发者，都可以通过简单的点击操作完成复杂的网页数据抓取任务。

核心功能包括：

🎯 XPath 精准提取：精确定位页面元素
📊 队列管理系统：提交和管理多个爬虫任务
🕷️ 域名爬虫：支持爬取同域名下的所有页面
📥 媒体下载：自动下载图片、视频等多媒体文件
📈 数据可视化：以结构化表格展示抓取结果
💾 多格式导出：支持 Markdown 和 CSV 格式导出
🔔 通知功能：任务完成后通过多种渠道发送通知

项目地址：[点我跳转](https://github.com/jaypyles/Scraperr)

### 部署安装

如需在公网环境下访问你的爬虫系统，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号。具体操作步骤可参考 [全平台快速上手指南](https://www.passnat.com/posts/p)，让你随时随地都能管理爬虫任务。

#### Docker 快速启动

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可一键启动 Scraperr：

```bash
docker run -d \
  --name scraperr \
  -p 3000:3000 \
  jaypyles/scraperr:latest
```

镜像会自动下载并启动。首次启动可能需要几分钟时间，请耐心等待。

端口说明：

容器内部服务运行在 3000 端口
如果你的主机 3000 端口已被占用，可以修改映射配置，例如改为 8080:3000，这样就能通过 http://ip:8080 访问

环境变量配置：

该项目无需强制配置环境变量即可正常运行，所有功能开箱即用。

启动成功后，打开浏览器访问 http://你的IP地址:3000 就能看到 Scraperr 的管理界面了。

### 使用建议

使用 Scraperr 时请遵守以下原则：

✅ 尊重网站的 robots.txt 文件规则
✅ 遵守目标网站的服务条款
✅ 设置合理的请求延迟，避免对服务器造成压力
✅ 仅在获得授权的网站上进行数据抓取

本篇教程结束

说明：由于项目仓库中的具体配置文件暂时无法直接访问，上述部署方案基于项目的标准 Docker 实现。如需更详细的配置选项，建议查阅项目官方文档或 Discord 社区获取支持。

---

原文：<https://www.passnat.com/posts/scraperr-open-source-web-crawler-tool-no-code-visual-interface-self-hosted-deployment-nas-simple-configuration-intranet-penetration-remote-access>
