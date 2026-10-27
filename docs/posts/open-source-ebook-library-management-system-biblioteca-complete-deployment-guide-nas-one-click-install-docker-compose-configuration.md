---
title: "开源电子书库管理系统Biblioteca完整部署指南-NAS一键安装Docker Compose配置"
date: 2025-10-16
description: "Biblioteca是开源电子书库管理系统，支持大规模书籍收藏、分类组织和搜索。本文详细介绍Docker快速部署方法，包括配置文件、端口映射、环境变量设置等步骤，帮助用户在NAS或服务器上轻松搭建属于自己的电子书管理平台。"
canonical: https://www.passnat.com/posts/open-source-ebook-library-management-system-biblioteca-complete-deployment-guide-nas-one-click-install-docker-compose-configuration
---

## 项目介绍

Biblioteca 是一款功能强大的电子书库管理系统，专为处理大规模电子书收藏而设计。这是一个开源的网页应用，致力于帮助用户建立结构清晰、分类规范的电子书库。无论你是个人收藏爱好者还是图书馆管理者，Biblioteca 都能为你提供一套完整的解决方案，让你轻松管理和组织数千本电子书。

项目地址：[点我跳转](https://github.com/biblioverse/biblioteca)

## 部署安装

如果你想在外网也能访问项目，建议配置内网穿透服务。可以考虑使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可以 [参考这里](https://www.passnat.com/posts/p) 了解内网穿透的全平台快速上手方法。

### 使用 Docker Compose 快速部署

Biblioteca 采用现代化的技术栈，包含 PHP 后端、MariaDB 数据库和 Typesense 搜索引擎。推荐使用 Docker Compose 一键部署，最为便捷。

**第一步：复制配置文件**

将下列 Docker Compose 配置文件保存到你的服务器或 NAS 设备：

```yaml
services:
  biblioteca:
    image: ghcr.io/biblioverse/biblioteca:latest
    ports:
      - "8080:8080"
    depends_on:
      db:  
        condition: service_started  
      typesense:
        condition: service_started
    environment:
      - DATABASE_URL=mysql://biblioteca:biblioteca@db:3306/biblioteca
      - TYPESENSE_API_KEY=xyz
      - TYPESENSE_HOST=typesense
    networks:
      - biblioteca

  db:
    image: mariadb:12.0
    environment:
      - MYSQL_ROOT_PASSWORD=biblioteca
      - MYSQL_DATABASE=biblioteca
      - MYSQL_USER=biblioteca
      - MYSQL_PASSWORD=biblioteca
    ports:
      - "3306:3306"
    volumes:
      - mariadb_data:/var/lib/mysql
    networks:
      - biblioteca

  typesense:
    image: typesense/typesense:29.0
    restart: on-failure
    ports:
      - "8108:8108"
    volumes:
      - typesense_data:/data
    command: '--data-dir /data --api-key=xyz --enable-cors'
    networks:
      - biblioteca

volumes:
  mariadb_data:
  typesense_data:

networks:
  biblioteca:
    driver: bridge
```

**第二步：启动服务**

打开你的飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面。

点击 **本地镜像** → **添加镜像** → **从 URL 添加**。

在镜像地址栏填入：ghcr.io/biblioverse/biblioteca:latest

等待镜像下载完成后，点击 **启动按钮** 启动容器。

**第三步：配置端口映射**

勾选 **开机自动启动** 后进入下一步。

在端口设置页面，你需要配置以下端口映射：
8080 → 容器内的 8080 端口（Biblioteca 主应用）
3306 → 容器内的 3306 端口（MariaDB 数据库）
8108 → 容器内的 8108 端口（Typesense 搜索引擎）

如果你的服务器上这些端口已被占用，可以修改左侧的外部端口号，但请确保格式正确。例如，如果 8080 被占用，可以改为 8888，访问时使用 ip:8888 即可。

**第四步：存储配置**

在存储位置设置中，建议创建以下目录用于数据持久化：

/biblioteca\_data/mariadb - 用于存储数据库文件
/biblioteca\_data/typesense - 用于存储搜索索引

这样即使容器重启，你的数据也不会丢失。

**第五步：环境变量设置**

**必须设置的环境变量：**

DATABASE\_URL - 数据库连接字符串，默认值：mysql://biblioteca:biblioteca@db:3306/biblioteca
TYPESENSE\_API\_KEY - Typesense 搜索引擎的 API 密钥，默认值：xyz

**可选设置的环境变量：**

TYPESENSE\_HOST - Typesense 服务器地址，默认值：typesense

其他配置项保持默认即可，无需特殊修改。

**第六步：启动应用**

完成上述配置后，点击 **启动** 按钮。

等待容器启动完成（通常需要 1-2 分钟），然后在浏览器中访问 http://你的服务器IP:8080，就能看到 Biblioteca 的管理界面了。

## 总结

至此，Biblioteca 电子书库管理系统已成功部署到你的服务器或 NAS 设备上。你现在可以开始上传和管理你的电子书收藏，享受高效便捷的图书管理体验。如有任何问题，可以参考项目的 [官方文档](https://biblioverse.github.io/biblioteca/)。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/open-source-ebook-library-management-system-biblioteca-complete-deployment-guide-nas-one-click-install-docker-compose-configuration>


## 相关文章

- [开源个人关系管理系统Monica私密部署指南，NAS一键Docker快速安装配置教程](open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install.md)
- [开源仓库管理系统GreaterWMS Docker部署教程 NAS极简搭建完整指南](open-source-warehouse-management-system-greaterwms-docker-deployment-tutorial-nas-simple-setup-complete-guide.md)
- [自托管个人收藏管理系统Koillection，NAS私有部署Docker快速上手指南](koillection-nas-docker-deployment-guide.md)
- [Calibre-Web 自建电子书库：飞牛 NAS Docker Compose 部署，内网穿透在外面也能翻自己的书](calibre-web-self-hosted-ebook-library-nas-docker-deployment-intranet-penetration.md)
- [OpenClinica开源临床数据管理系统Docker部署指南，NAS内网穿透远程访问完整教程](openclinica-docker-deployment-nas-intranet-penetration-remote-access-guide.md)
