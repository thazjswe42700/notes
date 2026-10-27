---
title: "Mealie 飞牛 NAS 部署与手机采购清单"
date: 2025-10-16
description: "家庭食谱存在自己的 NAS 上，外出用手机查看采购清单。包含单容器编排、默认管理员修改及飞牛 PassNAT 插件隧道步骤。"
canonical: https://www.passnat.com/posts/mealie-private-recipe-library-nas-deployment-35-languages-remote-access
---

## 项目介绍

Mealie 是可以部署在自己 NAS 上的家庭菜谱本。把常做的菜、食材和做法集中保存，再把要买的材料写进购物清单。到超市打开手机，同一份清单拿到一样勾一样，回家做菜再打开食谱。

本期在飞牛 NAS 上使用单容器部署，实测版本为 v3.28.0，界面可使用中文。手动写食谱和购物清单不需要配置 AI 服务。

项目源码：[Mealie](https://github.com/mealie-recipes/mealie)

官方安装说明：[Installation Checklist](https://mealie.io/documentation/getting-started/installation/installation-checklist/)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 在手机浏览器中访问。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 mealie；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  mealie:
    image: ghcr.io/mealie-recipes/mealie:latest
    container_name: mealie
    network_mode: bridge
    restart: unless-stopped
    ports:
      - "3450:9000"
    mem_limit: 768m
    volumes:
      - ./data:/app/data
    environment:
      ALLOW_SIGNUP: "false"
      PUID: "1000"
      PGID: "1000"
      TZ: Asia/Shanghai
      BASE_URL: http://localhost:3450
```

3450 是 NAS 对外的端口，9000 是容器内的端口。后面创建隧道填 3450。./data 是相对于刚才所选项目文件夹的数据目录，对应容器内的 /app/data。TZ 使用 Asia/Shanghai。

ALLOW\_SIGNUP 设为 false，关闭开放注册。BASE\_URL 在本期保持 http://localhost:3450；它用于通知链接和 OIDC 回调，本期不配置这些功能。

环境变量说明：[Backend Configuration](https://mealie.io/documentation/getting-started/installation/backend-config/)

勾选创建项目后立即启动，确认，等待容器运行。

## 二、先更改默认管理员，再开放公网

启动后先在局域网中登录一次，初始账号信息参见上面的官方安装说明。首次登录会进入 /admin/setup，按页面更改管理员用户名、邮箱和密码；公开访问保持关闭。请完成这些设置之后再启动公网隧道，不要使用默认账号开放到公网。

初始化中的 AI 配置可以直接点下一步跳过，手动创建食谱和购物清单无需填写 API key。视频中管理员登录及改密码已在镜头外完成。

## 三、在飞牛 PassNAT 插件创建隧道

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，本地 IP 保持默认，本地端口填 3450，其余按页面默认设置创建。

按隧道这一行的电源，等待状态显示运行中。打开详情，使用分配的公网地址访问 Mealie；手机在外面也打开这个地址，并使用你刚刚修改后的账号。

## 四、手动保存家庭食谱

点击创建，选择手动创建食谱。在「食谱名称」里填菜名，例如番茄炒蛋，然后点击创建。进入食谱后编辑食材和做法，完成后点右上角保存。

## 五、用购物清单安排一次采购

打开购物清单，创建一份本次采购的清单。点击添加项目，选择食品，填写数量，然后点击保存（磁盘图标）。例如把番茄和鸡蛋分别加入清单，每项保存后再添加下一项。

到超市用手机打开同一个公网地址，进入这份清单，拿到一样就勾选对应项目。勾选状态会保存，刷新页面后仍然保留，继续按剩余项目采购即可。

## 数据与备份

项目数据持久化到所选项目文件夹下的 data 目录。使用 Mealie 自带的备份功能生成备份，再把备份保存到另一处。

---

原文：<https://www.passnat.com/posts/mealie-private-recipe-library-nas-deployment-35-languages-remote-access>


## 相关文章

- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
- [Grocy 飞牛 NAS 部署与公网查库存](grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory.md)
- [qBittorrent 飞牛 NAS 部署与远程下载](qbittorrent-nas-remote-bt-downloader-deploy-intranet-penetration.md)
- [Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看](jellyfin-nas-media-server-private-cinema.md)
