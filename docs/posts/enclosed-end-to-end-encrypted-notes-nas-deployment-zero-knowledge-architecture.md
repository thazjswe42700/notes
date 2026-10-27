---
title: "端到端加密笔记分享，NAS 极简部署 Enclosed，零知识架构客户端加密，内网穿透外网访问"
date: 2025-10-16
description: "Enclosed是一款极简主义端到端加密笔记分享应用，采用SolidJS、HonoJS和TypeScript构建。零知识架构确保服务器无法访问笔记内容，采用AES-GCM 256位加密算法。支持密码保护、过期设置、自动销毁等功能，界面简洁直观，支持多语言和暗黑模式，在任何设备上流畅使用。"
canonical: https://www.passnat.com/posts/enclosed-end-to-end-encrypted-notes-nas-deployment-zero-knowledge-architecture
---

## 项目介绍

**Enclosed** 是一款极简主义的端到端加密笔记分享应用。它采用现代化的技术栈（SolidJS + HonoJS + TypeScript）构建，专为隐私和安全而生。无论你想分享敏感信息、临时笔记还是机密文件，Enclosed 都能确保内容在整个传输和存储过程中保持完全加密状态。

这个项目最大的特点是**零知识架构**——服务器完全无法访问你的笔记内容。所有加密工作都在客户端完成，使用业界标准的 AES-GCM 256 位加密算法。你可以为笔记设置密码、设定过期时间，甚至让笔记在被阅读后自动销毁。界面简洁直观，支持多语言和暗黑模式，在任何设备上都能流畅使用。

[点我跳转](https://github.com/CorentinTh/enclosed)

项目地址：

## 部署安装

如果你需要在外网也能访问自己部署的 Enclosed 实例，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，详细的跨平台上手指南请 [点我查看](https://www.passnat.com/posts/p)。

### 使用 Docker 快速启动

打开你的 NAS 设备或任何支持 Docker 的系统，执行以下命令即可一键启动 Enclosed：

```bash
docker run -d --name enclosed --restart unless-stopped -p 8787:8787 corentinth/enclosed
```

这条命令会：

下载最新的 Enclosed 镜像

创建一个名为 'enclosed' 的容器

将容器的 8787 端口映射到主机的 8787 端口

设置容器开机自动启动

如果 8787 端口已被占用，可以修改前面的端口号。例如改为 '8888:8787' 表示通过本机 8888 端口访问应用。

启动完成后，在浏览器中访问 'http://你的IP:8787' 即可打开 Enclosed 应用。

### 使用 Docker Compose 部署（推荐有持久化需求的用户）

如果你希望笔记数据能够持久保存，建议使用 Docker Compose。

复制以下配置文件内容：

```yaml
services:
  enclosed:
    image: corentinth/enclosed
    ports:
      - 8787:8787
    volumes:
      - enclosed-data:/app/.data
    restart: unless-stopped

volumes:
  enclosed-data:
    driver: local
```

将上述内容保存为 'docker-compose.yml' 文件，然后在文件所在目录执行：

```
docker-compose up -d
```

这个配置会自动创建一个数据卷来保存笔记数据，确保容器重启后数据不会丢失。

**端口说明**：容器内部运行在 8787 端口，如需修改外部访问端口，只需改动 'ports' 字段中冒号前的数字即可，但容器内端口（冒号后）需保持为 8787。

**存储位置**：'enclosed-data' 是一个 Docker 数据卷，用于存储所有加密笔记的元数据。Docker 会自动管理其位置，无需手动配置。

**环境变量**：Enclosed 开箱即用，无需设置任何环境变量即可正常运行。所有配置都已使用默认值优化。

部署完成后，在浏览器访问 'http://你的IP:8787' 就能看到 Enclosed 的优雅界面了。现在你可以开始创建、分享和接收端到端加密的笔记了！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/enclosed-end-to-end-encrypted-notes-nas-deployment-zero-knowledge-architecture>


## 相关文章

- [轻量级客户端加密笔记本，NAS一键部署Notepad，口令登录随时随地访问笔记](lightweight-encrypted-notes-nas-one-click-deploy-notepad-password-login.md)
- [本地密码生成器PSWD，隐私优先TypeScript构建，支持Docker和NAS快速部署，内网穿透远程访问](local-password-generator-pswd-privacy-first-typescript-docker-nas-deployment-intranet-penetration-remote-access.md)
- [现代React推箱子游戏，500+谜题无限撤销，NAS极简部署内网穿透在线玩](modern-react-sokoban-game-500-puzzles-unlimited-undo-nas-simple-deployment-intranet-penetration-online-play.md)
- [Hexon Hexo博客在线管理系统，图形化界面轻松管理文章，Docker极简部署内网穿透远程访问](hexon-hexo-blog-management-system-graphic-interface-docker-deployment.md)
- [无代码网页数据提取Maxun自建指南，2分钟训练机器人自动爬取，内网穿透远程访问](no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access.md)
