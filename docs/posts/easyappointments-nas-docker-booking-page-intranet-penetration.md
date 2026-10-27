---
title: "Easy!Appointments 预约排班：NAS Docker 部署 + 内网穿透"
date: 2026-10-18
description: "在 NAS 上用 Docker Compose 部署开源预约排班工具 Easy!Appointments：设好服务项目和工作时间，用帕斯内网穿透拿公网地址，把预约页发给客户自己挑时间，约掉的时段别人选不到。含完整编排和安装步骤。"
canonical: https://www.passnat.com/posts/easyappointments-nas-docker-booking-page-intranet-penetration
---

## 项目介绍

Easy!Appointments 是一个开源的预约排班工具。店里、工作室、诊所都能用：你在后台设好服务项目、可约的时间段和师傅，把预约页的地址发给客户，客户点开自己挑日期和时间，填个名字电话就约好了。

约好的时段会从可选项里消失，别人再打开就选不到，不会撞车；你这边打开后台日历，谁约了哪个时间，一条一条都看得见。客户不用注册、不用装 App，打开那个地址就能约。

项目地址：[点我跳转](https://github.com/alextselegidis/easyappointments)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Easy!Appointments 装上。顺序反过来不行——它页面里的链接、发出去的预约页地址，都是按编排里填的 BASE\_URL 生成的，填成家里的局域网地址，客户在外面就打不开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 easyappointments，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，再改两处：

```yaml
services:
  db:
    image: mysql:8.4
    environment:
      MYSQL_ROOT_PASSWORD: change-me-db-password
      MYSQL_DATABASE: easyappointments
    volumes:
      - db_data:/var/lib/mysql
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h 127.0.0.1 -uroot -p\"$$MYSQL_ROOT_PASSWORD\""]
      interval: 5s
      timeout: 5s
      retries: 30

  app:
    image: alextselegidis/easyappointments:latest
    ports:
      - "8080:80"
    environment:
      # 换成你的公网地址，结尾不要带斜杠；首次安装前就要填好
      BASE_URL: "http://nas.local"
      DB_HOST: db
      DB_NAME: easyappointments
      DB_USERNAME: root
      DB_PASSWORD: change-me-db-password
      GOOGLE_SYNC_FEATURE: 0
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

volumes:
  db_data:
```

**BASE\_URL**：填第一步拿到的公网地址，前面带上 http://，结尾不要带斜杠。

**数据库口令**：db 里的 MYSQL\_ROOT\_PASSWORD 和 app 里的 DB\_PASSWORD，两个地方填同一个，只用字母和数字。

**healthcheck** 那一段不用改，它只是等数据库起来再启动应用；端口 8080 被占用的话，把冒号左边的数字换一个，隧道端口也跟着改。

勾上「创建项目后立即启动」，点确认，两个镜像下完就起来了。数据存在 db\_data 这个卷里，备份把它整个拷走就行。

### 三、第一次打开：安装向导

用公网地址打开，第一次会跳到安装页：填管理员的名字、姓氏、邮箱、用户名和密码（至少 8 位），再填公司名称、公司邮箱和公司网址，点「安装」。装完就进后台了，以后从同一个地址的「登录」进后台。

### 四、把服务项目和工作时间设成自己的

进后台先改设置：一般设置里把默认时区改成你所在的时区，时间格式选 HH:MM，一周第一天选星期一。

然后到「服务项目」里改名字、时长和时段间隔。比如剪发 30 分钟、每 30 分钟一个时段，客户看到的预约页就是按这个排的。「服务人员」里填师傅的名字，给他勾上能做的服务；工作时间按星期设好起止时间，不工作的日子把勾去掉。

时区要两处都改：一般设置里一处，服务人员资料里一处。不改的话，你看到的日历和客户看到的时段会差几个小时。

### 五、把预约页发出去、看后台日历

后台右上角点「进入预约页面」，打开的那一页就是客户看到的预约页。把它的地址发给客户，客户挑日期、挑时间，填名字和电话，点确认就约好了。

已经约掉的时段，公开页面上直接不再出现，别人点不进同一条时间。你回到后台的「日历」，这条预约就在对应的时间格子里，周视图、日视图都能看；点开还能看到客户留的联系方式。

### 几点说明

**没有配置发信邮箱。** 不配 SMTP 也能收预约，上面的步骤就是这样跑的；只是系统不会替你把确认邮件发出去，客户看到的是页面上的确认信息。需要邮件提醒的话，再到一般设置里把 SMTP 填上。

**改了公网地址。** 隧道删了重建、地址变了，要把 BASE\_URL 一起改掉再重启项目，不然页面里的链接还是旧地址。

**更新。** 镜像标签用的是 latest，升级之前把 db\_data 卷备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/easyappointments-nas-docker-booking-page-intranet-penetration>


## 相关文章

- [精美倒计时页面生成工具Easy Countdown，Docker快速部署，内网穿透远程访问，NAS极简配置](elegant-countdown-page-generator-easy-countdown-docker-rapid-deployment-intranet-penetration-remote-access-nas-simple-configuration.md)
- [自托管服务门户Easy Gate，NAS一键部署，内网穿透远程访问自建应用聚合平台](easy-gate-nas-deployment-intranet-penetration-remote-access.md)
- [Leantime 项目管理：NAS Docker 部署 + 内网穿透](leantime-nas-docker-project-management-goals-timesheets-intranet-penetration.md)
- [Moments 自建朋友圈：飞牛 NAS Docker 部署 + 内网穿透外网访问](moments-self-hosted-private-timeline-nas-docker-deployment-intranet-penetration.md)
- [document 飞牛 NAS 部署：浏览器改 Word/Excel/PPT](document-nas-browser-docx-editor.md)
