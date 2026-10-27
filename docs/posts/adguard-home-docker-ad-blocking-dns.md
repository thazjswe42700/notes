---
title: "AdGuard Home 飞牛 NAS 部署与公网管理"
date: 2026-10-05
description: "全屋设备去广告：AdGuard Home 在飞牛 NAS 上的单容器部署、首启向导、隧道与公网远程改过滤规则步骤。"
canonical: https://www.passnat.com/posts/adguard-home-docker-ad-blocking-dns
---

## 项目介绍

AdGuard Home 是 AdGuard 官方开源的全网广告拦截 DNS 服务器，把它部署在 NAS 上，全家的设备把 DNS 指向它，手机、电视上的广告和跟踪域名就在解析这一步被拦掉，设备上不需要装任何客户端。

它是免费开源项目，实测版本 v0.107.79，官方 Docker 镜像，界面自带简体中文。拦了多少、拦了哪些域名在仪表盘和查询日志里都能看到，过滤规则随时可改。

项目源码：[AdguardTeam/AdGuardHome](https://github.com/AdguardTeam/AdGuardHome)

官方文档：[AdGuard Home Wiki](https://github.com/AdguardTeam/AdGuardHome/wiki)

## 部署安装

下面用飞牛 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 把管理界面开放到公网，人在外面也能看家里的拦截情况、改过滤规则。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 adguard；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面的编排贴进去：

```yaml
services:
  adguardhome:
    image: adguard/adguardhome:latest
    container_name: adguardhome
    network_mode: bridge
    ports:
      - "3444:3000"
      - "53:53/tcp"
      - "53:53/udp"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - "./work:/opt/adguardhome/work"
      - "./conf:/opt/adguardhome/conf"
    restart: unless-stopped
```

3444 是 NAS 对外的管理界面端口，容器里固定用 3000（首次配置向导就跑在这个端口上，向导里把「网页管理界面」的端口也填 3000，两边正好对上）。53 是 DNS 端口，TCP 和 UDP 都要映射，后面家里设备的 DNS 就指向 NAS 的这个端口。./work 存统计数据，./conf 存配置和数据库，两个都是相对路径，落在你选的项目文件夹里。

勾选「创建项目后立即启动」，点确认，等容器进入运行中。记住 3444 这个端口，后面一直用它。

## 二、建好隧道，先在局域网走完首启向导

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点「创建隧道」：隧道名称随便起，本地 IP 留默认，本地端口填 3444，创建。先别急着按这一行的电源。

先在浏览器打开 NAS 的 3444 端口，走一遍 AdGuard Home 的首启向导：欢迎页点「开始配置」；第二步「网页管理界面」的端口默认是 80，改成 3000（跟编排里的映射对上），DNS 服务器的端口 53 保持默认；第三步设置网页管理界面的用户名和密码，自己设一串记好；一路下一步到完成，就会进入仪表盘。

管理员建好之后，再回到 PassNAT 插件，按这条隧道的电源，等状态变成运行中。打开详情，这个公网地址就是管理界面在公网上的入口——因为管理员已经建好，别人碰到这个地址也只能停在登录页。

## 三、把全家设备接进来

AdGuard Home 自带「设置指导」页，Router、Windows、macOS、Android、iOS 的接法都写好了。最省事的是在路由器的 DHCP/DNS 设置里，把 DNS 服务器填成 NAS 的内网地址，连在路由器上的所有设备自动生效；也可以按指导给单台设备改 DNS。

设备接进来之后，回到仪表盘就能看到查询数和拦截数在涨；查询日志里每一条解析都有记录，被拦的域名会标红，点开条目的菜单还能放行或拦截。

## 四、在外面管理家里的过滤规则

在外面用手机或电脑打开第二步拿到的公网地址，登录自己建的管理员账号：仪表盘看拦截统计，查询日志里放行误拦的域名马上生效，「过滤器」里还有 DNS 黑名单、白名单和自定义过滤规则，跟在家里操作完全一样。

## 数据与备份

项目文件夹里的 conf 是配置和数据库，work 是统计数据。定期把整个项目文件夹复制一份到别的盘，就是完整备份；恢复的时候用同一份编排重新建项目即可。

---

原文：<https://www.passnat.com/posts/adguard-home-docker-ad-blocking-dns>
