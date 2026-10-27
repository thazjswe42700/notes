---
title: "开源个人关系管理系统Monica私密部署指南，NAS一键Docker快速安装配置教程"
date: 2025-10-16
description: "Monica是一款开源个人关系管理系统，基于Laravel和Vue.js构建。它帮助用户记录联系人、管理人际互动、设置提醒和追踪活动日志。这是一个私密的个人档案库，特别适合内向者和需要管理生活回忆的用户。支持Docker快速部署，可通过配置端口映射和存储实现外网访问。"
canonical: https://www.passnat.com/posts/open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install
---

## 项目介绍

Monica 是一个记人的工具，开源的个人关系管理系统。谁过生日、爱吃什么、上次见面聊了什么、礼物想送什么，都存在它里面。它不是一个社交网络，东西只存在你自己的机器上，别人看不到。

过年走亲戚之前翻一眼，就不会空着手进门、也不会把人家的喜好记错。

项目地址：[点我跳转](https://github.com/monicahq/monica)

## 部署安装

要在外面也能打开 Monica，可以配一个内网穿透服务。这里用[帕斯内网穿透](https://passnat.com)，先注册一个账号备用。具体操作可以参考[这份详细教程](https://www.passnat.com/posts/p)，全平台怎么配都写清楚了。

### 第一步：写好 docker-compose

Monica 要一个数据库，所以编排里是两个服务：app 是它自己，db 是 MariaDB。整段抄下去就行：

```yaml
services:
  app:
    image: monica:latest
    depends_on:
      - db
    ports:
      - "3495:80"
    environment:
      - DB_HOST=db
      - DB_USERNAME=monica
      - DB_PASSWORD=换成你自己的口令
    volumes:
      - data:/var/www/html/storage
    restart: unless-stopped

  db:
    image: mariadb:11
    environment:
      - MYSQL_RANDOM_ROOT_PASSWORD=true
      - MYSQL_DATABASE=monica
      - MYSQL_USER=monica
      - MYSQL_PASSWORD=换成你自己的口令
    volumes:
      - mysql:/var/lib/mysql
    restart: unless-stopped

volumes:
  data:
  mysql:
```

**几个地方说明一下。**

镜像用官方的 monica:latest，不写死版本号。冒号前面的 3495 是给 NAS 的端口，等下建隧道要填它。

数据库那三行，密码两处填你自己的，两处要一模一样。这个库只在 NAS 内部跑，不对外，所以不用改端口。

最后是两个具名卷，联系人资料和数据库都存在里面，删容器不会丢。

**编排里不用写 APP\_KEY。**Monica 镜像第一次启动会自己生成一把，写进去反而容易跟别人的重复。

### 第二步：在飞牛上新增项目

打开飞牛的 Docker，点新增项目，名字填 monica。路径挑一个空文件夹就行，没有就当场新建一个。来源选「创建 docker-compose」，把刚才那段整段粘进来。

右边勾上「创建后立即启动」，点确认，等它把镜像拉下来跑起来。

### 第三步：开放到公网

容器跑起来之后，接着把它开放到公网。飞牛的应用中心里搜 PassNAT，把这个插件装上。

插件里点创建隧道。名字随便起一个，端口填 3495，本地地址留默认不要动。建好之后这一行就有公网地址了。

最左边是电源键，点一下。绿点亮起来，公网地址就通了。

### 第四步：第一次打开先建管理员

用公网地址打开 Monica，第一次来会让你建个账号，这个就是管理员。填完点注册就进来了。注册口在建好第一个账号之后会自动关掉，所以**隧道一开着，先把管理员建好。**

### 第五步：开始记人

进来先加人。小姨、表哥，挨个添进去，谁的生日是哪天顺手就写上。

点开一个人，这一屏就全了：生日放在生活事件里，爱吃什么记在食物偏好，往下拉，礼物想送什么也有地方写。上次聊了什么还能记一笔，下次见面接着上次聊。

### 几个小提示

数据库密码两处要填成一样的，两处都是「换成你自己的口令」那种写法，自己改掉。

编排里的端口 3495 换成你自己空着的也行，但冒号前面后面要跟隧道里填的对上。

数据在两个具名卷里，Docker 那边删项目不会把联系人删掉；真要清干净才去删卷。

本篇教程结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install>
