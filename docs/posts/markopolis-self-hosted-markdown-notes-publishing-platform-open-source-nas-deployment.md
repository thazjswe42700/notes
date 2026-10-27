---
title: "Markopolis自托管Markdown笔记发布平台，完全开源无限制，NAS极简部署即可用，支持内网穿透远程访问"
date: 2025-10-16
description: "Markopolis 是自托管的开源 Markdown 笔记发布平台，支持即时渲染、全文搜索、深色主题切换。相当于自托管版 Obsidian Publish，采用现代技术栈构建，提供完整 REST API。通过 Docker 轻松部署在 NAS 或服务器，拥有数据完全控制权，适合搭建个人知识库、技术博客和文档中心。"
canonical: https://www.passnat.com/posts/markopolis-self-hosted-markdown-notes-publishing-platform-open-source-nas-deployment
---

## Markopolis - 自托管的 Markdown 笔记发布平台

### 项目介绍

Markopolis 是一个强大的自托管 Markdown 笔记发布平台，它让你能够轻松将个人笔记转化为精美的在线网站。这是一个完全开源的解决方案，相当于自托管版本的 Obsidian Publish，但功能更加强大灵活。

项目采用现代化技术栈构建，支持完整的 REST API 接口，让你可以随意扩展功能。无论你是想要发布个人知识库、技术博客，还是搭建文档中心，Markopolis 都能满足你的需求。它具有即时渲染、全文搜索、深色/浅色主题切换等丰富功能，同时保持极低的维护成本。

最重要的是，你拥有对所有数据的完全控制权，不受任何第三方平台的限制。

项目地址：[点我跳转](https://github.com/rishikanthc/markopolis)

### 部署安装

若需在外网访问你的 Markopolis 实例，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，注册账号后即可使用。详细的配置步骤可参考 [帕斯内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

#### 第一步：部署服务器

打开你的 NAS 设备或服务器，使用 Docker 来部署 Markopolis 是最快速的方式。

复制下列的 docker-compose 配置文件：

```yaml
version: '3.8'

services:
  markopolis:
    image: ghcr.io/rishikanthc/markopolis:latest
    ports:
      - "8080:8080"
      - "3000:3000"
    environment:
      - POCKETBASE_URL=http://127.0.0.1:8080
      - API_KEY=test
      - POCKETBASE_ADMIN_EMAIL=admin@admin.com
      - POCKETBASE_ADMIN_PASSWORD=password
      - TITLE=Markopolis
      - CAP1=caption1
      - CAP2=caption2
      - CAP3=caption3
    volumes:
      - ./pb_data:/app/db
```

打开飞牛 NAS 或其他 NAS 设备，进入 Docker 管理界面，选择'本地镜像'→'添加镜像'→'从 URL 添加'。

在镜像地址栏填入：ghcr.io/rishikanthc/markopolis:latest

等待镜像下载完成后，点击启动按钮启动容器。

#### 环境变量配置说明

**必须设置的环境变量：**

API\_KEY：API 访问密钥，用于保护 API 端点的安全。请设置一个强密码，不要使用默认值 'test'

POCKETBASE\_ADMIN\_EMAIL：数据库管理员邮箱，用于后台管理账户登录

POCKETBASE\_ADMIN\_PASSWORD：数据库管理员密码，请设置安全的密码

**可选设置的环境变量：**

TITLE：网站标题，默认为 'Markopolis'

CAP1、CAP2、CAP3：网站副标题/描述文字，可根据需要自定义

POCKETBASE\_URL：**不要修改此项**，保持为 http://127.0.0.1:8080

#### 端口映射说明

容器暴露两个端口：

3000：前端 Web 应用访问端口

8080：PocketBase 数据库服务端口

如果你的系统中这些端口已被占用，可以修改映射配置。例如，若要改为 8000 和 9000，修改为：

```
ports:
  - "8000:3000"
  - "9000:8080"
```

#### 数据持久化

配置文件中的 ./pb\_data:/app/db 用于存储数据库文件。请确保在运行容器前创建 pb\_data 文件夹，以便容器能够正确挂载并持久化你的笔记数据。

#### 启动容器

在 NAS 界面中，勾选'开机自动启动'选项，然后点击'启动'按钮。

容器启动成功后，你可以通过浏览器访问 http://你的设备IP:3000 来打开 Markopolis 平台。

#### 第二步：安装 CLI 工具（可选）

如果你想要从本地上传 Markdown 笔记到服务器，需要在本地安装 Markopolis 的 CLI 工具。

首先确保你的系统已安装 Python 3.12 或更高版本。

使用 pip 安装 CLI 工具：

```
pip install markopolis
```

然后配置环境变量指向你的 Markopolis 服务器：

**对于 bash/zsh：**

```
export MARKOPOLIS_DOMAIN=https://你的域名或IP:3000
export MARKOPOLIS_API=你的API_KEY
```

**永久保存配置（bash）：**

```
echo 'export MARKOPOLIS_DOMAIN=https://你的域名或IP:3000' >> ~/.bashrc
echo 'export MARKOPOLIS_API=你的API_KEY' >> ~/.bashrc
source ~/.bashrc
```

配置完成后，你就可以使用 CLI 命令来发布你的笔记了。

### 总结

Markopolis 提供了一个简单易用的自托管解决方案，让你能够完全掌控自己的笔记发布平台。通过 Docker 的支持，部署过程变得异常简单，即使是技术小白也能轻松上手。无论是个人知识管理还是团队文档共享，Markopolis 都是一个值得尝试的选择。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/markopolis-self-hosted-markdown-notes-publishing-platform-open-source-nas-deployment>


## 相关文章

- [MinDoc轻量级文档管理系统Docker极简部署，Golang高性能跨平台支持，内网穿透远程安全访问](mindoc-docker-deployment-golang-intranet-penetration.md)
- [私密日记极简部署，纯文本记录无数据库，Hibiscus.txt Docker内网穿透随处访问](hibiscus-txt-private-diary-docker-deployment.md)
- [自托管短链接服务Snapp，私密数据掌控无忧，NAS极简部署零烦恼，内网穿透远程访问](self-hosted-url-shortener-snapp-private-data-nas-deployment-intranet-penetration.md)
- [浏览器WebRTC视频通话应用Call-Me，NAS一键部署，支持屏幕共享与内网穿透远程通话](browser-webrtc-video-call-app-call-me-nas-one-click-deployment-screen-sharing-intranet-penetration-remote-calling.md)
- [自建图片社区平台Szurubooru，Docker极简部署NAS，内网穿透远程访问图片库](szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library.md)
