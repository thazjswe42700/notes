---
title: "浏览器里直接 SSH 到 NAS：飞牛 Docker 自建 Next Terminal + 内网穿透远程运维"
date: 2026-09-08
description: "在飞牛 NAS 上用 Docker 自建 Next Terminal，浏览器里直接 SSH 到机器，配合内网穿透在外面也能连。"
canonical: https://www.passnat.com/posts/next-terminal-fnos-docker-passnat
---

## 项目介绍

Next Terminal 是一个跑在浏览器里的远程连接工具。SSH、RDP、VNC、Telnet 这几种连接方式它都收进了一个网页里 —— 把要连的机器登记进去，之后想连哪台就点哪台，网页里直接开出一个终端，本地不用再装任何客户端。

它还带着一整套团队用的东西：谁能连哪台机器的授权策略、命令审计、会话录像、工单审批。一个人用的话这些可以先不管，主线很简单：加一台机器 → 点接入 → 网页里就是终端。

装在自己的 NAS 上，功能不锁。下面这套编排跑起来是三个容器：主程序、一个 PostgreSQL 数据库，外加一个 guacd（远程桌面协议的转换服务，连 RDP/VNC 才用得上，但它是编排的一部分，一起起着）。

项目地址：[点我跳转](https://github.com/next-terminal/next-terminal)

## 部署安装

这东西的价值就在于人不在家的时候也能连上去看一眼。装在内网，出了门就够不着，那它一大半用处就没了。想省事可以用 [帕斯内网穿透](https://passnat.com)，在飞牛的应用中心里就能装到插件，装好之后在插件里直接建一条隧道、把 NAS 上的 8088 端口开放到公网，拿到一个公网地址，之后建管理员、加机器、连终端全都在这个地址上做。

### 第一步：先把 config.yaml 建出来

这一步顺序不能反，先建文件再启动。编排里挂的是一个文件（./config.yaml:/etc/next-terminal/config.yaml），而 Docker 有个脾气：绑定挂载的路径要是不存在，它会自作主张建一个同名的目录。真让它建成目录了，主程序读不到配置就起不来，而且用 compose down 也删不掉那个目录，还得手动去删。所以在放编排的那个目录里，先建一个 config.yaml，内容如下：

```
Database:
  Type: postgres
  Postgres:
    Hostname: postgresql
    Port: 5432
    Username: next-terminal
    Password: next-terminal
    Database: next-terminal

Server:
  Addr: "0.0.0.0:8088"

App:
  Guacd:
    Hosts:
      - Hostname: guacd
        Port: 4822
        Weight: 1
```

上游给的那份有四十来行，这里只留了三段：数据库怎么连、自己监听哪个端口、guacd 在哪。其余的都是可选项，不写就走默认值，实测这样够用。数据库的主机名写 postgresql、guacd 写 guacd，是因为它们在同一套编排里，容器之间直接用服务名互相找得到，不用填 IP。

### 第二步：编排文件

同一个目录里再放这份编排。它跟上游给的那份是一致的，照抄就行：

```yaml
services:
  guacd:
    container_name: guacd
    image: dushixiang/guacd:latest
    volumes:
      - ./data:/usr/local/next-terminal/data
    restart: always
  postgresql:
    container_name: postgresql
    image: postgres:18
    environment:
      POSTGRES_DB: next-terminal
      POSTGRES_USER: next-terminal
      POSTGRES_PASSWORD: next-terminal
    volumes:
      - ./data/postgresql:/var/lib/postgresql
    restart: always
  next-terminal:
    container_name: next-terminal
    image: dushixiang/next-terminal:latest-pg18
    ports:
      - "8088:8088"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./data:/usr/local/next-terminal/data
      - ./logs:/usr/local/next-terminal/logs
      - ./config.yaml:/etc/next-terminal/config.yaml
    depends_on:
      - postgresql
      - guacd
    restart: always
```

要认的地方就两处。一是端口 8088:8088，左边那个是 NAS 上开出来的口，等下建隧道要填的就是它。二是几行挂载：data 放数据、logs 放日志、config.yaml 就是上一步建的那个文件。数据库那三个 POSTGRES\_ 变量是容器之间自己用的，跟上面 config.yaml 里那三行对上就行，想改的话两个文件一起改。

### 第三步：起容器，然后在公网地址上建管理员

在飞牛的 Docker 里新建一个 Compose 项目，路径选到刚才那个目录，把编排贴进去启动。三个容器都跑起来之后，先去 PassNAT 插件里把隧道建好、按下电源拿到公网地址，然后直接用公网地址打开，页面会停在一个初始化用户的界面上，设一个管理员账号和密码就行 —— 没有验证码、也没有时间限制，不用赶。

第一次打开是无人认领的状态，谁先访问谁就是管理员。所以隧道一开，就顺手把管理员建掉，别晾在那儿。

### 第四步：加一台机器，然后连上去

进去之后走 资源管理 → 资产 → 新建。要填的东西很少：

名称随便起一个自己认得出来的；协议那一排默认就选中了 SSH，不用动；网络地址填这台机器的内网 IP，右边的端口默认已经是 22；账户类型留默认的「密码」，下面把用户名和密码填上。保存之后列表里那一行的状态会变成「活跃」，说明它已经能连上这台机器了。

然后点那一行右边的「接入」，浏览器里就开出一个终端，命令敲下去跟本地终端没有区别。人在外面用手机或者别人的电脑，打开那个公网地址登录进来，一样能连。

## 几句提醒

管理员密码设一个复杂点的，别用弱口令 —— 这个页面开在公网上，进去之后就能连到你登记进去的所有机器。系统设置里还能开两步验证，建议顺手打开。

登记进去的机器账号密码是存在它自己的数据库里的，也就是编排里那个 data 目录。哪天不想用了，把容器停掉、把这个目录删掉，里面存的东西就一起没了。

---

原文：<https://www.passnat.com/posts/next-terminal-fnos-docker-passnat>


## 相关文章

- [浏览器里就是命令行：飞牛 NAS 用 Docker 自建 WebSSH + 内网穿透远程敲命令](webssh-fnos-docker-passnat.md)
- [自建 AI 画流程图：飞牛 NAS Docker 部署 next-ai-draw-io，一句中文生成 draw.io 图表并远程访问](ai-flowchart-next-ai-draw-io-nas-docker-deployment-intranet-penetration.md)
- [青蛇探针服务器监控系统，Docker一键部署实时可视化面板，支持远程SSH管理](qing-she-tan-zhen-fu-wu-qi-jian-kong-xi-tong-docker-yi-jian-bu-shu-shi-shi-ke-shi-hua-mian-ban-zhi-chi-yuan-cheng-ssh-guan-li.md)
- [DopplerTask开源自动化平台Docker快速部署指南，NAS轻松搭建工作流自动化系统](doppler-task-docker-nas-workflow-automation-guide.md)
- [轻量级Git容器镜像Alpine Docker，资源受限环境极简部署，无需安装Git直接运行](lightweight-git-alpine-docker-minimal-deployment-resource-constrained.md)
