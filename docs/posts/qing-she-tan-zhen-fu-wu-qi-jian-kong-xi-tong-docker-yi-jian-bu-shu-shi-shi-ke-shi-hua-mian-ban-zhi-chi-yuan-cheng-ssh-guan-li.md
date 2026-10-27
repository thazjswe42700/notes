---
title: "青蛇探针服务器监控系统，Docker一键部署实时可视化面板，支持远程SSH管理"
date: 2025-10-16
description: "青蛇探针是基于Node.js开发的高颜值云探针和服务器监控解决方案，提供实时多服务器监控、全球分布地图、在线SSH管理、SFTP文件编辑、流量统计分析等功能。支持批量命令执行、自定义分享、推送告警通知，让服务器管理简单高效。本文详细讲解Docker一键部署方案、端口配置、数据持久化等操作步骤。"
canonical: https://www.passnat.com/posts/qing-she-tan-zhen-fu-wu-qi-jian-kong-xi-tong-docker-yi-jian-bu-shu-shi-shi-ke-shi-hua-mian-ban-zhi-chi-yuan-cheng-ssh-guan-li
---

## 项目介绍

serverMmon（青蛇探针）是一款基于 Node.js 开发的高颜值云探针和服务器监控解决方案。它提供了一个酷炫高逼格的可视化界面，能够实时监控多台服务器的运行状态，支持全球服务器分布地图展示、在线SSH管理、SFTP文件编辑、流量统计分析等功能。

无论你是运维人员还是服务器爱好者，青蛇探针都能帮助你轻松掌握所有服务器的实时动态。它不仅提供了强大的监控能力，还支持批量命令执行、自定义分享地址、推送告警通知等高级功能，让服务器管理变得简单高效。

项目地址： [点我跳转](https://github.com/souying/serverMmon)

## 部署安装

如果你需要在外网也能访问到你的服务器监控面板，建议配置内网穿透功能。你可以选择使用 [帕斯内网穿透](https://passnat.com) 这样的专业服务，先注册好账号以备使用。具体的操作步骤可以参考他们的 [全平台上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 最快速上手方案（Docker单命令）

打开你的 NAS 设备或服务器终端，执行下面这条命令即可一键启动青蛇探针：

```bash
docker run -dit \
  -v $PWD/Mmon/basedata:/Mmon/basedata \
  -p 5999:5999 \
  --restart=always \
  --name mmon \
  grbhq/mmon:latest
```

**命令说明：**

`-v $PWD/Mmon/basedata:/Mmon/basedata` ：将本地数据目录映射到容器内，用于持久化保存配置和数据

`-p 5999:5999` ：映射容器内的5999端口到主机，这是青蛇探针的访问端口

`--restart=always` ：容器异常退出后会自动重启

`--name mmon` ：给容器起名为 mmon，方便后续管理

**端口配置说明：**

如果你的服务器上已经有其他应用占用了5999端口，可以修改前面的端口号。例如要改为6000端口，只需改为 `-p 6000:5999` 即可（冒号前面是主机端口，后面是容器内端口，容器内的5999不要改）。

**数据持久化说明：**

`-v $PWD/Mmon/basedata:/Mmon/basedata` 这个参数会在当前目录创建一个 `Mmon/basedata` 文件夹，用来存储服务器配置、监控数据等重要信息。重新启动容器时这些数据不会丢失。

**环境变量设置：**

此项目不需要额外配置环境变量，使用默认配置即可正常运行。

### 部署完成

等待镜像下载和容器启动完成后，在浏览器中访问 `http://你的IP:5999` 即可进入青蛇探针的前台监控界面。

访问 `http://你的IP:5999/admin` 可以进入后台管理面板（首次使用需要注册管理员账号）。

访问 `http://你的IP:5999/ssh` 可以使用在线SSH功能远程管理服务器。

到这里，青蛇探针就已经成功部署完成了！你现在可以开始添加你的服务器进行监控。

**本篇教程结束**

---

原文：<https://www.passnat.com/posts/qing-she-tan-zhen-fu-wu-qi-jian-kong-xi-tong-docker-yi-jian-bu-shu-shi-shi-ke-shi-hua-mian-ban-zhi-chi-yuan-cheng-ssh-guan-li>
