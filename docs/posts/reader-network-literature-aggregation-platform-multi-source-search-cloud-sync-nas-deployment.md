---
title: "开箱即用网络文学聚合阅读平台 Reader，多源搜索云端同步，NAS部署无需公网远程阅读"
date: 2025-10-16
description: "Reader是功能强大的网络文学聚合阅读平台。支持多源搜索、在线播放、书架管理、阅读进度同步、WebDAV云端备份等丰富功能。界面简洁优雅，支持自定义主题样式。所有书籍资源开箱即用，可在NAS、个人服务器或云服务器部署。支持多用户模式、书籍分组、RSS订阅、定时更新等高级功能，满足不同用户多样化阅读需求。"
canonical: https://www.passnat.com/posts/reader-network-literature-aggregation-platform-multi-source-search-cloud-sync-nas-deployment
---

## Reader - 开箱即用的网络文学聚合阅读平台

#### 项目介绍

Reader 是一个功能强大的网络文学聚合阅读平台，采用现代化的Web技术栈构建。这是一款专为网络文学爱好者打造的全能阅读工具，无需依赖手机客户端，直接在浏览器中即可享受畅快的阅读体验。

项目支持多源搜索、在线播放、书架管理、阅读进度同步、WebDAV云端备份等丰富功能。界面设计简洁优雅，支持自定义主题和样式，让每位用户都能打造专属于自己的阅读空间。更重要的是，所有书籍资源均来自第三方网络文学网站，用户无需自备资源库，开箱即用。

无论是在家中NAS、个人服务器还是云服务器上部署，Reader都能为你提供稳定可靠的阅读服务。支持多用户模式、书籍分组、RSS订阅、定时更新等高级功能，满足不同用户的多样化需求。

