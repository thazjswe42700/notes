---
title: "ExpenseOwl 轻量记账：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "不想做预算、只想看清钱花在哪：在 NAS 上用 Docker Compose 部署极简记账工具 ExpenseOwl，一分钟记一笔，首页环形图按分类汇总，表格能查能搜，还能导出 CSV。它没有内建登录，用帕斯内网穿透的 HTTP 隧道加网页访问口令再放到公网，含完整编排和备份说明。"
canonical: https://www.passnat.com/posts/expenseowl-lightweight-expense-tracker-nas-docker-intranet-penetration
---

## 项目介绍

ExpenseOwl 是一个极简的自托管记账工具。它不追求做成一套财务系统，只回答一个问题：这个月的钱都花在哪了？记一笔只要填名字、分类、金额和日期四样，不用做预算，也不用分账户。

打开首页是一张环形图：每一类占多少、花了多少，列在右边，底下是收入、支出和结余。点一下某个分类，可以把它从图里暂时拿掉，看看去掉它之后剩下的花销占多少；再点一下恢复。表格页按月列出流水，也能切到全部交易，按名字或标签搜索。设置里可以改分类和币种，还能把数据导出成 CSV，备份或者换工具都能带走。

它只有一个容器，默认用 JSON 文件存数据，挂一个文件夹到 /app/data 就完成了持久化。项目地址：[点我跳转](https://github.com/Tanq16/ExpenseOwl)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示；先把它跑起来，最后再讲怎么在外面访问。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 expenseowl，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  expenseowl:
    image: tanq16/expenseowl:main
    container_name: expenseowl
    restart: unless-stopped
    ports:
      - "5006:8080"
    volumes:
      - ./data:/app/data
```

这段编排只有三处要留意：镜像沿用官方写法的 main；冒号左边 5006 是 NAS 上对外用的端口，右边 8080 是容器里的端口，一般不用改；./data 是存账目的文件夹，所有记录都在里面，备份就把它整个拷走。

勾上「创建项目后立即启动」，点确认，镜像下完容器就起来了。浏览器打开 NAS 地址加 5006 端口，就能看到首页。

### 二、记第一笔

点页面上的 Add Expense，填名字、选分类、填金额和日期，点下面的 Add Expense 就记上了。支出填正数就行，工具会自己按支出处理；收入或者报销，勾上 Report Gain 再提交，它会记成进账。

分类可以在设置里增删和排序。金额很小或者很大的时候，都可以在设置里把币种换成自己用的那一种，图表和表格会一起变。

### 三、看图、查表、导出

首页的环形图按分类汇总当月支出，右边的百分比和金额就是每一类的占比；点图例里的分类，会把它从图里临时排除，再点恢复。底下的三张卡分别是收入、支出和结余，结余为正显示绿色，为负显示红色。

表格页默认显示当月流水，勾上 Show All Transactions 就是全部记录；每条都能编辑和删除，删除时要再确认一次。设置里的 Export to CSV 会把全部记录导出成 CSV，字段有 ID、名字、分类、金额、日期和标签，重新导入时 ID 会保留，方便备份和迁移。

### 四、出门访问：用帕斯内网穿透加一道锁

ExpenseOwl 本身没有账号和密码，这一点要先说清楚：如果直接在家里路由器上把 5006 端口映射出去，拿到地址的人就能看账、改账、删账。所以不要做端口映射，也不要直接把它暴露在公网。

正确的做法是走 [帕斯内网穿透](https://passnat.com)：飞牛的「应用中心」里搜 PassNAT，装上插件打开，点「创建隧道」。隧道名字随便起（不超过 12 个字符），传输协议选 HTTP，本地 IP 留默认的 127.0.0.1，本地端口填 5006。创建时把「网页访问口令」也填上——这就是那把锁，别人打开地址会先被浏览器要一次口令，输对了才进得来。

建好之后点开隧道，按下电源，状态变成运行中，详情里就是分配好的公网地址。在外面用手机流量打开这个地址，先输一次口令，就能记一笔、看图表、导出 CSV，和在家里打开是同一份数据。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**备份。** 所有账目都在 ./data 里（容器里的 /app/data），把整个文件夹拷走就是完整备份；恢复时放回原位再启动容器即可。

**没有登录怎么办。** 这个工具是按单用户、家里自用设计的，所以要么只在家里局域网用，要么就像上面那样挂在带访问口令的隧道后面。口令是隧道那一层做的，不需要改应用本身。

**更新。** 镜像标签用的是 main，跟着上游主分支走。升级前先把 ./data 备份一份，再到 Docker 里重新拉取镜像、重启项目。

**想多人一起记。** 这个工具没有多用户和权限，适合自己或一家人共用一份账本；需要按人分开、设权限的，应该选带账号体系的记账系统。

---

原文：<https://www.passnat.com/posts/expenseowl-lightweight-expense-tracker-nas-docker-intranet-penetration>


## 相关文章

- [LibreRegistration 活动报名页：NAS Docker 部署 + 内网穿透](libreregistration-nas-docker-event-signup-intranet-penetration.md)
- [MicroBin 临时分享：NAS Docker 部署 + 内网穿透](microbin-lightweight-self-hosted-clipboard-docker-deployment-intranet-penetration.md)
- [私有ntfy推送服务NAS极简部署，内网穿透外网实时通知接收方案](private-ntfy-push-service-nas-minimal-deployment-intranet-penetration-external-network-real-time-notification-solution.md)
- [PairDrop 自建隔空投送：飞牛 NAS Docker 部署 + 内网穿透跨设备传文件](pairdrop-cross-device-file-transfer-nas-docker-deployment-intranet-penetration.md)
- [HivisionIDPhotos 手机拍一张就出证件照：飞牛 NAS Docker 部署 + 内网穿透](hivision-id-photos-professional-certificate-photo-generator.md)
