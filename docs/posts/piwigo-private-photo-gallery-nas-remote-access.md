---
title: "自建私人相册库 Piwigo 开源免费，内网穿透远程访问，NAS 极简部署"
date: 2025-10-16
description: "Piwigo是功能强大的开源照片库管理系统，支持批量上传、相册组织、标签管理、用户权限控制等丰富功能。完全开源免费，可自行部署在服务器上，享受完全的数据控制权。适合摄影爱好者、组织和个人使用。"
canonical: https://www.passnat.com/posts/piwigo-private-photo-gallery-nas-remote-access
---

## 项目介绍

Piwigo 是一款功能强大的开源照片库管理系统，专为网络设计，适合组织、团队和个人使用。它提供了一个完整的相册管理解决方案，支持批量上传、相册组织、标签管理、用户权限控制、评论评分等丰富功能。无论你是摄影爱好者想要展示作品，还是组织需要管理海量图片资源，Piwigo 都能满足你的需求。

界面简洁易用，功能配置灵活，社区活跃友好。最重要的是，它完全开源免费，你可以自己部署在服务器上，享受完全的数据控制权。

项目地址： [点我跳转](https://piwigo.org/)

## 部署安装

如果需要在外网也能访问你的相册库，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在家外也能随时查看和管理你的相册。详细的配置步骤可以 [参考这里](https://www.passnat.com/posts/p)，有全平台的上手指南。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，只需复制以下配置文件即可：

```yaml
version: '3'
services:
  piwigo:
    image: lscr.io/linuxserver/piwigo:latest
    container_name: piwigo
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
    volumes:
      - /path/to/piwigo/config:/config
      - /path/to/piwigo/gallery:/gallery
    ports:
      - 80:80
    restart: unless-stopped
```

**部署步骤：**

1. 打开你的 NAS 设备（飞牛、群晖、威联通等）或 Linux 服务器

2. 创建必要的文件夹用于存储配置和相册：

/path/to/piwigo/config - 用于存储 Piwigo 的配置文件和数据库信息

/path/to/piwigo/gallery - 用于存储你上传的所有相册和图片

3. 将上述 docker-compose 配置保存为 'docker-compose.yml' 文件

4. 在配置文件所在目录运行以下命令启动容器：

```
docker-compose up -d
```

5. 等待容器启动完成后，在浏览器中访问 'http://你的IP地址' 即可打开 Piwigo 相册库

**环境变量说明：**

**必须设置：**

'PUID=1000' - 用户ID（用于文件权限管理）

'PGID=1000' - 组ID（用于文件权限管理）

**可选设置：**

'TZ=Asia/Shanghai' - 时区设置，根据你的地理位置调整

**端口说明：**

默认配置使用 80 端口。如果你的系统中 80 端口已被占用，可以修改为其他端口，例如：

```
ports:
  - 8080:80
```

这样就可以通过 'http://你的IP地址:8080' 访问。记住冒号前面的是外部端口，冒号后面的是容器内部端口，只需修改前面的数字即可。

**存储说明：**

/config 目录用于存储 Piwigo 的配置、插件和数据库配置文件，需要持久化保存

/gallery 目录用于存储你上传的所有图片和相册数据，这是最重要的目录，务必定期备份

### 使用 Docker 命令行快速启动

如果你更喜欢直接使用 Docker 命令，可以运行：

```bash
docker run -d \
  --name=piwigo \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Shanghai \
  -p 80:80 \
  -v /path/to/piwigo/config:/config \
  -v /path/to/piwigo/gallery:/gallery \
  --restart unless-stopped \
  lscr.io/linuxserver/piwigo:latest
```

**注意事项：**

请根据你的实际路径替换 '/path/to/piwigo/config' 和 '/path/to/piwigo/gallery'

确保这两个目录存在，如果不存在请先创建

如果端口冲突，可以将 '-p 80:80' 改为 '-p 8080:80' 等其他端口

启动成功后，打开浏览器访问 'http://你的IP地址'（或 'http://你的IP地址:8080' 如果你修改了端口），就可以开始使用 Piwigo 了。首次访问会引导你完成初始化设置。

**到此为止，Piwigo 相册库已经成功部署！**

你现在拥有了一个功能完整的私人相册管理系统。上传你的照片，邀请家人朋友查看，享受属于你自己的图片库吧！

---

原文：<https://www.passnat.com/posts/piwigo-private-photo-gallery-nas-remote-access>