项目地址：[点我跳转](https://github.com/hectorqin/reader)

#### 部署安装

如果你想在外网也能随时打开项目进行阅读，推荐使用内网穿透服务。可以考虑使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备后用。具体的操作步骤可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你轻松实现远程访问。

##### Docker 单命令快速启动（推荐小白用户）

这是最简单的部署方式，只需一条命令即可启动Reader服务。

打开你的NAS设备或服务器的终端，执行以下命令：

```bash
docker run -d --restart=always --name=reader -e "SPRING_PROFILES_ACTIVE=prod" -v $(pwd)/logs:/logs -v $(pwd)/storage:/storage -p 8080:8080 hectorqin/reader
```

命令说明：

-d 后台运行容器
--restart=always 容器异常退出时自动重启
--name=reader 容器名称
-e "SPRING\_PROFILES\_ACTIVE=prod" 生产环境配置
-v $(pwd)/logs:/logs 映射日志目录到本地
-v $(pwd)/storage:/storage 映射数据存储目录到本地（重要：保存书架、书源、阅读进度等数据）
-p 8080:8080 端口映射，前面的8080是宿主机端口，后面的8080是容器内端口

如果8080端口被占用，可以修改为其他端口：

```bash
docker run -d --restart=always --name=reader -e "SPRING_PROFILES_ACTIVE=prod" -v $(pwd)/logs:/logs -v $(pwd)/storage:/storage -p 4396:8080 hectorqin/reader
```

这样就会使用4396端口访问应用。

##### Docker-Compose 配置方式（功能更全面）

如果需要更灵活的配置选项，可以使用 Docker-Compose 方式部署。

第一步：下载配置文件

```
wget https://mirror.ghproxy.com/https://raw.githubusercontent.com/hectorqin/reader/master/docker-compose.yaml
```

第二步：编辑配置文件

使用文本编辑器打开 docker-compose.yaml 文件，根据需要调整以下配置：

```yaml
version: '3.8'

services:
  reader:
    image: hectorqin/reader:latest
    container_name: reader
    restart: always
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      # 单用户模式（默认）
      # 多用户模式需要设置以下参数
      # - READER_APP_SECURE=false
      # - READER_APP_INVITECODE=
      # - READER_APP_SECUREKEY=
    volumes:
      - ./logs:/logs                    # 日志目录
      - ./storage:/storage              # 数据存储目录（必须）
    ports:
      - "8080:8080"                     # 端口映射，修改前面的数字可改变访问端口
    networks:
      - reader-network

networks:
  reader-network:
    driver: bridge
```

第三步：启动服务

在 docker-compose.yaml 所在目录执行：

```
docker-compose up -d
```

第四步：查看日志

```bash
docker logs -f reader
```

#### 环境变量配置

Reader 支持通过环境变量进行灵活配置。以下是常用的环境变量说明：

必须配置：

SPRING\_PROFILES\_ACTIVE=prod 生产环境配置（必须）

可选配置（多用户模式）：

READER\_APP\_SECURE 是否启用多用户模式（true/false，默认false）
READER\_APP\_INVITECODE 注册邀请码（为空时开放注册）
READER\_APP\_SECUREKEY 管理密码（多用户模式下的后台管理密码）

⚠ 这两项要一起看：**多用户模式默认是关的，但一旦你把它打开、又把邀请码留空，注册就是敞开的**。这个实例挂到公网之后，任何拿到地址的人都能注册进来。所以要开多用户模式的话，先把 READER\_APP\_INVITECODE 设一个邀请码再挂公网。

其他可选参数：

READER\_APP\_CACLECHAPTERCONTENT 是否缓存章节内容（true/false，默认false）
READER\_APP\_DEBUGLOG 是否打开调试日志（true/false，默认false）
READER\_SERVER\_PORT 服务端口（默认8080）

大多数用户只需保持默认配置即可，无需额外设置。

#### 数据持久化说明

Reader 的所有数据都存储在 /storage 目录中，包括：

书源列表 - 你添加的所有书源
书架信息 - 你收藏的书籍和阅读进度
缓存数据 - 书籍封面和章节缓存
用户配置 - 阅读主题、样式等个性化设置

因此在部署时，**必须将 /storage 目录映射到宿主机**，以确保数据在容器重启后不会丢失。

#### 访问应用

部署完成后，打开浏览器访问：

```
http://你的IP:8080
```

如果修改了端口（例如改为4396），则访问：

```
http://你的IP:4396
```

稍等几秒钟，Reader 的精美界面就会呈现在你眼前。你可以开始添加书源、搜索书籍、建立个人书架，享受无限的阅读乐趣。

#### 功能特性一览

📚 多源搜索 - 聚合多个网络文学网站，一键搜索
🎨 自定义主题 - 支持多种阅读主题，自由定制样式
💾 云端同步 - WebDAV 备份，随时恢复阅读进度
📱 移动适配 - 完美支持手机、平板等各类设备
🔄 换源功能 - 书籍可随时切换阅读源
📖 多格式支持 - 支持导入 TXT、EPUB、PDF 等本地书籍
👥 多用户模式 - 支持多个用户独立使用
📻 RSS 订阅 - 订阅网络文学更新
🎵 听书功能 - 部分浏览器支持文字转语音
📊 定时更新 - 自动更新书架中的书籍

#### 常见问题

Q：如何更新 Reader 到最新版本？

A：执行以下命令：

```bash
docker pull hectorqin/reader:latest
docker-compose down
docker-compose up -d
```

Q：如何备份我的书架和阅读进度？

A：所有数据都保存在 ./storage 目录中，直接备份该目录即可。

Q：可以支持多个用户吗？

A：可以。启用多用户模式需要设置 READER\_APP\_SECURE=true，然后通过网页注册新用户。

Q：如何自定义阅读样式？

A：在 storage/assets/reader.css 文件中编写自定义 CSS 代码。

#### 总结

Reader 是一款功能完整、开箱即用的网络文学阅读平台。通过简单的 Docker 部署，你就能在自己的服务器或 NAS 上拥有一个私人的书籍聚合中心。无论是追更最新网络文学，还是收藏珍爱的电子书，Reader 都能满足你的需求。

立即开始部署，开启你的专属阅读之旅吧！

---

原文：<https://www.passnat.com/posts/reader-network-literature-aggregation-platform-multi-source-search-cloud-sync-nas-deployment>
