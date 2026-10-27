---
title: "极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库"
date: 2025-10-16
description: "Dnote是一款极简强大的命令行笔记本应用，采用轻量级设计，单一二进制文件零依赖。笔记安全存储在SQLite数据库中，支持跨设备同步、全文搜索。本指南详细介绍Docker Compose快速部署方法，包括配置文件、端口设置、数据存储、开机自启等完整步骤，帮助你在NAS上轻松搭建私有笔记服务。"
canonical: https://www.passnat.com/posts/dnote-self-hosted-docker-deployment-internal-penetration-remote-access
---

## 项目介绍

Dnote 是一款极简而强大的命令行笔记本应用，自 2017 年以来就备受开发者青睐。它采用轻量级设计理念，单一二进制文件、零依赖，让你可以轻松在任何设备上部署使用。

你的所有笔记都被安全地存储在一个 SQLite 数据库文件中，具有高度的可移植性和可搜索性，完全掌握在你自己手中。同时它还支持跨设备同步功能，通过自建服务器的 REST API 接口实现无缝协作。无论是快速记录代码片段、保存系统命令，还是进行全文搜索查询，Dnote 都能胜任。

[点我跳转](https://github.com/dnote/dnote)

项目地址：

## 部署安装

如果你想在外网也能访问自己的笔记库，可以考虑配置内网穿透方案。建议使用 [帕斯内网穿透](https://passnat.com) 服务，先在官网注册账号备用。具体的配置步骤可以参考 [这份详细指南](https://www.passnat.com/posts/p)，它提供了全平台的上手教程。

### 使用 Docker Compose 快速部署

这是最推荐的方式，一条命令即可启动完整的 Dnote 服务器。

**第一步：复制配置文件**

将下面的 Docker Compose 配置复制到你的 NAS 或服务器上，保存为 `compose.yml` 文件：

```yaml
services:
  dnote:
    image: dnote/dnote:latest
    container_name: dnote
    ports:
      - 3001:3001
    volumes:
      - ./dnote_data:/data
    restart: unless-stopped
```

**第二步：启动服务**

在飞牛 NAS 或其他 NAS 设备中，打开应用中心或 Docker 管理面板，选择'从 Compose 文件导入'，粘贴上述配置内容，点击启动即可。

**第三步：端口配置说明**

容器内部端口：3001（固定，不需要修改）

主机映射端口：3001（可根据需要修改，如果与其他应用冲突，改为其他端口如 3002、3003 等，但要保持格式正确）

如果你的 NAS 上已经有其他服务占用了 3001 端口，只需修改配置中的第一个 3001 为其他可用端口号即可。

**第四步：数据存储配置**

配置中的 `./dnote\_data:/data` 表示将容器内的数据目录映射到本地。建议在 NAS 上提前创建一个专用文件夹（如 `/mnt/nas/dnote\_data`），然后将配置改为：

```yaml
volumes:
  - /mnt/nas/dnote_data:/data
```

这样你的所有笔记数据都会被持久化保存，即使容器重启也不会丢失。

**第五步：开机自启设置**

勾选'开机自动启动'选项，确保 NAS 重启后 Dnote 服务能够自动恢复运行。

**第六步：环境变量（可选）**

Dnote 的基础部署不需要额外的环境变量配置。如果你需要启用邮件通知功能，可以添加以下环境变量：

变量名：SmtpHost | 说明：SMTP 服务器地址 | 是否必须：可选

变量名：SmtpPort | 说明：SMTP 服务器端口 | 是否必须：可选

变量名：SmtpUsername | 说明：SMTP 账户用户名 | 是否必须：可选

变量名：SmtpPassword | 说明：SMTP 账户密码 | 是否必须：可选

通常情况下，你无需设置这些变量，直接启动即可正常使用。

**第七步：访问应用**

部署完成后，在浏览器中访问 `http://你的NAS地址:3001`，即可看到 Dnote 的 Web 界面。如果修改了端口号，请相应地替换为你设置的端口。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/dnote-self-hosted-docker-deployment-internal-penetration-remote-access>


## 相关文章

- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [Isso 评论区 NAS Docker 部署 + 内网穿透](isso-nas-docker-self-hosted-comments-intranet-penetration.md)
- [Rustpad 协同编辑器：NAS Docker 部署 + 内网穿透](rustpad-nas-docker-collaborative-code-editor-intranet-penetration.md)
- [开源个人数据管理框架Eidos本地部署指南，SQLite私密数据库离线使用，NAS内网穿透远程访问完整教程](eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial.md)
- [自建开源协作编辑平台Etherpad，NAS极简部署，数据完全掌控，内网穿透远程访问](etherpad-nas-deployment-guide.md)
