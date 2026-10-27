---
title: "Spug 运维平台：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源运维平台 Spug：主机清单、批量执行、任务计划、应用发布集中在一个网页面板；首次启动要进容器建表并建管理员；用帕斯内网穿透拿公网地址，在外面也能进面板处理事情。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/spug-nas-docker-ops-panel-intranet-penetration
---

## 项目介绍

Spug 是一个开源的轻量级运维平台。它把平时散落在脚本和命令里的活收进一个网页：主机清单、批量执行、应用发布、定时任务，都在同一个面板里点。

对家里有 NAS、又跑着几台机器的人来说，它的用处是「少记几个命令」：主机加进列表，以后连哪台机器不用翻笔记；常用的清理、备份写成任务，到点自己跑；应用发布走一遍「申请 → 审核 → 发布」，谁在什么时候发了哪个版本，记录都在。

它不是一个开箱就替你干活的工具，而是把「你在哪台机器上执行了什么」这件事变成可视化、可追溯的操作。

项目地址：[点我跳转](https://github.com/openspug/spug)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Spug 装起来。先穿透、后部署，装完直接用公网地址打开，在外面也能进面板处理事情。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 spug，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: mariadb:10.8
    container_name: spug-db
    restart: always
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    volumes:
      - ./spug/mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=spug
      - MYSQL_USER=spug
      - MYSQL_PASSWORD=请替换为你的数据库口令
      - MYSQL_ROOT_PASSWORD=请替换为你的数据库口令
  spug:
    image: openspug/spug-service:latest
    container_name: spug
    privileged: true
    restart: always
    volumes:
      - ./spug/service:/data/spug
      - ./spug/repos:/data/repos
    ports:
      - "8080:80"
    environment:
      - SPUG_DOCKER_VERSION=v4.0.1
      - MYSQL_DATABASE=spug
      - MYSQL_USER=spug
      - MYSQL_PASSWORD=请替换为你的数据库口令
      - MYSQL_HOST=db
      - MYSQL_PORT=3306
    depends_on:
      - db
```

这段编排里有两处要改：**MYSQL\_PASSWORD** 和 **MYSQL\_ROOT\_PASSWORD** 换成你自己的口令，两处填一样的就行；宿主机端口 8080 对应隧道里填的本地端口，想换就两处一起改。Spug 的容器要 **privileged: true**，因为它的 Web 终端和文件分发要操作宿主机环境。

勾上「创建项目后立即启动」，镜像下完就起来了。第一次启动会从上游拉取对应版本的代码，日志里看到 nginx 和 spug-api 都起来了，就算启动完成。

### 三、初始化数据库、建管理员

Spug 的镜像第一次启动只准备运行环境，**不会自动建表**。进容器跑两条命令：

```bash
docker exec spug python3 manage.py updatedb
docker exec spug python3 manage.py user add -u admin -p 你的口令 -n 管理员 -s
```

第一条建表，第二条建管理员账号，**-s** 表示超级管理员。跑完打开地址，用刚建的账号登录。如果登录后弹出「未能获取到访问者的真实 IP」的安全提示，在「系统管理 → 系统设置」里把基于 IP 的登录校验关掉，或者让请求带上真实 IP 头。

### 四、主机管理：把机器加进清单

左边是分组，右边是主机列表。点「新建」加一台：填主机名称、登录用户名、主机名或 IP、SSH 端口，再选口令或密钥，保存时它会试着连一次，连得通就标成「已验证」。加进来的机器可以按分组归类，列表里能看到核数和内存，点「Web 终端」直接在浏览器里开一个 shell。

### 五、批量执行和任务计划

**批量执行**：选一批主机，把要跑的命令发下去，执行结果逐台回显；常用的命令可以存成模板，也能把本地文件分发到多台机器。

**任务计划**：点「新建」，三步——先写任务（选 Shell 或 Python，把命令贴进去），再选执行对象，最后设置触发器。触发器支持一次性、日历间隔、普通间隔和 UNIX cron，比如每天凌晨两点备份数据库，填一个 cron 表达式就行。任务列表里能看到最近一次执行的状态：待调度、执行中、成功或失败。

### 六、应用发布：把上线做成流程

**发布配置**里先建应用（名称和标识符），再给应用配一个发布：选环境、选目标主机、选常规发布或自定义发布，填仓库地址和部署目录。

**构建仓库**里能看到每个应用构建出的版本；**发布申请**里是发布记录，状态从待审核、待发布、发布中走到发布成功或发布异常，谁申请的、发的是哪个版本，一条条都留着。要不要审核，可以在发布配置里开关。

### 七、其他几个面板

**配置中心**：环境、服务、应用配置分开管，发布时直接引用。

**监控中心**：给主机加监控任务，按频率采集；**报警中心**：配告警规则和通知方式。

**系统管理**：账户、角色、登录日志和系统设置，权限按页面分配。

### 几点说明

**数据在哪。** 主机、任务、发布记录都在 MariaDB 里，对应编排里的 db 容器；Spug 自己的代码和数据在 spug 容器的 /data/spug 下。备份这两个卷就够了。

**升级。** 镜像标签用 latest，代码版本由 **SPUG\_DOCKER\_VERSION** 决定。升级前先备份数据库卷，再重新拉镜像、重启项目。

**它管的是「你已有的机器」。** 它不会替你装系统、也不会自动发现主机，机器要先能 SSH 登录，再手动加进清单。

---

原文：<https://www.passnat.com/posts/spug-nas-docker-ops-panel-intranet-penetration>
