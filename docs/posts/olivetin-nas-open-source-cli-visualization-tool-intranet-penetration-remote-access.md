---
title: "开源命令行可视化工具OliveTin，NAS一键部署，内网穿透远程访问，无公网也能随时执行"
date: 2025-10-16
description: "OliveTin是开源网页按钮面板，把常用shell命令变成一排可点击的网页按钮，按下即执行、结果直接弹回。单容器部署资源占用低，配合PassNAT内网穿透，出门也能一键操作家里NAS。完整Docker Compose部署与按钮清单配置指南。"
canonical: https://www.passnat.com/posts/olivetin-nas-open-source-cli-visualization-tool-intranet-penetration-remote-access
---

## 项目介绍

OliveTin 是一个开源的网页按钮面板，它把常用的 shell 命令变成一排可点击的网页按钮。按下按钮，命令就在你自己的机器上执行，输出结果直接显示在页面上。

它用 Go 语言编写，单个容器就能跑起来，资源占用很低。硬盘剩余空间、网络连通性、备份进度……凡是能写成一条命令的操作，都可以变成一个按钮，家里人不懂数据库和终端也能一键使用。

项目地址：[点我跳转](https://github.com/OliveTin/OliveTin)

## 部署安装

如果你希望出门在外也能按到家里的按钮，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，具体的操作步骤可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手指南。

### 使用 Docker Compose 部署

打开飞牛 NAS 的 Docker 应用，切到 Compose，点右上角的「新增项目」。

项目名称填 olivetin，路径挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面的编排整段贴进去：

```yaml
services:
  olivetin:
    image: jamesread/olivetin
    container_name: olivetin
    ports:
      - "3490:1337"
    volumes:
      - ./config:/config
    restart: unless-stopped
    network_mode: bridge
```

编排里要认两处：冒号左边的 3490 是浏览器里访问的端口（可以换成别的没被占用的端口），右边的 1337 是 OliveTin 在容器里的默认端口，保持不变；下面的 ./config 是按钮清单的挂载位置，容器会从这里读取配置。

先别急着点「创建」，按钮清单还没就位。打开飞牛的文件管理，进入刚才选的文件夹，新建一个名为 config 的文件夹。

在电脑上把下面的内容保存为 config.yaml（这就是按钮清单），上传到刚建的 config 文件夹里：

```
defaultOnClick: execution-dialog

actions:
  - title: 硬盘还剩多少
    icon: 💾
    shell: df -h /config
    timeout: 15

  - title: 家里网络通不通
    icon: 🌐
    shell: ping -c 3 223.5.5.5
    timeout: 20

  - title: NAS 记一笔
    icon: ✏️
    shell: echo "$(TZ=Asia/Shanghai date '+%m-%d %H:%M') 出门按了一下" | tee -a /config/clicks.log
    timeout: 15

  - title: 看记了什么
    icon: 📜
    shell: tail -n 5 /config/clicks.log
    timeout: 15
```

每个按钮就是一条 action：title 是按钮上显示的名字，icon 是图标，shell 是按下后真正执行的命令；最上面的 defaultOnClick: execution-dialog 让命令跑完后自动弹出结果窗口。上面的例子都是查看类的只读命令，你完全可以按自己的需要改写。

回到 Docker，勾选「创建后立即启动」，点「创建」，等列表变成「正在运行」就装好了。

### 用 PassNAT 开放到公网

OliveTin 出厂没有登录页，所以开放到公网前，务必给隧道设一个网页访问口令。打开飞牛应用中心里的 PassNAT 插件，点「创建隧道」：名字填 olivetin，协议选 HTTP，子域名起一个好记的，再设置网页访问的用户名和口令；本地地址留默认，本地端口填 3490，点创建。

点开这条隧道，按一下最左边的电源键，等状态变成「运行中」，面板上面的网址就是出门用的公网地址。

### 出门按一下

在外面的浏览器打开公网地址，输一次你设的口令，就能看到同一排按钮。点「硬盘还剩多少」，命令在家里的 NAS 上执行，结果直接弹回来；点「NAS 记一笔」，家里就记下这一下，再点「看记了什么」，记过的都还在。

按钮清单完全由你定义：把 shell 换成你自己的命令，家里 NAS 能干的事，就都能变成一个按钮。

本篇教程到此结束，祝你玩得开心！

---

原文：<https://www.passnat.com/posts/olivetin-nas-open-source-cli-visualization-tool-intranet-penetration-remote-access>


## 相关文章

- [自建 changedetection.io：飞牛 NAS Docker 部署 + 内网穿透外网访问](changedetection-io-website-change-monitor-nas-docker-deployment-intranet-penetration.md)
- [Jellyfin 家庭影院：飞牛 NAS Docker 部署 + 内网穿透，出门手机接着看](jellyfin-nas-media-server-private-cinema.md)
- [Navidrome 飞牛 NAS 部署与公网听歌](navidrome-docker-private-music-server.md)
- [Mealie 飞牛 NAS 部署与手机采购清单](mealie-private-recipe-library-nas-deployment-35-languages-remote-access.md)
- [Homebox 飞牛 NAS 部署与公网查物品档案](home-asset-management-system-nas-deployment.md)
