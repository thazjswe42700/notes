---
title: "NetAlertX家庭网络看守NAS部署教程，Docker搭建WiFi设备清单，新设备接入早知道"
date: 2026-10-14
description: "NetAlertX是开源家庭网络看守工具，定期ARP扫描全网段，新设备接入自动记录提醒。支持Docker单容器部署在飞牛NAS等家用设备上，配合内网穿透出门也能查看家庭WiFi设备清单，设备名、厂商、在线时间一目了然。"
canonical: https://www.passnat.com/posts/netalertx-nas-wifi-device-scanner-deploy
---

## 项目介绍

NetAlertX 是一个开源的家庭网络看守。它会定期把家里整个网段扫描一遍，谁连上了 WiFi、是什么设备、什么时候上线，全部记在一张清单里。家里有多少台设备在偷偷联网，一眼就能看穿；有陌生设备接入，它还会记下时间提醒你。

项目地址：[点我跳转](https://github.com/netalertx/NetAlertX)

它是单容器应用，资源占用很低，飞牛 NAS 这类家用设备就能跑。手机、电脑打开浏览器就能看这张清单，配合内网穿透，出门在外也能打开家里这张网络清单。

## 部署安装

还没有装好飞牛 NAS 和 Docker 环境的，可以先参考官网 [帕斯内网穿透](https://passnat.com) 上的安装教程，把基础环境搭好再回来。

打开飞牛的 Docker 应用，切到 Compose 页，点「新增项目」。项目名随便起，路径选一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段编排整段贴进去：

```yaml
services:
  netalertx:
    image: jokobsk/netalertx:latest
    container_name: netalertx
    network_mode: host
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
      - NET_RAW
      - NET_BIND_SERVICE
      - CHOWN
      - SETUID
      - SETGID
    environment:
      - PORT=20211
      - SCAN_SUBNETS=192.168.0.0/24
    volumes:
      - ./data:/data
      - /etc/localtime:/etc/localtime:ro
```

**网络模式用 host：**NetAlertX 要直接在局域网里发 ARP 扫描包，才能发现网上有哪些设备，所以容器直接用宿主网络，不再单独映射端口。

**PORT=20211：**网页管理界面的端口，浏览器访问和后面建隧道都用它，可以照自己需要改。

**SCAN\_SUBNETS=192.168.0.0/24：**要扫描的网段，照你家路由器实际分配的网段填，一般是 192.168.0.0/24 或 192.168.1.0/24。这一项建议显式写上：NetAlertX 在多网卡的机器上会把识别到的所有网段都自动填进去，虚拟网卡多的时候一轮扫描会非常久，反而扫不出结果。

**./data:/data：**设备清单和配置都存在这个文件夹里，备份把它整个拷走就行。

勾上「创建后立即启动」，点确认，等容器跑起来。

## 首次配置

浏览器打开 http://你NAS的IP:20211 ，就是设备清单页面。第一次打开没有密码，先到 Settings（设置）里把密码功能打开并设一个自己的密码，保存之后再次访问就需要登录了。

设置里还能调扫描周期，默认每 5 分钟扫一轮；想更快发现新设备，可以把 ARPSCAN 的计划改得更密一些。

## 开放到公网

家里宽带一般没有公网 IP，出门要打开这张清单，用飞牛自带的 PassNAT 插件做内网穿透最省事：应用中心里搜 PassNAT 装上，打开插件点「创建隧道」，名字随意，本地地址留默认的 127.0.0.1，端口填 20211，其他都不用动。

创建好之后在详情面板点一下电源键，绿点亮起，公网地址就能用了。以后在外面打开这个地址，登录之后就能看到家里网络的实时清单。

## 日常使用

清单里每台设备都有名字、厂商、MAC 地址和最近在线时间，可以给常用设备改成好认的名字、加收藏。有新设备接入时，应用内通知会记下时间，陌生设备一露头就能发现。

---

原文：<https://www.passnat.com/posts/netalertx-nas-wifi-device-scanner-deploy>
