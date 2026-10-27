---
title: "Rallly 约时间投票：NAS Docker 部署 + 内网穿透"
date: 2026-10-15
description: "约饭约球不用在群里一个个问哪天有空：在 NAS 上用 Docker Compose 部署开源约时间工具 Rallly，用帕斯内网穿透拿公网地址，朋友点开链接勾时间，人最齐的那天一眼看清。含 SMTP 邮箱配置和完整步骤。"
canonical: https://www.passnat.com/posts/rallly-nas-docker-group-scheduling-poll-intranet-penetration
---

## 项目介绍

Rallly 是一个开源的约时间工具。想约朋友吃饭、打球、开个会，不用在群里一个个问「哪天有空」：你建一个投票，挑几个候选时间，把链接发到群里，大家点开勾一下自己哪天行，谁有空、哪天人最齐一张表看得清清楚楚。

被邀请的人不用注册、不用装 App，打开链接填个名字就能勾；每个时间可以选「有空」「如果需要」「不行」三种。人齐了，你在管理里点「安排」，把最终时间定下来，大家再打开链接看到的就是定好的那一天。

项目地址：[点我跳转](https://github.com/lukevella/rallly)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Rallly 装上。顺序反过来不行——Rallly 发出去的邀请链接，是按编排里填的那个地址生成的，填成家里的局域网地址，朋友在外面就打不开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3000（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、准备一个能发信的邮箱

Rallly 登录不用密码，靠往你邮箱里发 6 位验证码，所以编排里要填一个能发信的邮箱（SMTP）。拿 QQ 邮箱举例：网页版 QQ 邮箱「设置 → 账号」里开启 SMTP 服务，按提示生成一个授权码。

**授权码不是邮箱密码**，下面编排里 SMTP\_PWD 填的是这个授权码。用别家邮箱的话，把 SMTP\_HOST 和端口换成它家的就行。

### 三、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 rallly，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，再改几处：

```yaml
services:
  rallly:
    image: lukevella/rallly:4
    container_name: rallly
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_BASE_URL=http://你的公网地址:端口
      - SECRET_PASSWORD=换成32位以上的随机字符串
      - DATABASE_URL=postgres://postgres:换成你的数据库口令@rallly_db:5432/rallly
      - ALLOWED_EMAILS=你的邮箱
      - SUPPORT_EMAIL=你的邮箱
      - SMTP_HOST=smtp.qq.com
      - SMTP_PORT=465
      - SMTP_SECURE=true
      - SMTP_USER=你的邮箱
      - SMTP_PWD=邮箱的SMTP授权码
    depends_on:
      - rallly_db

  rallly_db:
    image: postgres:18-alpine
    container_name: rallly_db
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=换成你的数据库口令
      - POSTGRES_DB=rallly
    volumes:
      - ./db:/var/lib/postgresql
```

**NEXT\_PUBLIC\_BASE\_URL**：填第一步拿到的公网地址，前面带上 http://。

**SECRET\_PASSWORD**：随便敲一串 32 位以上的字母数字，用来加密登录状态。

**数据库口令**：DATABASE\_URL 里冒号后面那段，和下面 POSTGRES\_PASSWORD 填同一个，只用字母和数字。

**邮箱那几行**：ALLOWED\_EMAILS、SUPPORT\_EMAIL、SMTP\_USER 都填你自己的邮箱，SMTP\_PWD 填上一步的授权码。ALLOWED\_EMAILS 的意思是只有这个邮箱能登录管理，别人拿到公网地址也注册不进来；被邀请投票的人不受影响，他们本来就不用登录。

./db 是 NAS 上存数据的文件夹，所有投票都在里面，备份就把它整个拷走。勾上「创建项目后立即启动」，点确认，两个镜像下完就起来了。

### 四、用公网地址登录、建第一个投票

浏览器打开公网地址，输入你的邮箱，点继续，邮箱里会收到一封带 6 位验证码的信，填进去就登录了。第一次进来填个名字、选一下时区和 24 小时制。主页上会提示可以设一个密码，设了以后登录就不用每次收验证码。

点「创建」，填标题和地点，在日历上点几个候选日期（每个日期默认带一个时间段，可以改时间、也可以加），点「创建投票」。

投票页右上角点「分享」，那条邀请链接就是用你的公网地址拼出来的，发到群里就行。

### 五、朋友勾时间、你定下来

朋友在手机上点开链接，每个时间点一下勾「有空」，再点一下变成「如果需要」，然后点下一步、填个名字、保存，就投完了。

你这边的投票页是一张表：每一行是一个人，每一列是一个候选时间，列头上的人数就是这个时间能来几个人。人齐了，点右上角「管理 → 安排」，选人最多的那一天点「安排」，投票页就变成「已安排」，大家再打开链接看到的就是最终时间。

### 几点说明

**邮件发不出去。** 登录页一直收不到验证码，多半是 SMTP 那几行填错了：授权码有没有当成邮箱密码填、端口和 SMTP\_SECURE 对不对得上（465 配 true）。改完编排在 Compose 里重新启动项目就生效。

**改了公网地址。** 隧道删了重建、地址变了，要把 NEXT\_PUBLIC\_BASE\_URL 一起改掉再重启，不然发出去的链接还是旧地址。

**更新。** 镜像标签用的是官方写法的 4（跟着 4.x 走），升级之前把 ./db 备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/rallly-nas-docker-group-scheduling-poll-intranet-penetration>
