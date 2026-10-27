---
title: "自建WriteFreely博客平台，NAS极简部署指南，内网穿透远程访问无限制"
date: 2025-10-16
description: "开源博客平台WriteFreely，极简设计、隐私优先，支持多语言和ActivityPub协议。提供无干扰写作编辑器、自动保存功能，可在NAS上通过Docker Compose快速部署。支持多账户管理，完美适配个人博客、知识分享和社区建设场景。"
canonical: https://www.passnat.com/posts/self-hosted-writefreely-blog-nas-deployment-guide-intranet-penetration-remote-access
---

## 项目介绍

WriteFreely 是一个简洁优雅的开源博客发布平台，专为写作者精心设计。无论你想要开启个人博客、在组织内分享知识，还是围绕写作建立一个社区，WriteFreely 都能为你提供完美的解决方案。

这个平台采用极简主义设计理念，内置自动保存的编辑器，为你营造一个无干扰的创意写作环境。支持 20+ 种语言本地化，包括对非拉丁字符和从右到左（RTL）文字的完美支持。最重要的是，WriteFreely 默认保护隐私，收集最少数据，让你可以用不同的笔名创建多个博客账户，而无需暴露它们之间的关联。

此外，WriteFreely 还支持 ActivityPub 协议，能与其他 Fediverse 社区无缝互联，让你的写作不被限制在单一平台。

项目地址：[点我跳转](https://github.com/writefreely/writefreely)

## 部署安装

想要在外网也能访问你的 WriteFreely 博客？建议配置内网穿透服务来实现远程访问。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，它提供了全平台支持的内网穿透解决方案。具体操作步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)，轻松上手。

### 使用 Docker Compose 快速部署

WriteFreely 提供了官方的 Docker Compose 配置，这是最快速、最推荐的部署方式。

**第一步：复制配置文件**

将下列配置保存为 `docker-compose.yml` 文件：

```yaml
version: '3'

volumes:
  web-keys:
  db-data:

networks:
  external_writefreely:
  internal_writefreely:
    internal: true

services:
  writefreely-web:
    container_name: 'writefreely-web'
    image: 'writeas/writefreely:latest'

    volumes:
      - 'web-keys:/go/keys'
      - './config.ini:/go/config.ini'

    networks:
      - 'internal_writefreely'
      - 'external_writefreely'

    ports:
      - '8080:8080'

    depends_on:
      - 'writefreely-db'

    restart: unless-stopped

  writefreely-db:
    container_name: 'writefreely-db'
    image: 'mariadb:latest'

    volumes:
      - 'db-data:/var/lib/mysql/data'

    networks:
      - 'internal_writefreely'

    environment:
      - MYSQL_DATABASE=writefreely
      - MYSQL_ROOT_PASSWORD=changeme

    restart: unless-stopped
```

**第二步：打开 NAS 设备**

登录到你的飞牛 NAS 或其他 NAS 设备的管理后台。

**第三步：启动容器**

在 NAS 的 Docker 管理界面中，找到'容器'或'Docker'选项，点击'新建'或'添加'，然后选择'从 Compose 文件导入'。

将上面的配置文件内容粘贴到编辑框中，系统会自动拉取所需的镜像并启动两个容器：WriteFreely 网站服务和 MariaDB 数据库服务。

**第四步：配置环境变量**

在启动前，你需要修改数据库密码以确保安全：

**必须设置**

- `MYSQL\_ROOT\_PASSWORD`：数据库 root 用户的密码（默认为 `changeme`，强烈建议修改为强密码）

- `MYSQL\_DATABASE`：数据库名称（默认为 `writefreely`，一般无需修改）

**第五步：端口配置**

WriteFreely 默认使用 `8080` 端口。如果你的 NAS 上该端口已被占用，可以修改 `ports` 字段中的第一个数字。例如，改为 `'8888:8080'` 表示通过 `8888` 端口访问。注意：冒号后面的 `8080` 是容器内部端口，必须保持不变。

**第六步：存储配置**

WriteFreely 已经配置了两个数据卷用于持久化存储：

- `web-keys`：存储 WriteFreely 的加密密钥

- `db-data`：存储 MariaDB 数据库文件

这些卷会自动在 NAS 上创建，无需手动干预。

**第七步：启动服务**

完成上述配置后，点击'启动'或'部署'按钮。系统会自动下载镜像、创建容器并启动服务。

勾选'开机自启'选项，确保 NAS 重启后 WriteFreely 能自动运行。

**第八步：初始化设置**

等待容器完全启动（通常需要 30-60 秒），然后在浏览器中访问 `http://你的NAS地址:8080`。

首次访问时，WriteFreely 会引导你完成初始化设置，包括创建管理员账户、配置博客基本信息等。按照提示操作即可。

**第九步：开始写作**

初始化完成后，你就可以登录 WriteFreely，开始创建和发布你的文章了。享受简洁、专注的写作体验吧！

## 总结

通过 Docker Compose，你可以在几分钟内在 NAS 上部署一个完整的 WriteFreely 博客平台。整个过程无需复杂的命令行操作，只需粘贴配置文件，点击几个按钮即可。WriteFreely 提供的极简设计和强大功能，将让你专注于写作本身，而不用担心技术细节。

本篇教程结束。祝你的博客之旅顺利！

---

原文：<https://www.passnat.com/posts/self-hosted-writefreely-blog-nas-deployment-guide-intranet-penetration-remote-access>
