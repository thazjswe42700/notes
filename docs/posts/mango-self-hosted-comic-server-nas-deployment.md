---
title: "自托管漫画服务器Mango，私密在线阅读平台，支持多格式漫画，NAS一键部署"
date: 2025-10-16
description: "Mango自托管漫画服务器和阅读器，支持多用户账户、自动进度保存、缩略图生成。兼容.cbz、.zip、.cbr、.rar格式，响应式设计适配移动设备。提供深色/浅色主题、OPDS协议支持和插件扩展机制。Docker快速部署，私密便捷的在线漫画阅读平台。"
canonical: https://www.passnat.com/posts/mango-self-hosted-comic-server-nas-deployment
---

## 项目介绍

Mango 是一个自托管的漫画服务器和阅读器，为漫画爱好者提供了一个私密、便捷的在线阅读平台。这是一款功能完整的漫画管理系统，支持多用户账户、自动阅读进度保存、缩略图生成等丰富功能。

项目采用现代化架构设计，所有静态文件都内嵌在二进制文件中，部署过程简洁高效。支持 `.cbz`、`.zip`、`.cbr` 和 `.rar` 等多种漫画格式，并能够自动识别嵌套文件夹结构。Web 阅读器响应式设计，在移动设备上也能获得良好的阅读体验，无需额外的手机应用。

此外，项目还支持插件扩展机制，允许用户从第三方网站下载漫画资源。深色/浅色主题切换功能让用户可以根据环境选择舒适的阅读模式，OPDS 协议支持让你能够通过多种电子书阅读器访问你的漫画库。

项目地址： [点我跳转](https://github.com/hkalexling/Mango)

> 重要提示：该项目自 2025 年 3 月起已停止维护。虽然代码仍然开源，但建议谨慎使用。

## 部署安装

如果你需要在家庭网络外访问 Mango，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) - 注册账号后即可使用。详细的配置指南可以参考 [这份全平台上手教程](https://www.passnat.com/posts/p)，快速掌握内网穿透的使用方法。

### Docker Compose 部署（推荐）

这是最快速的部署方式，适合所有用户。

**第一步：准备配置文件**

复制下列的配置文件到你的 NAS 或服务器上，保存为 `docker-compose.yml`：

```yaml
version: '3'

services:
  mango:
    container_name: mango
    build:
      context: .
      dockerfile: ./Dockerfile
    expose:
      - ${PORT}
    ports:
      - "${PORT}:9000"
    volumes:
      - ${MAIN_DIRECTORY_PATH}:/root/mango
      - ${CONFIG_DIRECTORY_PATH}:/root/.config/mango
```

同时创建 `.env` 文件，填入以下配置：

```
PORT=9000
MAIN_DIRECTORY_PATH=/path/to/your/mango/library
CONFIG_DIRECTORY_PATH=/path/to/your/mango/config
```

**第二步：启动容器**

打开飞牛 NAS 或其他的 NAS 设备，进入 Docker 管理界面。

通过 `docker-compose up -d` 命令启动容器，或者在 NAS 的 Docker 管理面板中上传 `docker-compose.yml` 和 `.env` 文件，然后点击启动。

容器会自动拉取最新的 Mango 镜像并启动服务。

**第三步：配置说明**

PORT：Mango 服务运行的端口，默认为 9000。如果该端口已被占用，可修改此值，但容器内部端口始终为 9000，无需修改

MAIN\_DIRECTORY\_PATH：你的漫画库存储路径。请提前在 NAS 上创建一个文件夹，并将其路径填入。Mango 会自动扫描此目录下的所有漫画文件

CONFIG\_DIRECTORY\_PATH：配置文件存储路径。用于保存数据库和配置信息

**第四步：访问应用**

部署完成后，在浏览器中访问 `http://你的NAS地址:9000`，即可打开 Mango 界面。

首次登录时，系统会生成一个默认用户名和随机密码，这些信息会输出到容器日志中。建议立即登录后修改密码以保证安全。

### Docker 单命令部署

如果你只想快速体验，可以使用官方提供的 Docker 镜像：

```bash
docker run -d \
  --name mango \
  -p 9000:9000 \
  -v /path/to/library:/root/mango \
  -v /path/to/config:/root/.config/mango \
  hkalexling/mango:latest
```

将 `/path/to/library` 和 `/path/to/config` 替换为你实际的目录路径即可。

## 环境变量配置

Mango 的核心配置通过 `config.yml` 文件管理，以下是主要的可配置参数：

**必须设置**

library\_path：漫画库目录路径

port：服务端口（默认 9000）

**可选设置**（使用默认值即可）

scan\_interval\_minutes：库扫描间隔（分钟），默认 5。设置为 0 禁用自动扫描

thumbnail\_generation\_interval\_hours：缩略图生成间隔（小时），默认 24

log\_level：日志级别，可选值为 debug、info、warn、error、fatal、off，默认 info

cache\_enabled：是否启用元数据缓存，默认 true，可提升页面加载速度

cache\_size\_mbs：缓存大小（MB），默认 50

disable\_login：禁用登录认证，默认 false（不推荐启用）

大多数用户保持默认配置即可正常使用，无需额外调整。

## 使用建议

部署完成后，你可以：

上传漫画：将你的漫画文件（支持 .cbz、.zip、.cbr、.rar 格式）放入 `library\_path` 指定的目录

组织结构：支持创建子文件夹来分类管理漫画，系统会自动识别嵌套结构

多用户管理：通过管理员账户创建多个用户账户，每个用户的阅读进度独立保存

插件扩展：如需从第三方网站下载漫画，可配置相应的插件

到这里部署就全部完成了，现在你可以享受私密的漫画阅读体验了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/mango-self-hosted-comic-server-nas-deployment>
