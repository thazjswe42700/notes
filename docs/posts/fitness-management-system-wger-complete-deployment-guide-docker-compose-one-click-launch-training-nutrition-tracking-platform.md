---
title: "自建健身管理系统wger完整部署指南，Docker Compose一键启动训练营养追踪平台"
date: 2025-10-16
description: "wger是完全免费开源的健身营养管理平台，采用Django+PostgreSQL+Redis架构。提供自定义训练计划、营养追踪、体重管理、进度相册等功能。支持多用户管理，适合个人或健身房使用。所有功能完全免费，可自主部署掌控数据。Docker Compose一键部署，轻松在NAS或Linux服务器上运行。"
canonical: https://www.passnat.com/posts/fitness-management-system-wger-complete-deployment-guide-docker-compose-one-click-launch-training-nutrition-tracking-platform
---

## 项目介绍

wger 是一个完全免费且开源的健身与营养管理平台，采用 Django + PostgreSQL + Redis 架构构建。它提供了一个全面的健身解决方案，包括自定义训练计划、营养追踪、体重管理、进度相册等功能。

无论你是健身新手还是资深爱好者，wger 都能帮你制定个性化的训练方案，记录每一次的进步。项目支持多用户管理，适合个人使用或小型健身房运营。最棒的是，所有功能完全免费，且可以自主部署在自己的服务器上，完全掌控你的数据。

项目地址： [点我跳转](https://github.com/wger-project/wger)

## 部署安装

如果你想在家里或公司随时随地访问 wger，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先完成账号注册。具体的配置步骤可以参考 [帕斯内网穿透全平台使用指南](https://www.passnat.com/posts/p)，轻松实现外网访问。

### Docker Compose 一键部署

这是最快速、最推荐的部署方式。wger 官方提供了完整的 Docker Compose 配置，只需一条命令就能启动完整的应用栈。

第一步：获取配置文件

打开你的 NAS 设备（飞牛 NAS、群晖、威联通等）或 Linux 服务器，创建一个项目文件夹，然后从 GitHub 克隆官方配置：

```bash
git clone https://github.com/wger-project/docker.git wger
cd wger
```

第二步：启动应用

进入项目目录后，执行以下命令即可启动所有服务：

```bash
docker compose up -d
```

这条命令会自动下载并启动以下服务：

Web 应用 - wger 主应用服务
Nginx - 反向代理和静态文件服务
PostgreSQL - 数据库服务
Redis - 缓存和消息队列服务
Celery Worker & Beat - 后台任务处理服务

第三步：配置环境变量

进入 'config' 文件夹，编辑 'prod.env' 文件进行必要的配置。以下是一些重要的环境变量说明：

必须修改的关键变量：

SECRET\_KEY - Django 密钥，建议改为 50 个字符的随机字符串（可使用 https://djecrety.ir/ 生成）
SIGNING\_KEY - JWT 签名密钥，必须与 SECRET\_KEY 不同
SITE\_URL - 你的访问域名或 IP 地址，例如 'http://192.168.1.100' 或 'https://wger.yourdomain.com'

可选但推荐配置：

TIME\_ZONE - 时区设置，默认为 'Europe/Berlin'，改为 'Asia/Shanghai' 适配中国时区
CSRF\_TRUSTED\_ORIGINS - 如果使用自定义域名，需要配置此项以避免 CSRF 错误
ALLOW\_REGISTRATION - 是否允许用户注册，默认为 'True'
ALLOW\_GUEST\_USERS - 是否允许游客用户，默认为 'True'
ALLOW\_UPLOAD\_VIDEOS - 是否允许上传视频，默认为 'True'

⚠ 上面这两项默认都是 True，意味着**装好之后任何人都能在你的实例上注册，还允许游客账号**。如果你要用内网穿透把它开放到公网，建议在编排里就把 ALLOW\_REGISTRATION 和 ALLOW\_GUEST\_USERS 设成 False，而不是等挂上去之后再改。

数据库配置（通常无需修改）：

DJANGO\_DB\_PASSWORD - 数据库密码，默认为 'wger'（建议改为强密码）
CELERY\_FLOWER\_PASSWORD - Celery 监控面板密码，默认为 'adminadmin'

其他变量保持默认即可，系统会自动处理同步训练计划、食物数据库等功能。

第四步：访问应用

启动完成后，打开浏览器访问：

```
http://你的IP地址:80
```

或者如果你配置了域名，直接访问你的域名即可。首次访问会看到 wger 的欢迎界面，你可以创建账户开始使用。

第五步：后续管理

查看日志：'docker compose logs -f web'
停止服务：'docker compose down'
更新应用：'docker compose pull && docker compose up -d'
重启服务：'docker compose restart'

### 端口说明

80 端口 - Nginx 反向代理，这是主要的访问端口
5432 端口 - PostgreSQL 数据库（容器内部使用，无需暴露）
6379 端口 - Redis 缓存（容器内部使用，无需暴露）

如果你的 80 端口已被占用，可以在 'docker-compose.yml' 文件中修改 nginx 的端口映射。找到以下部分：

```
ports:
  - "80:80"
```

改为你想要的端口，例如 '"8080:80"'，然后重新启动服务即可。

### 数据持久化

wger 会自动创建以下数据卷来保存你的数据：

postgres-data - 数据库数据
redis-data - 缓存数据
celery-beat - 定时任务数据
static - 静态文件
media - 用户上传的文件（如进度照片）

这些数据会永久保存在 Docker 卷中，即使容器重启也不会丢失。

## 总结

通过以上步骤，你已经成功部署了一个完整的 wger 健身管理系统。这个系统包含了训练计划制定、营养追踪、进度记录等所有功能，完全可以满足个人或小型健身房的需求。

如果遇到任何问题，可以查看官方文档 https://wger.readthedocs.io 或在 GitHub 提交 Issue。祝你健身愉快！

---

原文：<https://www.passnat.com/posts/fitness-management-system-wger-complete-deployment-guide-docker-compose-one-click-launch-training-nutrition-tracking-platform>


## 相关文章

- [家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆](photostructure-nas-private-photo-management-remote-access.md)
- [WPS Office 浏览器在线办公：NAS Docker 部署 + 内网穿透](wps-office-nas-docker-browser-online-office-intranet-penetration.md)
- [Webtop：NAS 上的网页桌面，Docker 部署 + 内网穿透](webtop-nas-browser-linux-desktop-docker.md)
- [企业级协作服务开箱即用，私有部署支持Docker与Kubernetes，内网穿透远程访问](enterprise-collaboration-service-docker-kubernetes-intranet-penetration.md)
- [阿里云盘CLI命令行工具NAS部署指南，本地文件云端备份同步方案详解](aliyun-pan-cli-nas-deployment-guide-local-cloud-backup-sync-solution.md)
