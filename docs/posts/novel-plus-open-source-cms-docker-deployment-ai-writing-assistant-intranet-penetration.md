---
title: "Novel-Plus 开源小说CMS系统 Docker一键部署 AI写作助手 内网穿透远程访问"
date: 2025-10-16
description: "Novel-Plus是功能完善的原创文学内容管理系统，采用Spring Boot 3、MyBatis、Spring AI等现代技术栈构建。支持PC端和移动端双端阅读，集成AI写作助手、全文搜索、排行榜、会员订阅等完整功能。支持自定义模板、灵活存储方案、多源爬虫采集，是搭建个人文学网站的理想选择。"
canonical: https://www.passnat.com/posts/novel-plus-open-source-cms-docker-deployment-ai-writing-assistant-intranet-penetration
---

## 项目介绍

Novel-Plus 是一个功能完善、开箱即用的原创文学内容管理系统（CMS）。它采用 Spring Boot 3 + MyBatis + Spring AI 等现代化技术栈构建，支持 PC 端和移动端（WAP）双端阅读体验。

这个项目集前台门户系统、作家创作后台、平台管理后台和智能爬虫系统于一身，提供了小说推荐、全文搜索、排行榜、在线阅读、评论互动、会员中心、作家专区、充值订阅等完整功能。最大的亮点是内置了 AI 写作助手，支持 AI 扩写、缩写、续写、文本润色以及 AI 生成封面图，让创作变得更加智能高效。

同时，系统支持自定义多套前端模板、灵活的小说内容存储方案（数据库分表或 TXT 文本）、阅读主题切换、多源爬虫自动采集更新、完整的会员订阅模式和数据统计报表，是搭建个人文学网站或内容平台的理想选择。

项目地址：[点我跳转](https://github.com/201206030/novel-plus)

## 部署安装

如果你想在外网也能访问这个项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体的配置步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)，涵盖了全平台的快速上手指南。

### Docker 快速部署

Novel-Plus 提供了开箱即用的 Docker 镜像，只需一条命令即可快速启动：

打开你的 NAS 设备（飞牛 NAS、群晖等）或 Linux 服务器的 Docker 管理面板。

在本地镜像管理中，选择'添加镜像'，然后选择'从 URL 添加'。

镜像地址填写：201206030/novel-plus:latest

等待镜像下载完成后，点击启动按钮创建容器。

**容器配置说明：**

勾选'开机自动启动'以确保容器在系统重启后自动运行。

**端口映射配置：**

容器内部运行在 8888 端口。建议将其映射到宿主机的 8888 端口（如果 8888 端口已被占用，可改为其他端口如 8889、8890 等，但容器内端口保持 8888 不变）。

映射格式示例：8888:8888 或 8889:8888

**存储卷配置：**

建议创建一个本地文件夹用于存储应用数据和配置文件，例如在 NAS 上创建 /mnt/novel-plus 文件夹，然后将其映射到容器内的 /app/data 目录。这样可以确保数据持久化，即使容器重启也不会丢失数据。

**环境变量配置：**

必须设置：

DB\_HOST：MySQL 数据库地址（如 127.0.0.1）

DB\_PORT：MySQL 数据库端口（默认 3306）

DB\_NAME：数据库名称（如 novel\_plus）

DB\_USER：数据库用户名

DB\_PASSWORD：数据库密码

REDIS\_HOST：Redis 缓存地址（如 127.0.0.1）

REDIS\_PORT：Redis 端口（默认 6379）

可选设置（如需使用 AI 功能）：

SPRING\_AI\_OPENAI\_API\_KEY：硅基流动 API 密钥（用于 AI 写作功能）

SPRING\_AI\_OPENAI\_BASE\_URL：AI 服务基础 URL（默认为硅基流动地址）

其他配置项保持默认即可，无需额外修改。

完成以上配置后，点击'启动'按钮，等待容器运行成功。

**访问应用：**

部署完成后，在浏览器中访问 http://你的IP地址:8888 即可进入 Novel-Plus 的首页。

如果需要进入管理后台，访问 http://你的IP地址:8888/admin（默认管理员账号和密码请查阅项目文档）。

作家创作后台访问地址为 http://你的IP地址:8888/writer。

## 功能特性速览

✨ AI 智能创作：集成 Spring AI 框架，提供 AI 扩写、缩写、续写、文本润色和自动生成封面功能

📱 多端适配：完美支持 PC 端和移动端，响应式设计确保各设备上都有最佳体验

🎨 灵活模板系统：支持自定义多套前端模板，轻松切换不同风格

📊 完整的内容管理：包含小说推荐、全文检索、排行榜、评论系统、会员订阅等功能

🕷️ 智能爬虫系统：支持多源自动采集，定时更新小说数据

💰 充值订阅模式：内置完整的会员充值和订阅系统，支持商业化运营

📈 数据统计报表：实时生成各类统计数据，助力运营决策

## 总结

Novel-Plus 是一套完整、专业的文学内容平台解决方案。通过 Docker 一键部署，即使是技术小白也能快速搭建属于自己的小说阅读网站。结合 AI 写作助手和灵活的爬虫系统，你可以轻松管理海量内容。无论是个人兴趣项目还是商业运营，Novel-Plus 都能满足你的需求。

本篇教程到此结束，祝你部署顺利！

---

原文：<https://www.passnat.com/posts/novel-plus-open-source-cms-docker-deployment-ai-writing-assistant-intranet-penetration>


## 相关文章

- [RuoYi-Flowable开源工作流管理平台Docker快速部署指南，企业级流程引擎解决方案](ruoyi-flowable-docker-deployment-guide-enterprise-workflow-engine.md)
- [个人音乐流媒体服务器Airsonic-Advanced部署教程，NAS内网穿透远程访问音乐库](airsonic-advanced-nas-deployment-guide-remote-music-streaming.md)
- [开源文件预览服务kkFileView Docker部署完整指南，支持百余种格式在线查看](open-source-file-preview-kkfileview-docker-deployment-guide.md)
- [Grav 建站：NAS 上 Docker 部署文件型 CMS + 内网穿透](private-grav-blog-nas-docker-deployment-intranet-tunneling.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
