---
title: "Leantime 项目管理：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源项目与目标管理 Leantime：待办任务按状态分列，每条写优先级、负责人和预计工时，工时表按周记实际耗时，目标用进度条看整体走到哪；用帕斯内网穿透拿公网地址，在外面也能看进度、记工时。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/leantime-nas-docker-project-management-goals-timesheets-intranet-penetration
---

## 项目介绍

Leantime 是一个开源的项目和目标管理工具，面向的是「手里同时有几件事在推」的人：每件事做到哪一步、谁在做、花了多少时间、离目标还差多远，都在一个界面里。它把自己定位成轻量、少干扰，页面上的入口不多，第一次用不需要先学一套方法论。

一个项目里有几种东西：待办任务、里程碑、目标和工时。待办任务可以按状态分列，写成看板、表格或者列表；每条能填优先级、负责人、预计工时和截止日。里程碑把任务按阶段归拢，目标则是可量化的指标——比如「页面改完 0 到 100」，填一个当前值，进度条自己算。

工时表按周记：一行是一条任务，一列是一天，填进去的小时数会自动合计。项目、任务、工时、目标互相关联，不用在几个工具之间来回搬。

界面有简体中文，账号分角色。项目地址：[点我跳转](https://github.com/Leantime/leantime)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Leantime 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后在外面也能看项目进度、记工时。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3691（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 leantime，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: mysql:8.0
    restart: unless-stopped
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      MYSQL_ROOT_PASSWORD: change-me-db-password
      MYSQL_DATABASE: leantime
      MYSQL_USER: leantime
      MYSQL_PASSWORD: change-me-db-password
    volumes:
      - leantime_db:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "127.0.0.1", "-u", "leantime", "-pchange-me-db-password"]
      interval: 5s
      timeout: 5s
      retries: 30

  leantime:
    image: leantime/leantime:latest
    restart: unless-stopped
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "3691:8080"
    environment:
      LEAN_DB_HOST: db
      LEAN_DB_USER: leantime
      LEAN_DB_PASSWORD: change-me-db-password
      LEAN_DB_DATABASE: leantime
      LEAN_SITENAME: 项目管理
      LEAN_LANGUAGE: zh-CN
      LEAN_DEFAULT_TIMEZONE: Asia/Shanghai
    volumes:
      - leantime_userfiles:/var/www/html/userfiles
      - leantime_public_userfiles:/var/www/html/public/userfiles

volumes:
  leantime_db:
  leantime_userfiles:
  leantime_public_userfiles:
```

这段编排里要改的只有数据库口令：

**change-me-db-password**：编排里出现了四处（MySQL 的 root 口令、应用账号口令、健康检查那一行、Leantime 连接数据库的口令），四处必须换成同一串你自己的口令，字母数字混着来，越长越好。改完这一串，数据库和 Leantime 才能对上。

端口 3691 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，MySQL 先起、健康检查通过之后 Leantime 才起，整个过程大约半分钟。

数据都在三个具名卷里：leantime\_db 是数据库，另外两个是用户上传的文件，备份这三个卷就够了。

### 三、第一次打开：建管理员、选中文

用公网地址打开，第一次会进安装页：填邮箱、名、姓、公司，点「Install」。下一页是账号设置，填名字、职位和密码——密码要求至少 8 位，包含大写、小写、数字和符号各一个。

接着是主题、配色、作息时间三步，按自己习惯点就行。最后一步如果停在「journey about to begin」再点按钮报错，直接点页面上的「返回仪表盘」跳过，账号已经建好了，不影响使用。

进去之后如果想换界面语言：右上角头像 → 账号设置 → 设置 → 语言，选「中文 (简体)」保存。编排里已经写了 LEAN\_LANGUAGE=zh-CN，新装的实例默认就是中文，个别地方没跟上的按上面这一步补一下。

### 四、建项目、建任务

**先建项目。** 左侧「项目枢纽」→ 新增项目，写项目名（比如「官网改版」）和一句描述，保存。

**再建任务。** 进项目的「待办任务」，点右上角「新增 → 新增待办」，写标题，选状态和优先级，「分派给」选自己，填上预计工时和截止日，保存。几条任务建好之后，切到「看板模式」，把任务卡片拖到「进行中」，状态就跟着变；同一批任务也能在「表格模式」「列表」里看。

**再建目标。** 左侧「目标」进目标仪表板，点「Create New Goal」，写目标名，指标类型选数字，起始填 0、目标填 100、当前填上现在的进度，保存后进度条和「正常 / 风险 / 延期」状态就出来了。

### 五、记工时、看进度

干完活去左侧「工时表」，一周一行任务，找到那条任务，在今天的格子里填几个小时，点保存；下面一行会自动合计本周一共多少小时。

仪表盘上汇总了几块：剩余任务数、最近七天完成的任务、你参与的目标数，下面是「我的待办」，按截止日排好，这周该做什么直接列出来。开会前打开看一眼，比在群里挨个问快。

在外面用手机打开同一个公网地址，任务、工时、目标都能看，界面会按手机宽度自动收窄。

### 几点说明

**数据在哪。** 数据库在 leantime\_db 卷里，上传的文件在另外两个卷里，三个卷一起备份就行。想搬到别的机器，把这三个卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先把三个卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Leantime 本身不需要 HTTPS 才能用；但它记的是自己家的项目和工时，公网地址别到处发，用完了可以在插件里把隧道关掉。

**团队协作。** 多个人的时候，在「公司 → 用户」里邀请成员，再在项目设置的「团队」里分配角色；这篇只写单人从零用起来这条路，团队权限等熟了自己再摸。

---

原文：<https://www.passnat.com/posts/leantime-nas-docker-project-management-goals-timesheets-intranet-penetration>
