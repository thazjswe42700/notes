---
title: "SideQuests 任务管理系统 Docker 一键部署，NAS 极简配置内网穿透远程访问任务"
date: 2025-10-16
description: "SideQuests是一款简洁高效的任务管理系统，基于Flask+SQLAlchemy+SQLite构建。支持分层任务管理、响应式设计、拖拽功能、深色模式和PWA特性。可通过Docker一键部署，适合个人和团队追踪目标与待办事项，开箱即用。"
canonical: https://www.passnat.com/posts/sidequests-docker-nas-deployment-intranet-penetration
---

## 项目介绍

SideQuests 是一款简洁高效的任务管理系统，专为追踪目标和任务而生。它采用 Flask + SQLAlchemy + SQLite 的技术栈构建，提供了一个直观易用的网页界面。无论你是想管理日常待办事项，还是追踪长期目标，SideQuests 都能轻松胜任。

项目支持任务分层管理（Quest 和 Objective），完全响应式设计确保在手机、平板和电脑上都有出色体验。拖拽式的区块设计让组织任务变得轻而易举，深色模式保护你的眼睛，PWA 特性让你可以像使用原生应用一样使用它。最重要的是，部署简单，开箱即用！

项目地址： [点我跳转](https://github.com/need4swede/SideQuests)

## 部署安装

想要在公网上随时随地访问你的任务管理系统？建议配置内网穿透来实现远程访问。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的操作步骤可以 [参考这篇攻略](https://www.passnat.com/posts/p) 来快速上手全平台内网穿透方案。

### Docker 快速启动

打开你的 NAS 设备或任何支持 Docker 的系统，执行以下命令即可一键启动 SideQuests：

```bash
docker run -d \
  -p 8080:8080 \
  -e PORT=8080 \
  -e ADMIN_USERNAME=admin \
  -e ADMIN_PASSWORD=your_password \
  -e SECRET_KEY=your_secret_key \
  need4swede/sidequests:latest
```

参数说明：

ADMIN\_USERNAME: 管理员用户名（默认：admin）

ADMIN\_PASSWORD: 管理员密码（请修改为安全的密码）

SECRET\_KEY: 应用密钥（请修改为随机字符串）

如果 8080 端口已被占用，可以将前面的 *8080* 改为其他端口号（如 *8081*），容器内部端口保持 *8080* 不变。

启动完成后，在浏览器中访问 *http://你的IP:8080* 即可进入任务管理界面。

### Docker Compose 部署（可选）

如果你更习惯使用 Docker Compose，可以复制下列配置文件：

```yaml
services:
  SideQuests:
    image: need4swede/sidequests:latest
    container_name: SideQuests
    ports:
      - "8080:8080"
    volumes:
      - ./instance:/app/instance
    environment:
      - PORT=8080
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=your_password
      - SECRET_KEY=your_secret_key
```

将上述内容保存为 *docker-compose.yml* 文件，然后在同一目录运行：

```
docker-compose up -d
```

存储说明：

*./instance:/app/instance* 这个映射用于持久化存储应用数据，确保容器重启后数据不会丢失。建议在启动前创建一个 *instance* 文件夹。

环境变量设置：

必须设置：

ADMIN\_USERNAME: 管理员账户名称

ADMIN\_PASSWORD: 管理员账户密码

SECRET\_KEY: 应用加密密钥

可选设置：

PORT: 应用运行端口（默认 8080）

其他配置保持默认即可，直接启动就能正常使用。

启动成功后，使用管理员账号登录，即可开始创建和管理你的任务了。

## 总结

SideQuests 凭借其简洁的界面和强大的功能，成为了任务管理的绝佳选择。无论你是团队协作还是个人使用，它都能帮助你保持专注和高效。立即部署，开启高效的任务管理之旅吧！

---

原文：<https://www.passnat.com/posts/sidequests-docker-nas-deployment-intranet-penetration>


## 相关文章

- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
- [自建密码生成器Docker一键部署，支持离线模式安全检测泄露，NAS极简配置内网穿透远程访问](self-hosted-password-generator-docker-one-click-deploy-offline-mode-security-breach-detection-nas-simple-config-intranet-penetration-remote-access.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
