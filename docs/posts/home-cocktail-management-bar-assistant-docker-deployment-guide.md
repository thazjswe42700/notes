---
title: "家庭鸡尾酒管理系统Bar Assistant部署教程，Docker快速安装配置指南"
date: 2025-10-16
description: "发现完整的家庭鸡尾酒管理系统。Bar Assistant 提供500+专业配方、库存追踪、购物清单和多人协作功能。支持Docker一键部署，适合调酒师和酒吧爱好者快速筛选、评分和分享鸡尾酒配方。"
canonical: https://www.passnat.com/posts/home-cocktail-management-bar-assistant-docker-deployment-guide
---

## 项目介绍

Bar Assistant 是一个专为鸡尾酒爱好者量身打造的全能家庭酒吧管理系统。与其他通用的食谱管理软件不同，Bar Assistant 专注于鸡尾酒领域，提供了丰富的酒吧管理功能。

这个项目包含超过 500 种详细的鸡尾酒配方和 250+ 种基础酒类成分，支持多人协作管理酒吧、成分库存追踪、购物清单生成、配方评分与收藏、以及强大的搜索和过滤功能。无论你是专业调酒师还是家庭酒吧爱好者，都能轻松找到合适的配方，并根据现有的酒类材料快速筛选出能制作的鸡尾酒。

项目还支持配方导入、公开分享、价格计算、用户角色权限管理等高级功能，让你的家庭酒吧管理变得专业而高效。

项目地址： [点我跳转](https://github.com/karlomikus/bar-assistant)

## 部署安装

如果你想在家庭网络之外也能访问 Bar Assistant，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com)

注册账号，这样就能随时随地管理你的酒吧库存了。具体的操作步骤可以参考 [这份详细教程](https://www.passnat.com/posts/p)

### 使用 Docker Compose 快速部署

这是最推荐的安装方式，只需简单几步就能在你的 NAS 或服务器上运行完整的 Bar Assistant 系统。

**第一步：创建配置文件**

首先，在你想要存放项目的目录中，创建一个 `.env` 文件，用来存储基本的配置信息：

```
# Meilisearch 主密钥（用于搜索服务的安全认证）
MEILI_MASTER_KEY=masterKey-make-it-long-for-security

# Meilisearch 搜索服务地址
MEILISEARCH_URL=http://localhost:8081

# Bar Assistant API 服务地址
API_URL=http://localhost:8082
```

接着，在同一目录中创建 `docker-compose.yml` 文件，复制下面的完整配置：

```yaml
volumes:
  bar_data:
  meilisearch_data:

services:
  meilisearch:
    image: getmeili/meilisearch:v1.15
    environment:
      - MEILI_NO_ANALYTICS=true
      - MEILI_MASTER_KEY=$MEILI_MASTER_KEY
      - MEILI_ENV=production
    restart: unless-stopped
    volumes:
      - meilisearch_data:/meili_data
    ports:
      - "8081:7700"

  # 可选，但强烈推荐使用（提升性能）
  redis:
    image: redis
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
    restart: unless-stopped

  bar-assistant:
    image: barassistant/server:v5
    depends_on:
      - meilisearch
      - redis
    environment:
      - APP_URL=$API_URL
      - MEILISEARCH_KEY=$MEILI_MASTER_KEY
      - MEILISEARCH_HOST=http://meilisearch:7700
      - REDIS_HOST=redis
      - CACHE_DRIVER=redis
      - SESSION_DRIVER=redis
      - ALLOW_REGISTRATION=true
    restart: unless-stopped
    ports:
      - "8082:8080"
    volumes:
      - bar_data:/var/www/cocktails/storage/bar-assistant

  salt-rim:
    image: barassistant/salt-rim:v4
    depends_on:
      - bar-assistant
    environment:
      - API_URL=$API_URL
      - MEILISEARCH_URL=$MEILISEARCH_URL
    restart: unless-stopped
    ports:
      - "8080:8080"
```

**第二步：启动服务**

打开你的 NAS 或服务器的 Docker 管理界面，将上面的配置文件放入相应目录后，执行以下命令启动所有服务：

```bash
docker compose up -d
```

稍等片刻（通常需要 1-2 分钟），服务就会完全启动。你可以通过查看 Docker 日志来确认服务是否正常运行：

```bash
docker compose logs bar-assistant
```

当看到 'Application ready' 的提示信息时，说明 Bar Assistant 已经准备好了。

**第三步：访问应用**

打开浏览器，访问 `http://localhost:8080`（如果是远程访问，将 localhost 替换成你的服务器 IP 地址），就能看到 Bar Assistant 的前端界面了。

首次访问时，你需要点击注册按钮创建一个新账户，然后就可以开始使用了。

**端口说明**

8080：Web 前端界面（Salt Rim）- 这是你主要使用的端口

8081：Meilisearch 搜索服务 - 通常不需要直接访问

8082：Bar Assistant API 服务 - 后端 API 接口

如果你的系统中这些端口已经被占用，可以修改 `docker-compose.yml` 中的 `ports` 字段。例如，如果 8080 被占用，可以改为 `'8888:8080'` 来使用 8888 端口访问。

**环境变量说明**

上面的配置中已经包含了所有必需的环境变量，这些都是开箱即用的设置：

MEILI\_MASTER\_KEY：搜索服务的安全密钥，建议改成更复杂的字符串

ALLOW\_REGISTRATION：允许新用户注册（设置为 true）

CACHE\_DRIVER 和 SESSION\_DRIVER：使用 Redis 来提升性能

如果你不想使用 Redis，可以将这两项改为 `file`，但这样性能会有所下降。

**数据持久化**

配置中的 `bar\_data` 和 `meilisearch\_data` 卷会自动保存你所有的酒吧数据、配方和搜索索引，确保重启容器后数据不会丢失。

本篇教程结束。现在你已经拥有一个完整的家庭鸡尾酒配方管理系统了。尽情享受探索各种鸡尾酒配方的乐趣吧！🍸

---

原文：<https://www.passnat.com/posts/home-cocktail-management-bar-assistant-docker-deployment-guide>


## 相关文章

- [Home Assistant 自建智能家居中枢：飞牛 NAS Docker 部署官方镜像，不同牌子的设备一页全管，内网穿透出门手机也能控](home-assistant-nas-smart-home-hub-docker-deployment-intranet-penetration.md)
- [自建网络电台完整方案 AzuraCast Docker 快速部署指南 NAS 轻松搭建](azuracast-docker-nas-wangluo-diantai-kuaisushubu.md)
- [高效文本分享平台Hasty Paste II，Docker快速部署NAS私有服务，支持内网穿透远程访问](hasty-paste-ii-docker-nas-private-service-intranet-penetration.md)
- [Redmine开源项目管理系统Docker部署指南，NAS快速安装配置，内网穿透远程访问完整教程](redmine-docker-deployment-nas-installation-intranet-penetration-remote-access.md)
- [开源个人关系管理系统Monica私密部署指南，NAS一键Docker快速安装配置教程](open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install.md)
