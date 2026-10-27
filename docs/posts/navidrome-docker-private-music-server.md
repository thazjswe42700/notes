---
title: "Navidrome 飞牛 NAS 部署与公网听歌"
date: 2025-10-16
description: "自己的音乐文件集中放在 NAS 上，在外面用手机也能听自己的歌单。包含单容器编排、公网首启建管理员及飞牛 PassNAT 插件隧道步骤。"
canonical: https://www.passnat.com/posts/navidrome-docker-private-music-server
---

## 项目介绍

Navidrome 是一个开源的私人音乐库和流媒体服务，把你的音乐文件集中放在自己的 NAS 上，浏览器打开就能听。专辑、歌手、随机播放、收藏这些功能都是现成的，网页之外还兼容各种 Subsonic 协议的客户端应用。

本期在飞牛 NAS 上使用单容器部署，实测版本为 v0.64.2，界面可以在设置里切换成中文。它不需要配置任何外部服务账号，把音频文件放进音乐文件夹就能自动扫描入库。

项目源码：[navidrome](https://github.com/navidrome/navidrome)

官方文档：[Navidrome Documentation](https://www.navidrome.org/docs/)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 在外面的手机上听自己的歌单。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 navidrome；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    network_mode: bridge
    ports:
      - "3477:4533"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - "./music:/music"
      - "./data:/data"
    restart: unless-stopped
```

3477 是 NAS 对外的端口，4533 是容器内的默认端口。后面创建隧道填 3477。./music 是放歌的文件夹，./data 存配置和数据库，两个都是相对路径，会在你刚才选的项目文件夹里生成。TZ 使用 Asia/Shanghai。

勾选创建项目后立即启动，确认，等待容器运行。

## 二、创建隧道，再马上建管理员

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，本地 IP 保持默认，本地端口填 3477，其余按页面默认设置创建。按隧道这一行的电源，等待状态显示运行中。

打开详情，使用分配的公网地址访问。第一次打开会先要求创建管理员账号，用户名和密码自己设。隧道一开，理论上别人也可能先碰到这个页面，所以建好隧道之后先把管理员建好，不要拖。

登录后打开右上角的用户设置，把 Language 切换成简体中文，界面就变成中文了。

## 三、把歌放进 music 文件夹

打开飞牛的文件管理，找到刚才项目文件夹下的 music 目录，把你的音频文件复制进去就行，数量不限。Navidrome 会自动扫描，回到网页刷新一下，专辑和歌手就都出来了。

专辑封面和歌手名取决于文件自带的标签信息，用播放器或标签工具把歌名、歌手、专辑写进文件里，界面就会整齐很多。

## 四、出门听自己的歌单

在外面用手机打开同一个公网地址，登录你自己刚创建的管理员账号（或者再建一个普通账号给家人用），点开一张专辑就能播放。听到一半停下来，下次打开接着听。

## 数据与备份

歌曲本体在 music 文件夹里，Navidrome 的配置和数据库在 data 文件夹里。定期把这两个文件夹复制到另一块盘或另一台设备，就是一份完整的备份。

---

原文：<https://www.passnat.com/posts/navidrome-docker-private-music-server>


## 相关文章

- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
- [Mealie 飞牛 NAS 部署与手机采购清单](mealie-private-recipe-library-nas-deployment-35-languages-remote-access.md)
- [Grocy 飞牛 NAS 部署与公网查库存](grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory.md)
- [qBittorrent 飞牛 NAS 部署与远程下载](qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration.md)
- [Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看](jellyfin-nas-media-server-private-cinema.md)
