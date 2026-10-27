---
title: "开箱即用 Markdown 笔记应用，多库协作实时编辑，NAS 极简部署 Many Notes 内网穿透远程访问"
date: 2025-10-16
description: "Many Notes 是一款简洁高效的 Markdown 笔记应用，支持多用户管理、实时协作、快速搜索等功能。数据同时存储在数据库和文件系统中，确保完全控制权。支持多笔记库、标签链接、PDF导出、PWA、主题切换和备份恢复，适合个人和团队协作使用。"
canonical: https://www.passnat.com/posts/many-notes-markdown-notebook-collaborative-editing-nas-deployment
---

## 项目介绍

Many Notes 是一款简洁高效的 Markdown 笔记应用，专为用户提供开箱即用的笔记记录体验。这个项目采用现代化的技术栈构建，支持多用户管理、多个笔记库、实时协作、快速搜索等功能，让你可以轻松组织和管理自己的所有笔记内容。

项目的独特之处在于，它既利用数据库提供强大的功能支持，同时也将文件保存在文件系统中，确保你对笔记结构拥有完全的控制权，并且可以随时随地访问和备份。无论你是个人用户还是团队协作，Many Notes 都能满足你的需求。支持多个笔记库的创建，让你可以根据不同的主题或项目进行分类管理。

项目地址：[点我跳转](https://github.com/brufdev/many-notes)

## 部署安装

如果你需要在外网也能访问到项目，建议配置内网穿透服务。可以选择 [帕斯内网穿透](https://passnat.com) 这样的解决方案，先注册账号备用。具体的操作步骤可以 [参考这份全平台上手指南](https://www.passnat.com/posts/p)，让你快速掌握内网穿透的使用方法。

### Docker Compose 快速部署

这是最简单的部署方式，适合大多数用户。

**第一步：创建配置文件**

在你的 NAS 或服务器上创建一个 `compose.yaml` 文件，复制以下内容：

```yaml
services:
  php:
    image: brufdev/many-notes:latest
    restart: unless-stopped
    environment:
      - APP_URL=http://localhost
    volumes:
      - database:/var/www/html/database/sqlite
      - logs:/var/www/html/storage/logs
      - private:/var/www/html/storage/app/private
      - typesense:/var/www/html/typesense
    ports:
      - 80:8080

volumes:
  database:
  logs:
  private:
  typesense:
```

**第二步：启动服务**

打开你的 NAS 设备（飞牛、群晖等），进入 Docker 管理界面，将上述配置文件上传或复制，然后执行以下命令启动容器：

```bash
docker compose up -d
```

**第三步：配置调整**

访问地址：根据你的实际情况修改 `APP\_URL` 字段。如果你改变了端口（比如改为 8080），需要更新为 `http://localhost:8080`

时区设置：可以添加 `APP\_TIMEZONE=Asia/Shanghai` 来设置时区（默认为 UTC）

上传限制：默认文件上传限制为 500M，如需增加可添加以下环境变量：

```
- PHP_POST_MAX_SIZE=1G
- PHP_UPLOAD_MAX_FILE_SIZE=1G
```

**环境变量说明：**

**必须设置：**

`APP\_URL`：应用访问地址

**可选设置：**

`APP\_TIMEZONE`：时区设置（默认 UTC）

`PHP\_POST\_MAX\_SIZE`：POST 请求大小限制（默认 500M）

`PHP\_UPLOAD\_MAX\_FILE\_SIZE`：文件上传大小限制（默认 500M）

如果你需要启用 OAuth 认证（如 GitHub、Google 等），可以添加对应的 `CLIENT\_ID` 和 `CLIENT\_SECRET` 环境变量。

**第四步：访问应用**

部署完成后，打开浏览器访问 `http://localhost`（如果改了端口就改成对应的端口号），即可看到 Many Notes 的登录界面。创建账户后就可以开始使用了。

**端口说明：**

容器内部服务运行在 8080 端口

通过 Docker 映射到主机的 80 端口

如果 80 端口被占用，可以修改为其他端口，比如改为 `8080:8080`

**存储说明：**

项目使用了四个持久化卷来保存数据：

`database`：SQLite 数据库文件

`logs`：应用日志

`private`：私密文件存储

`typesense`：搜索引擎数据

这些卷会自动创建并持久化保存，无需手动干预。

## 功能特性

Many Notes 提供了一系列强大的功能：

🔐 多用户支持：通过认证保护你的笔记

📚 多库管理：为不同的项目创建独立的笔记库

🔄 实时协作：邀请他人共同编辑笔记

🔍 快速搜索：支持容错搜索，快速定位笔记

📝 Markdown 编辑：强大的 Markdown 编辑器，自动保存

🏷️ 标签和链接：通过标签、链接和反向链接组织笔记

📄 PDF 导出：将笔记导出为 PDF 格式

🌓 主题切换：自动适配系统亮色/暗色主题

📱 PWA 支持：获得原生应用般的体验

💾 备份恢复：轻松导入导出笔记库

本篇教程结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/many-notes-markdown-notebook-collaborative-editing-nas-deployment>


## 相关文章

- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
- [私人知识库无限制 Logseq 开源笔记 NAS 部署 内网穿透远程访问管理](private-knowledge-base-unlimited-logseq-opensource-notes-nas-deployment-intranet-penetration-remote-access-management.md)
- [轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统](noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access.md)
- [Slidev开发者演示文稿工具，Markdown轻松创建专业幻灯片，代码高亮实时演示编辑](slidev-markdown-presentation-tool-code-highlight.md)
- [思绪思维导图：NAS Docker 部署 + 内网穿透](mindmap-web-app-lightweight-opensource-tool-multiple-structure-types-export-formats-nas-docker-deployment.md)
