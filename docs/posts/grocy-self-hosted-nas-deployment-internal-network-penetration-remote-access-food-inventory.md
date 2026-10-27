---
title: "Grocy 飞牛 NAS 部署与公网查库存"
date: 2025-10-16
description: "家里吃的剩多少、哪天过期，记在自己 NAS 上的 Grocy 里，出门手机一查就知道。包含单容器编排、初始口令修改、中文界面及飞牛 PassNAT 插件隧道步骤。"
canonical: https://www.passnat.com/posts/grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory
---

## 项目介绍

Grocy 是一个开源的家庭管家系统，把家里吃的东西、家务、购物清单这些琐事集中记在自己的 NAS 上。冰箱里还剩多少、哪天到期，打开网页一眼看完；快过期的它会自己标红提醒。

它用 PHP + SQLite 构建，单容器就能跑，不需要配置任何外部服务账号，数据全部存在本地。界面支持简体中文，还提供完整的 REST API 和条码扫描。本期在飞牛 NAS 上实测部署，从建库、进货记库存，到出门在超市用手机查一眼。

项目源码：[grocy](https://github.com/grocy/grocy)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 在外面的超市用手机查家里的库存。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 grocy；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  grocy:
    image: lscr.io/linuxserver/grocy:latest
    container_name: grocy
    network_mode: bridge
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
    ports:
      - "3493:80"
    volumes:
      - ./config:/config
    restart: unless-stopped
```

3493 是 NAS 对外的端口，80 是容器内的网页端口，后面创建隧道填 3493。./config 存配置和数据库，是相对路径，会在你刚选的项目文件夹里生成，备份它就等于备份了全部数据。TZ 使用 Asia/Shanghai。

勾选创建项目后立即启动，确认，等待容器运行。

## 二、创建隧道，马上改掉初始口令

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，本地 IP 保持默认，本地端口填 3493，其余按页面默认设置创建。按隧道这一行的电源，等待状态显示运行中。

打开详情，使用分配的公网地址访问。首次登录用初始账号 admin / admin，登录后点右上角用户菜单里的修改密码，马上换成自己的密码——隧道一开别人也可能碰到这个页面，这一步别拖。

再到用户设置里把 Language 切换成 Chinese (China)，整个界面就是简体中文了。

## 三、把吃的记进库存

打开采购页，产品框里输入名字（比如纯牛奶），选创建新产品，填好数量、到期日期和位置（冰箱），确定就入库了。库存总览里每行都标着数量和下次到期，快到期的整行变黄，顶上还有「N 款产品到期」的统计。

快吃完的时候用消耗按钮扣掉，买了一批就用采购再进一次。库存数字始终跟你家冰箱里的实际数量对得上。

## 四、家务和购物清单

家务总览里可以建周期性的家务，比如倒垃圾每 3 天一次，到期会出现在待办里，做完点一下完成。购物清单随手记该买的东西，到了超市照着勾。

## 五、出门在超市查一眼

在外面用手机打开同一个公网地址，登录你自己的账号，库存还剩什么、哪天过期一眼看完；想买什么当场记进购物清单，到家采购入库，闭环就形成了。

## 数据与备份

所有数据都在项目文件夹的 config 目录里，定期把这个文件夹复制到另一块盘或另一台设备，就是一份完整的备份。

---

原文：<https://www.passnat.com/posts/grocy-self-hosted-nas-deployment-internal-network-penetration-remote-access-food-inventory>
