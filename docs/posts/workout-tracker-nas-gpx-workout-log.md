---
title: "workout-tracker 飞牛 NAS 部署：GPX 轨迹库与运动统计"
date: 2026-10-04
description: "单容器部署 workout-tracker：手表导出的 GPX/TCX/FIT 集中存档，自动算配速心率爬升，统计图表与热力图，家人多账号各自记录。包含编排与 PassNAT 隧道步骤。"
canonical: https://www.passnat.com/posts/workout-tracker-nas-gpx-workout-log
---

## 项目介绍

workout-tracker 是一个部署在自己 NAS 上的运动记录库，把手表、手机导出的轨迹文件集中存一份。支持 GPX、TCX、FIT 三种格式上传，跑步、骑行、徒步等类型会自动识别，距离、配速、心率、踏频、爬升和消耗卡路里都会自动算好。

上传之后每条记录有地图路线、平均速度和海拔曲线、爬坡分段，以及每一公里的平均心率和步频明细；统计页把一段时间里的运动次数、时长、距离和速度画成图表，热力图可以把所有轨迹叠在一张地图上。界面语言跟随浏览器，包含简体中文。支持多用户：家人自己注册账号，管理员激活之后各记各的数据。

项目源码：[jovandeginste/workout-tracker](https://github.com/jovandeginste/workout-tracker)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 在外网打开自己的运动记录。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 workout；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  workout:
    image: ghcr.io/jovandeginste/workout-tracker:latest
    container_name: workout
    user: "1000:1000"
    network_mode: bridge
    environment:
      - TZ=Asia/Shanghai
      - WT_JWT_ENCRYPTION_KEY=RunDemo2026Key7x2k
    ports:
      - "3484:8080"
    volumes:
      - ./data:/data
    restart: unless-stopped
```

3484 是 NAS 对外的端口，8080 是镜像里固定的容器端口。WT\_JWT\_ENCRYPTION\_KEY 是登录会话的加密密钥，不设置的话每次重启都会随机生成、所有登录失效，所以这里自己编一串随机字符换掉示例值。数据库用的是内置 SQLite，单容器就够，不需要额外的数据库服务，也不用申请任何第三方密钥。network\_mode 使用 bridge，与其他容器的编排互不影响。user 一行让容器以普通用户身份写数据，避免挂载目录里出现 root 文件。

勾选创建项目后立即启动，确认，等待镜像拉取完成后容器开始运行。

## 二、在飞牛 PassNAT 插件创建隧道

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，本地 IP 保持默认（127.0.0.1），本地端口填 3484，其余按页面默认设置创建。

按隧道这一行的电源，等待状态显示运行中，用分配的公网地址就能打开自己的运动记录库；出门在外用手机浏览器打开同一个地址，记录照样看得到。

## 三、初始化与导入轨迹

第一次登录用默认账号 admin，密码也是 admin，登录后第一件事就是改密码：进管理、打开管理用户、点 admin 编辑，填新密码保存。家人想一起记的话，在登录页用「创建新账号」自己注册，管理员在管理用户里点激活即可，之后每个人的记录相互独立。

导入轨迹：锻炼记录页点添加锻炼，选择文件时可以一次选多个 GPX，类型保持自动检测，提交后几秒钟就解析完成。地图、速度海拔曲线、每公里明细和卡路里都在记录详情页里。

## 数据与隐私

所有轨迹和统计数据都存在 NAS 上项目目录的 data 文件夹里（SQLite 单文件），备份这个目录就等于备份了全部记录。不需要把任何运动数据交给第三方服务。

---

原文：<https://www.passnat.com/posts/workout-tracker-nas-gpx-workout-log>
