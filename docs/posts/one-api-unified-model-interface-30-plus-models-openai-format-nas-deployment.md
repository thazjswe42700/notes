---
title: "One API 统一大模型接口，支持30+模型OpenAI格式调用，NAS一键部署使用。"
date: 2025-10-16
description: "One API是一个开箱即用的AI模型聚合平台，通过标准OpenAI格式无缝访问ChatGPT、Claude、Gemini等30+种国内外主流大模型。基于Go+React构建，提供统一API接口、渠道管理、令牌控制、负载均衡等企业级功能，支持Docker快速部署，极大降低开发和迁移成本。"
canonical: https://www.passnat.com/posts/one-api-unified-model-interface-30-plus-models-openai-format-nas-deployment
---

## One API - 通过标准 OpenAI 格式访问所有大模型

#### 项目介绍

One API 是一个开箱即用的 AI 模型聚合平台，它通过标准的 OpenAI API 格式让你能够无缝访问全球主流的大语言模型。基于 Go + React 构建，One API 支持超过 30+ 种国内外主流大模型，包括 OpenAI ChatGPT、Anthropic Claude、Google Gemini、百度文心一言、阿里通义千问等。

这个项目的核心优势在于**统一的 API 接口**——无论你使用哪个大模型，都可以通过相同的 OpenAI API 格式进行调用，极大降低了开发和迁移成本。同时，One API 还提供了强大的渠道管理、令牌控制、负载均衡等企业级功能，让你可以轻松构建自己的 AI 服务平台。

项目地址：[点我跳转](https://github.com/songquanpeng/one-api)

#### 部署安装

如果你想在公网上访问 One API，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册好账号备用。具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台上手攻略，一键即可让你的内网服务暴露到公网。

##### 最快速上手方案 - Docker 单命令部署

One API 官方推荐使用 Docker 进行部署，最简单的方式是使用 SQLite 数据库：

打开你的 NAS 设备或服务器，进入 Docker 管理界面。

**第一步：添加 Docker 镜像**

点击 `本地镜像` - `添加镜像` - `从 URL 添加`，在镜像地址栏填入：

```
justsong/one-api
```

如果无法拉取，可以尝试使用 GitHub 的镜像源：`ghcr.io/songquanpeng/one-api`

等待镜像下载完成后，点击启动按钮启动容器。

**第二步：配置容器参数**

勾选 `开机自动启动` 后进入下一步。

**端口映射设置**

在端口配置界面，设置如下：
容器端口：`3000`（内部端口，不要修改）
宿主机端口：`3000`（外部访问端口，如果 3000 端口已被占用，可修改为其他端口如 3001、3002 等）

**存储配置**

为了保证数据持久化，需要创建一个本地目录用于存储数据。建议在 NAS 上创建一个名为 `one-api` 的文件夹，然后将其映射到容器内的 `/data` 目录。

映射关系如下：
宿主机路径：`/home/nas/one-api`（根据实际情况修改）
容器内路径：`/data`

**环境变量设置**

One API 提供了丰富的环境变量配置选项。根据你的需求，可以设置以下变量：

**必须设置（如果使用 MySQL）：**

`SQL\_DSN`：数据库连接字符串，格式为 `root:password@tcp(localhost:3306)/oneapi`

**可选设置：**

`TZ`：时区设置，建议设置为 `Asia/Shanghai`（中国时区）
`SESSION\_SECRET`：会话密钥，用于保持登录状态，建议设置为一个随机字符串
`REDIS\_CONN\_STRING`：Redis 连接字符串（如需启用缓存）
`MEMORY\_CACHE\_ENABLED`：是否启用内存缓存，可选值 `true` 或 `false`
`THEME`：系统主题，默认为 `default`

如果你是初次部署，可以暂时不设置这些变量，系统会使用默认值。

**第三步：启动容器**

配置完成后，点击下一步启动容器。容器启动成功后，你就可以通过浏览器访问 One API 了。

#### 访问和初始化

部署完成后，在浏览器中访问：

```
http://你的IP:3000
```

或者如果你配置了域名，可以直接访问你的域名。

初始账号信息：

用户名：`root`
密码：`123456`

⚠️ 重要提示：首次登录后，请立即修改默认密码！这是出于安全考虑的必要步骤。

#### 基本使用流程

添加 API 渠道：在管理后台的渠道页面添加你的 API Key（支持 OpenAI、Claude、Gemini 等多种模型）
创建访问令牌：在令牌页面生成你自己的 API Key
配置客户端：在任何支持 OpenAI API 的应用中，将 API Base 设置为你的 One API 地址，API Key 设置为你生成的令牌

例如，对于 OpenAI 官方库的配置方式：

```
OPENAI_API_KEY='sk-your-token-here'
OPENAI_API_BASE='http://your-domain:3000/v1'
```

#### 高级部署方案 - Docker Compose（支持 MySQL）

如果你需要使用 MySQL 数据库以支持更高的并发，可以使用 Docker Compose 部署。

在你的服务器上创建一个 `docker-compose.yml` 文件，复制以下配置：

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: one-api-mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: your_password_here
      MYSQL_DATABASE: oneapi
    ports:
      - '3306:3306'
    volumes:
      - ./data/mysql:/var/lib/mysql

  one-api:
    image: justsong/one-api
    container_name: one-api
    restart: always
    ports:
      - '3000:3000'
    environment:
      SQL_DSN: 'root:your_password_here@tcp(mysql:3306)/oneapi'
      TZ: 'Asia/Shanghai'
      SESSION_SECRET: 'your_random_secret_here'
    volumes:
      - ./data/one-api:/data
    depends_on:
      - mysql
```

将上述配置保存后，在该文件所在目录运行：

```
docker-compose up -d
```

查看部署状态：

```
docker-compose ps
```

#### 项目特色功能

✨ 多模型支持：支持 30+ 种国内外主流大模型
🔄 负载均衡：自动分配请求到多个渠道
💾 数据持久化：支持 SQLite 和 MySQL 数据库
🔐 令牌管理：灵活的 API Key 管理和权限控制
📊 额度管理：精细化的用户额度和消费统计
🎨 主题定制：支持自定义系统主题和页面
🚀 高可用部署：支持多机部署和集群模式

#### 本篇教程结束

通过以上步骤，你已经成功部署了 One API，现在可以开始使用它来统一管理和调用各种大模型了。如有任何问题，欢迎查看官方文档或提交 Issue。

祝你使用愉快！🎉

---

原文：<https://www.passnat.com/posts/one-api-unified-model-interface-30-plus-models-openai-format-nas-deployment>
