---
title: "开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问"
date: 2025-10-16
description: "Appsmith是开源低代码平台，快速构建数据仪表板、管理后台、客户系统等业务应用。提供可视化界面、UI组件、数据库连接、API集成，降低开发门槛。支持Docker Compose部署，适合NAS设备和本地机器安装。无需编程基础即可开发功能完整应用，专业开发者也能高效构建复杂系统。"
canonical: https://www.passnat.com/posts/appsmith-nas-docker-compose-local-deployment-guide
---

## 项目介绍

Appsmith 是一个开源的低代码平台，专为快速构建自定义业务应用而设计。无论是数据仪表板、管理后台、客户信息系统、IT自动化工具还是服务管理系统，Appsmith 都能帮助团队高效地开发、部署和维护这些应用。

这个平台的核心优势在于它大幅降低了应用开发的门槛。即使你不是专业的开发者，也能通过可视化界面快速搭建功能完整的业务应用。Appsmith 提供了丰富的UI组件、数据库连接、API集成等开箱即用的功能，让你可以专注于业务逻辑而不用纠缠于底层技术细节。

项目地址：[点我跳转](https://github.com/appsmithorg/appsmith)

## 部署安装

如果你需要在外网也能访问你的 Appsmith 应用，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样就能随时随地远程访问你的应用了。具体操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。

### Docker Compose 部署（推荐方案）

这是最简单快速的部署方式，适合大多数用户。

**第一步：创建部署文件夹**

打开你的 NAS 设备或本地机器，创建一个名为 'appsmith' 的文件夹用于存放配置和数据。

**第二步：添加 Docker Compose 配置**

复制下列配置文件到 'docker-compose.yml'：

```yaml
version: "3"
services:
  appsmith:
    image: index.docker.io/appsmith/appsmith-ee
    container_name: appsmith
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./stacks:/appsmith-stacks
    restart: unless-stopped
```

**第三步：启动容器**

在飞牛 NAS 或其他 NAS 设备上，打开 Docker 管理界面，选择 "本地镜像" → "添加镜像" → "从 URL 添加"。

输入镜像地址：'index.docker.io/appsmith/appsmith-ee'（这是企业版，免费使用且可随时升级）

如果你想使用社区版，可以改为：'index.docker.io/appsmith/appsmith-ce'

等待镜像下载完成后，点击启动按钮。

**第四步：容器配置**

开机自启：勾选 "开机自动启动" 选项，确保系统重启后应用自动运行

端口映射：设置如下端口映射

容器内端口 80 → 宿主机端口 80

容器内端口 443 → 宿主机端口 443

如果这些端口已被占用，可以修改宿主机端口（左侧数字），比如改为 8080:80，但容器内端口保持不变

存储位置：将 '/appsmith-stacks' 映射到本地一个文件夹（用于持久化存储应用数据和配置），建议提前创建好这个文件夹

环境变量：Appsmith 的基础配置已经包含在镜像中，一般情况下无需额外设置环境变量。如有特殊需求可自行添加

**第五步：启动并访问**

点击确认启动容器。等待 3-5 分钟让服务完全启动。

然后在浏览器中访问：'http://你的设备IP' 或 'http://localhost'（如果在本机访问）

首次访问时会要求你创建管理员账号，填写相关信息即可。

创建账号后，你可以选择使用免费计划，或者在 [customer.appsmith.com](https://customer.appsmith.com) 生成许可证密钥来激活高级功能。

**部署完成！** 现在你已经拥有了一个功能完整的低代码开发平台，可以开始构建你的第一个业务应用了。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/appsmith-nas-docker-compose-local-deployment-guide>


## 相关文章

- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管提醒应用MIND，Docker极简部署NAS，80+平台消息推送，内网穿透远程访问](self-hosted-reminder-app-mind-docker-nas-deployment-80-plus-messaging-platforms-internal-penetration-remote-access.md)
- [开源无代码数据库SeaTable自托管部署指南，NAS极简配置Docker Compose一键启动](seatable-self-hosted-nas-docker-compose-deployment-guide.md)
- [无代码网页数据提取Maxun自建指南，2分钟训练机器人自动爬取，内网穿透远程访问](no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access.md)
- [开源问卷调查系统LimeSurvey，NAS一键部署，内网穿透远程访问，无限问卷模板](open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates.md)
