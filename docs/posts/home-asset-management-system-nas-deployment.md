---
title: "Homebox 飞牛 NAS 部署与公网查物品档案"
date: 2025-10-16
description: "家里每样东西建档存 NAS：说明书、保修、型号，出门手机搜索直达。包含单容器编排、公网首启注册管理员及飞牛 PassNAT 插件隧道步骤。"
canonical: https://www.passnat.com/posts/home-asset-management-system-nas-deployment
---

## 项目介绍

Homebox 是一个开源的家庭物品档案管理工具，把家里每样东西建一张卡片：说明书、保修到期日、型号、放在哪个房间，全部集中存在自己的 NAS 上。物品可以生成带二维码的标签，贴在机器上扫码直达；首页的统计卡会自动汇总物品总数和价值。

它由社区持续维护（sysadminsmedia 组织），官方提供 Docker 镜像，单容器就能跑，数据用 SQLite 存在本地文件夹，不需要配置任何外部服务账号。

项目源码：[sysadminsmedia/homebox](https://github.com/sysadminsmedia/homebox)

官方文档：[Homebox Docs](https://homebox.sysadminsmedia.com/)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 在外面的手机上查家里的物品档案。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 homebox；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  homebox:
    image: ghcr.io/sysadminsmedia/homebox:latest
    container_name: homebox
    restart: unless-stopped
    network_mode: bridge
    environment:
      - HBOX_OPTIONS_ALLOW_ANALYTICS=false
      - HBOX_AUTH_API_KEY_PEPPER=change-me-to-a-long-random-string-min-32-chars
    ports:
      - "3480:7745"
    volumes:
      - ./data:/data
```

3480 是 NAS 对外的端口，7745 是容器内的默认端口，后面创建隧道填 3480。./data 是数据目录，物品的照片附件和数据库都存在里面，用相对路径会在你刚选的项目文件夹里生成。

环境变量里 HBOX\_AUTH\_API\_KEY\_PEPPER 是官方要求的必填项，用来给接口密钥做哈希，要求至少 32 个字符，把示例里这串换成你自己随手编的一串长字符就行。HBOX\_OPTIONS\_ALLOW\_ANALYTICS 设为 false 表示不上报匿名统计。

勾选创建项目后立即启动，确认，等待容器运行。

## 二、创建隧道，再马上建管理员

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，本地 IP 保持默认，本地端口填 3480，其余按页面默认设置创建。按隧道这一行的电源，等待状态显示运行中。

打开详情，使用分配的公网地址访问。第一次打开会先要求注册管理员账号，用户名和密码自己设。隧道一开，理论上别人也可能先碰到这个页面，所以建好隧道之后先把管理员建好，不要拖。

## 三、给家里东西建档

登录后点新建物品，把名称、位置、描述填上，比如打印机的硒鼓型号写进描述，下次买耗材对着型号买就不会错。每样东西都能生成一张二维码标签，打印出来贴在机器上，手机扫码直达这一页。

物品可以上传照片和附件，说明书电子版直接挂在对应的卡片下面。首页的统计卡会按物品数量和价值自动汇总。

## 四、出门查档案

在外面用手机打开同一个公网地址，登录你自己刚创建的管理员账号，搜索型号或者名字，就能查到家里有没有这个东西、保修到哪天。全家人都可以建各自的账号一起维护这份档案。

## 数据与备份

所有数据都在 data 文件夹里（SQLite 数据库加照片附件），定期把整个文件夹复制到另一块盘或另一台设备，就是一份完整的备份。只给自己家里人用的话，可以在编排的 environment 里加一行 HBOX\_OPTIONS\_ALLOW\_REGISTRATION=false，建完管理员账号后重启容器，关闭公开注册。

---

原文：<https://www.passnat.com/posts/home-asset-management-system-nas-deployment>
