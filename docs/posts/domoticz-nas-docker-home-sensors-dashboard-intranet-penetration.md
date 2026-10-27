---
title: "Domoticz 家庭设备看板：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker 部署 Domoticz 家庭自动化平台：单容器、数据存本地，网页看板集中显示温湿度、开关、门窗和光照，手机浏览器也能用。没有硬件时用 Dummy 虚拟设备先把流程跑通，再用帕斯内网穿透在手机流量下打开。含完整编排、首次建管理员和虚拟设备步骤。"
canonical: https://www.passnat.com/posts/domoticz-nas-docker-home-sensors-dashboard-intranet-penetration
---

## 项目介绍

Domoticz 是一个开源的家庭自动化平台。它把家里各种传感器和执行器接进来，用一个网页集中显示：温度、湿度、光照、门窗状态、开关，全部在一个看板上，手机浏览器打开也能用。支持大量现成的硬件协议（Z-Wave、Zigbee 网关、MQTT、HTTP 接口、电表水表等等），也自带「虚拟设备」，没有硬件时可以先建测试设备，把界面和自动化流程跑通。

它跑起来只需要一个容器，数据存在容器挂载的目录里，默认用 SQLite，不需要额外数据库。适合把家里的温湿度、灯、门窗这类状态集中到 NAS 上，再通过内网穿透在手机流量下查看。项目地址：[点我跳转](https://github.com/domoticz/domoticz)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：在「Docker」应用里新建一个 Compose 项目，把编排贴进去启动，第一次打开网页时创建管理员账户，然后进后台添加硬件和设备。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 domoticz，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  domoticz:
    image: domoticz/domoticz:stable
    container_name: domoticz
    ports:
      - "8080:8080"
    volumes:
      - ./domoticz-data:/opt/domoticz/userdata
    environment:
      - TZ=Asia/Shanghai
    restart: unless-stopped
```

这段编排里没有要改的口令或地址，直接用就行。三个地方按需调整：

**端口**：左边 8080 是 NAS 上的访问端口，被占用就换一个，右边的 8080 是容器内部端口不要改。

**数据目录**：./domoticz-data 会建在项目目录下面，存数据库、日志和设备配置，备份这个目录就够了。

**时区**：TZ 按自己所在时区写，日志和定时任务的时间会用到。

勾上「创建项目后立即启动」，第一次会拉取镜像，通常半分钟内容器就起来了。

### 二、第一次打开：建管理员账户

浏览器打开 NAS 地址加 8080 端口（例如 http://NAS地址:8080），第一次访问会自动进入设置向导：「Create your admin account」，填用户名、两次密码，点「Create Account」。这一步不需要邮箱、不需要邮件验证、不连外部服务，提交完就回到登录页，用刚建的账户登录。

如果之后想再改口令，在后台「Setup → Users」里管理；这个版本里已经在用的账户可以改密码，口令只存在 NAS 的 domoticz-data 目录里，不会上传到任何地方。

### 三、添加硬件：真实设备或虚拟测试设备

登录后进「Setup → Hardware」。页面顶部是添加表单：勾上 Enabled，填名字，在 Type 里选设备类型。接真实硬件时按你的网关或协议选（例如 MQTT、Zigbee 网关、Z-Wave USB）；手头没有硬件，就在列表里选 **Dummy (Does nothing, use for virtual switches only)**，点「Add」。

本期的截图和演示用的就是 Dummy 虚拟硬件，名字写成「测试设备（虚拟）」，这样看板上的数值变化都是虚拟设备在动，不代表控制了什么真实家电。硬件列表里这一行会出现「Create Virtual Sensors」按钮，下一步就从这里建设备。

### 四、创建虚拟传感器

在硬件列表里点「Create Virtual Sensors」，弹窗里填设备名字、选类型，点「OK」。常用的类型有：

**Temp + Humidity**：温湿度一体，一张卡片同时显示温度和湿度；

**Switch**：开关，看板上直接有 On / Off 按钮；

**Alert**：告警类，值可以是 Normal / Alarm，适合门磁、烟感这类状态；

**Lux**：光照度。

本期演示建了八个测试设备：客厅和卧室各一个温湿度、四个开关（客厅灯、玄关灯、书房灯、厨房插座）、一个大门门磁和一个阳台光照。建完回到「Dashboard」，每个卡片右上角有个星标，点一下收藏，收藏的设备才会出现在看板上。

虚拟设备没有真实数据来源，要让它显示数值，可以在「Setup → Devices」里点设备右侧的箭头编辑，也可以调用 Domoticz 的 JSON 接口直接写值。接真实硬件时这一步由硬件自己上报，不用手动填。

### 五、用起来：看板和手机

Dashboard 按类型分组：Lights/Switches 是开关卡片，Temperature 显示温度和湿度，Utility 放门磁、光照这类状态。开关卡片点右上角就能切换，温度卡片能看到最后上报时间。

顶部的「Temperature」「Switches」「Utility」是分类页，点进设备还能看历史曲线：「Log」里有最近 24 小时的温度、湿度变化图，可以按 1 小时 / 3 小时 / 一天切换。

手机浏览器打开同一个地址，会自动进手机版：每行一个设备，开关是 On / Off 两个大按钮，温湿度、门磁、光照排成列表，单手就能点。把它加到手机主屏幕，用起来跟 App 差不多。

### 六、用帕斯内网穿透拿到公网地址

在家里的 Wi-Fi 下用 NAS 地址就能打开；出门在外想用手机流量看，就用 [帕斯内网穿透](https://passnat.com) 这个插件。在飞牛的「应用中心」里搜 PassNAT，装上它，打开插件，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口）。在外面用手机浏览器打开这个地址，就是家里同一个 Domoticz 看板，登录后还能切换开关。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**默认是明文 HTTP。** 内网访问和穿透访问都是 http，登录口令在局域网内是明文传输，公网地址谁拿到都能打开登录页，别到处发；不用的时候可以在插件里把隧道关掉。Domoticz 容器本身带 443，但需要自己配证书，默认不用它。

**数据在本地。** 设备、日志、用户都存在 domoticz-data 目录的 SQLite 数据库里，不依赖外部服务；备份这个目录就能迁移到另一台 NAS。

**虚拟设备只做演示。** Dummy 硬件产生的数值不会控制任何真实设备，接真实硬件要按协议在 Hardware 里另加对应类型的硬件，再让网关或设备上报。

**更新。** 镜像标签是 stable，想升级就在 Docker 里重新拉取镜像并重启项目；升级前先备份 domoticz-data 目录，大版本升级前看一眼官方发布说明。

---

原文：<https://www.passnat.com/posts/domoticz-nas-docker-home-sensors-dashboard-intranet-penetration>
