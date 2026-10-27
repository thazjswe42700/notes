---
title: "Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问"
date: 2025-10-16
description: "Cogito是功能强大的个人知识管理系统，支持笔记、链接、推文等信息汇聚。采用Angular+SpringBoot开发，提供智能标签、全文搜索、深色主题、PWA应用等丰富功能。界面简洁美观，支持Docker快速部署，特别适合搭建个人知识库。"
canonical: https://www.passnat.com/posts/1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug
---

## 项目介绍

Cogito 是一款功能强大的个人知识管理系统，将你的笔记、链接、推文和各类信息汇聚一处。它采用 Angular + Spring Boot 技术栈开发，提供了一个直观易用的界面来管理和组织你的数字资产。

无论是保存有趣的网页链接、记录重要推文，还是撰写个人笔记，Cogito 都能帮你轻松整理。项目支持智能标签自动分类、全文搜索、深色主题、待办事项标记等丰富功能。此外，它还提供了 PWA 应用支持，让你在手机上也能方便地分享内容到 Cogito。

界面简洁美观，部署简单快速，特别适合想要搭建个人知识库的用户。

项目地址：[点我跳转](https://github.com/Tukks/cogito)

## 部署安装

如需在外网访问你的 Cogito 实例，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com)

注册账号。具体的操作流程可以参考 [这份详细的上手指南](https://www.passnat.com/posts/p)

，支持全平台快速配置。

### 使用 Docker Compose 快速部署

**第一步：准备配置文件**

创建一个名为 'cogito' 的文件夹，并在其中新建 'docker-compose.yml' 文件。

**第二步：复制配置内容**

将下列配置复制到 'docker-compose.yml' 文件中：

```yaml
version: "3"

services:
  cogito:
    image: tukks/cogito:latest
    ports:
      - "8585:9191"
    volumes:
      - ./db:/app/db
      - ./image:/app/image
    environment:
      - APP_JWT_SECRET="YOUR_APP_GENERATE_SECRET"
      - API_TWITTER_KEY_SECRET="YOUR_API_TWITTER_KEY_SECRET"
      - API_TWITTER_KEY="YOUR_API_TWITTER_KEY"
      - HTTPS_ONLY=true
```

如果你使用树莓派或其他 ARM 架构设备，请将镜像改为 'tukks/cogito-armv7:latest'

**第三步：配置环境变量**

打开飞牛 NAS、群晖或其他 NAS 设备的管理界面，找到 Docker 容器管理功能。

环境变量设置说明：

**必须设置：**

'APP\_JWT\_SECRET'：应用的 JWT 密钥，建议使用 Bitwarden 等工具生成一个 256 位（128 字符长）的随机字符串。这是保证应用安全的关键参数。

**可选设置：**

'API\_TWITTER\_KEY'：Twitter 开发者账号的 API Key（如需集成推文保存功能）

'API\_TWITTER\_KEY\_SECRET'：Twitter 开发者账号的 API Secret（如需集成推文保存功能）

'HTTPS\_ONLY'：是否仅允许 HTTPS 连接，默认为 true。如果在测试环境可设为 false

其他参数保持默认即可，无需修改。

**第四步：启动容器**

在 NAS 的 SSH 终端或 Docker 管理界面中，进入 cogito 文件夹，执行以下命令：

```
docker-compose up -d
```

等待容器启动完成。

**第五步：初始化账户**

打开浏览器，访问 'http://你的NAS\_IP:8585'

首次访问时，点击'新建账户'按钮，输入邮箱和密码完成注册。然后使用刚才创建的邮箱和密码登录即可。

**第六步：配置书签小工具（可选）**

为了在浏览器中快速保存链接到 Cogito，可以添加一个书签小工具。在浏览器书签栏中新建书签，将下列代码添加到 URL 字段：

```
javascript:(function(){window.open('https://你的Cogito地址/handle-share?text='+encodeURIComponent(window.location.href));}())
```

将 '你的Cogito地址' 替换为实际的访问地址。点击这个书签就能一键保存当前网页到 Cogito。

**端口说明：**

容器内部运行在 9191 端口，外部通过 8585 端口访问。如果 8585 端口被占用，可在 docker-compose.yml 中修改第一个端口号（如改为 '8686:9191'），但容器内部的 9191 端口保持不变。

**存储说明：**

'./db' 目录：存储应用数据库文件，请确保此目录有足够的空间

'./image' 目录：存储保存的图片和截图文件

至此，Cogito 部署完成！你现在可以开始使用这个强大的个人知识管理系统了。

---

原文：<https://www.passnat.com/posts/1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug>
