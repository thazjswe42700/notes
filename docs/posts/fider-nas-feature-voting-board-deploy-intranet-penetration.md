---
title: "Fider 飞牛 NAS 部署与公网投票"
date: 2026-10-05
description: "自建投票站：Fider 在飞牛 NAS 上的三容器部署、先建隧道拿公网地址、公网初始化与发链接全家投票的完整步骤。"
canonical: https://www.passnat.com/posts/fider-nas-feature-voting-board-deploy-intranet-penetration
---

## 项目介绍

Fider 是一个开源的反馈投票站，原版是给产品团队收集用户建议用的：帖子、投票、评论、Roadmap 看板都是现成的。拿回家里一样好使——周末去哪玩、年夜饭吃什么、买东西要不要下手，发一条帖子把链接丢到家庭群，大家点开就能投，票数和评论自动排好。

它是免费开源项目，实测版本 v0.38.1，官方 Docker 镜像。所有数据存在自己 NAS 的 PostgreSQL 里，界面是英文的，跟着步骤点很快就能上手。

项目源码：[getfider/fider](https://github.com/getfider/fider)

官方文档：[Fider Docs](https://docs.fider.io)

## 部署安装

下面在飞牛 NAS 上部署，再通过 [帕斯内网穿透](https://passnat.com) 把投票站开放到公网——它的公网地址要提前写进配置，所以我们先把隧道建好、拿到地址，再来部署。

## 一、先建隧道，拿一个公网地址

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点「创建隧道」：类型选 HTTP，域名自己起一个（我填的是 nasvote9k2），本地地址留默认，本地端口填 3486，点创建隧道，然后按这一行的电源，等状态变成运行中。这个 http://你的域名.odn.cc 就是投票站的公网地址，待会儿要用，先复制下来。

## 二、Docker Compose 部署

打开 Docker，切到 Compose，点新增项目。项目名称填 fider；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面的编排贴进去：

```yaml
services:
  db:
    image: postgres:17-alpine
    restart: always
    volumes:
      - ./pgdata:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: fider
      POSTGRES_PASSWORD: ChangeMe_DbPass
  mail:
    image: axllent/mailpit:latest
    restart: always
    ports:
      - "3487:8025"
  app:
    image: getfider/fider:v0.38.1
    restart: always
    depends_on:
      - db
      - mail
    ports:
      - "3486:3000"
    environment:
      BASE_URL: http://nasvote9k2.odn.cc
      DATABASE_URL: postgres://fider:ChangeMe_DbPass@db:5432/fider?sslmode=disable
      JWT_SECRET: 换成一串长一点的随机字符
      EMAIL_NOREPLY: noreply@example.com
      EMAIL: smtp
      EMAIL_SMTP_HOST: mail
      EMAIL_SMTP_PORT: "1025"
      EMAIL_SMTP_ENABLE_STARTTLS: "false"
```

3486 是投票站的对外端口，容器里固定用 3000。3487 是邮件测试后台 Mailpit 的页面端口，只在局域网看，不用开放到公网。BASE\_URL 填的就是待会儿隧道给的公网地址，必须一字不差；DATABASE\_URL 里的数据库口令要跟上面 POSTGRES\_PASSWORD 完全对上，两处都换成你自己的。JWT\_SECRET 随便换一串长一点的随机字符就行。./pgdata 存的是全部投票数据，备份这个文件夹就是备份整个投票站。Fider 没有邮件功能就起不来（登录链接靠邮件发），所以这里带了一个 Mailpit 当测试邮箱，验证码都收在它里面；你自己长期用的话，把 EMAIL\_SMTP 那几行换成自己邮箱的 SMTP 授权码即可。

把 BASE\_URL 换成第一步拿到的公网地址，两个口令换成你自己的，勾选「创建项目后立即启动」，点确认，等三个容器都进入运行中。

## 三、在公网地址上初始化

浏览器打开第一步的公网地址，会自动跳到注册页。这里填三样：你的名字、邮箱、站点名称（就是投票站的名字，我填的是「我家的投票站」），点 Confirm。页面会提示等你激活——Fider 给这个邮箱发了一封确认邮件，里面有验证码和激活链接。你配置自己邮箱的话就是收在自己邮箱里；邮件里点链接或填验证码，投票站就激活好、直接进入看板。

因为管理员已经在你手里，验证码又只发到你自己邮箱，别人碰到这个公网地址也只能看，想发帖投票必须过你邮箱这一关。

## 四、发链接，大家一起投

在看板里点「Enter your suggestion here」发一条帖子，比如「周末去哪儿玩」，写上选项说明。把公网链接发到家庭群，家人点开就能看到这条帖子，点「VOTE」投票、评论区留一句话都行。他们的第一次也要用邮箱收一个验证码登录，之后就是点开就投。Roadmap 标签页可以把帖子标记成 Planned / Started / Done，家庭旅行计划照着这个流程走也挺好用。

## 数据与备份

投票站的全部数据都在项目文件夹的 pgdata 里，定期把这个文件夹复制一份到别的盘就是完整备份；恢复的时候用同一份编排重新建项目即可。

---

原文：<https://www.passnat.com/posts/fider-nas-feature-voting-board-deploy-intranet-penetration>
