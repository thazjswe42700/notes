---
title: "Traggo 时间记录：NAS 上 Docker 部署，给每段时间打标签"
date: 2026-10-20
description: "用 NAS 上的 Docker Compose 部署开源时间记录工具 Traggo：项目、活动两层标签，点开始计时，日历和看板按周复盘；数据存在自己的 traggo-data 卷里，重启不丢。配帕斯内网穿透，出门也能补记。"
canonical: https://www.passnat.com/posts/traggo-time-tracking-nas-docker-tag-time-intranet-penetration
---

## 项目介绍

Traggo 是一个开源的时间记录工具，Go 写的，单个容器就能跑。它不替你自动采集屏幕，而是让你在做一件事的时候点一下开始，选好「项目」和「活动」两层标签，做完点停止；忘了点，也可以事后补一条，起止时间自己填。

记录攒起来之后，它给你两种看法：日历按周排开，每段时间一个色块，哪天花在哪个项目上一眼看清；看板把近七天的时间做成饼图、柱状图和按天排的表，周末复盘不用凭感觉。数据存在自己的 NAS 上，重启、换电脑都还在。

项目地址：[点我跳转](https://github.com/traggo/server)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿一个公网地址，再用 Docker Compose 把 Traggo 装上。Traggo 的编排本身不用填公网地址，但出门想补记或者看一眼，隧道提前准备好会省事。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3030（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 traggo，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  traggo:
    image: traggo/server:latest
    ports:
      - "3030:3030"
    environment:
      TRAGGO_DEFAULT_USER_NAME: "admin"
      TRAGGO_DEFAULT_USER_PASS: "换成你自己的口令"
    volumes:
      - traggo-data:/opt/traggo/data
volumes:
  traggo-data:
```

**ports**：把容器里的 3030 映射到宿主机的 3030，冒号左边是 NAS 上的端口，建隧道时填的就是它。

**TRAGGO\_DEFAULT\_USER\_NAME / TRAGGO\_DEFAULT\_USER\_PASS**：第一次启动时创建的管理员账号和口令。这两行只在数据库为空时生效，装好之后请到用户管理里把口令改掉，别一直用这里填的值。

**volumes**：把容器里的 /opt/traggo/data 挂到名为 traggo-data 的卷上，记录、标签和看板配置全在这里，重启不丢。备份时把这个卷导出，或者把数据目录拷走就行。

勾上「创建项目后立即启动」，点确认，镜像拉完就起来了。

### 三、第一次登录

浏览器打开公网地址（在家也可以直接用 NAS 的局域网地址加 3030 端口），用编排里设的账号口令登录。

**登录后第一件事是改口令**：左侧「Users」进用户管理，点 admin 那一行，在 Password 栏填新口令，点勾保存。

### 四、开始记录时间

左侧「Tags」先把标签建好。Traggo 用两层标签：一层是「项目」，比如网站改版、客户方案、内容整理；一层是「活动」，比如开发、写作、会议。每个标签可以挑一个颜色，日历和看板就靠颜色区分。

平时做事，在「Timesheet / List」顶上的输入框里选好项目和活动，点 START 就开始计时；做完点 STOP，时长自动算好。同一时刻可以开多个计时器，适合并行处理几件事。

忘了点也没关系：条目右侧的菜单里可以手动补一条，起止时间自己填，标签和备注照常加。

左侧「Calendar」按周看，每段时间是一个色块；点色块能改起止时间、换标签。

### 五、周末复盘

左侧「Dashboards」里可以建看板，往里加三种小卡片：饼图看项目占比，柱状图看每天投入，表格按天把项目和活动摊开。时间范围选「近七天」或者「本周」，周末打开就知道这周时间花在哪。

**说明一下**：Traggo 不会自动采集你在电脑上做了什么，所有记录都来自你点的开始、停止或者手动补录；它也不带手机 App，手机浏览器打开网页就能用。

### 几点说明

**数据在哪。** 记录在 traggo-data 这个卷里（容器内是 /opt/traggo/data）。换机器时把卷导出、在新机器上恢复，重新起容器就是原来的记录。

**更新。** 重新拉取镜像、重启项目即可；升级前先把数据卷备份一份。

**端口。** 编排里写的是 3030:3030，想换宿主端口只改冒号左边；改完记得把隧道里的本地端口一起改。

---

原文：<https://www.passnat.com/posts/traggo-time-tracking-nas-docker-tag-time-intranet-penetration>
