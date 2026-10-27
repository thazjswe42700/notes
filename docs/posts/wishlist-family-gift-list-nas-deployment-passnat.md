---
title: "Wishlist 全家礼物想单存 NAS"
date: 2026-10-14
description: "Wishlist 装在飞牛 NAS Docker，用 PassNAT 开放到公网；含 ORIGIN 必填、公开注册必关、单容器编排、PassNAT 隧道创建、首次访问建管理员。"
canonical: https://www.passnat.com/posts/wishlist-family-gift-list-nas-deployment-passnat
---

## 项目介绍

Wishlist 是一个开源的全家礼物想单工具：每个人建自己的清单，互相看不见对方买啥，避免撞车。每个清单可以生成一个公开链接发给家人，对方点开看中点认领（输入名字即可），认领人也看不到别人认领了谁。它由 cmintey 单人维护，仓库公开（cmintey/wishlist），官方提供 Docker 镜像，单容器跑、SQLite 存数据、不需要任何外部服务账号。

项目地址：[点我跳转](https://github.com/cmintey/wishlist)

## 部署安装

下面用飞牛 Docker Compose 部署 Wishlist，再用 [帕斯内网穿透](https://passnat.com) 在外面的手机上也能看家里的礼物想单。

### 一、在飞牛 Docker 新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 wishlist，路径在「我的文件」里挑一个空文件夹，没有就当场新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  wishlist:
    image: ghcr.io/cmintey/wishlist:latest
    container_name: wishlist
    restart: unless-stopped
    network_mode: bridge
    environment:
      - ORIGIN=http://naswish.odn.cc
      - PUBLIC_SIGNUP=false
    ports:
      - "3280:3000"
    volumes:
      - ./data:/usr/src/app/data
```

几个认的地方：3280 是 NAS 对外暴露的端口，映射到容器内的 3000；ORIGIN 这一行要填公网地址，等会建好隧道回头再补，填错 JWT / 重置链接会指向错的 origin；PUBLIC\_SIGNUP=false 必须设——Wishlist 默认是公开注册 + first-user-is-admin，公网开着等于自杀；./data 是 SQLite 数据目录。

### 二、创建隧道，公网地址拿回来填 ORIGIN

飞牛的「应用中心」里搜 PassNAT，装上穿透插件，打开它，点「创建隧道」。隧道名字起 wishlist（12 个字符以内），接入节点和协议留默认，本地 IP 保持 127.0.0.1，本地端口填 3280，「协议」选 HTTP，「子域名」起一个 odn.cc 还没用过的（比如 naswish），「顶级域名」选 odn.cc，提交。建好后按一下电源键，绿点亮了就是通了——这时把详情里的公网地址复制下来，回头填进上面编排 ORIGIN 那一行，重启容器。

### 三、第一次访问建管理员

用公网地址打开它，第一次会自动进入首启向导：用户名 / 邮箱 / 密码，提交。因为编排里把 PUBLIC\_SIGNUP=false 设了，这个首启向导是唯一能创建管理员的入口，建完之后别人就进不来了。建好之后首页应该是空的——现在轮到我们建清单了。

### 四、建清单 + 加物品

登录后右上角点 New List 起一个清单，比如「家人生日」。进清单点 Add 加几样东西，每样东西能填名称 + 链接 + 价格范围 + 图片。加好后右上角点「Copy link」拿到一个公开链接，发给家人。家人打开链接看中点「Claim」输入一个名字就算认领了，认领人不会被其他人看到。

### 几个模式可以挑

Wishlist 不止一种用法：

• 单清单模式（Registry Mode）：建一个清单就完事，公开链接直接给所有家人。适合「今年生日礼物」这种一次性场景。

• 多 Group 模式：建家庭组 / 朋友组，每个组独立的清单，互相之间看不到。最常用，演示走这个。

• 惊喜模式（Surprise Me）：建议人对物品不可见——你给别人提建议，但建议人不知道你提了什么。适合「送礼人不想被发现」的场景。

### 安全与备份

Wishlist 单容器，SQLite 存 ./data 文件夹。备份就是把这个文件夹整个复制到另一块盘——包括数据库和上传的图片。

公网部署一定记得 PUBLIC\_SIGNUP=false + ORIGIN 填公网地址。两个都设了之后，外部只能通过首启创建的管理员账号登录。

---

原文：<https://www.passnat.com/posts/wishlist-family-gift-list-nas-deployment-passnat>


## 相关文章

- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Mealie 飞牛 NAS 部署与手机采购清单](mealie-private-recipe-library-nas-deployment-35-languages-remote-access.md)
- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
- [qBittorrent 飞牛 NAS 部署与远程下载](qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration.md)
- [Grocy 飞牛 NAS 部署与公网查库存](grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory.md)
