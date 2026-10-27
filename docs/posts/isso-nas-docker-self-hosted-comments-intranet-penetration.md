---
title: "Isso 评论区 NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源轻量评论系统 Isso：SQLite 存评论，网页加两行脚本就能嵌入，读者匿名留言、楼中楼回复，后台先审后发；用帕斯内网穿透拿公网地址，评论数据留在自己硬盘上。含完整编排、配置和步骤。"
canonical: https://www.passnat.com/posts/isso-nas-docker-self-hosted-comments-intranet-penetration
---

## 项目介绍

Isso 是一个开源、轻量的评论系统，用 Python 写的，评论存在一个 SQLite 文件里。它不像大型评论平台那样要求注册账号：在你自己的网页里加两行脚本，评论区就直接长在页面下方，读者填个名字就能留言。

数据在你自己的硬盘上，不经过任何第三方。它支持匿名留言、楼中楼回复，也支持先审后发：新评论先进待审队列，管理员在后台点通过，才会出现在页面上。

项目地址：[点我跳转](https://github.com/isso-comments/isso)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。先把评论服务用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Isso 装起来。

先说清楚它负责哪一半：Isso 只管评论这一半。你的网页要放在外面能打开的地方（你自己的域名或托管），外面的人先打开网页，网页再加载评论区。如果你只在局域网里用，家人连同一个网也能留言。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是评论服务的端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、准备两个文件

这套东西要两个文件：一个是 Docker Compose 编排，一个是 Isso 自己的配置文件。先看编排：

```yaml
services:
  isso:
    image: ghcr.io/isso-comments/isso:release
    container_name: isso
    environment:
      ISSO_SETTINGS: /config/isso.cfg
    ports:
      - "8080:8080"
    volumes:
      - ./isso.cfg:/config/isso.cfg:ro
      - isso_db:/db
    restart: unless-stopped

volumes:
  isso_db:
```

编排里把配置文件挂进容器，数据放在命名卷 isso\_db 里，宿主端口 8080 映射到容器 8080。端口被占用的话，改冒号左边那个数字，隧道里的本地端口也跟着改。

再看配置文件 isso.cfg：

```
[general]
dbpath = /db/comments.db
host =
    http://nas.local/

[server]
listen = http://0.0.0.0:8080

[admin]
enabled = true
password = 改成你自己的密码

[moderation]
enabled = true

[guard]
enabled = true
reply-to-self = true
```

**dbpath**：SQLite 数据库文件的位置，保持 /db/comments.db 就行，对应上面挂的卷。

**host**：允许哪个网页来用这个评论区。填你网页的访问地址；本地调试就写本地地址，外面的人要能留言，就把网页的公网地址也换行写上。地址必须和网页实际打开时一模一样（协议、域名、端口），否则浏览器会被跨域挡掉。

**password**：后台管理密码，换成你自己的。

**moderation.enabled**：打开后新评论先审后发。

### 三、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 isso，路径在「我的文件」下面挑一个空文件夹，没有就当场新建。来源选「创建 docker-compose.yml」，把上面的编排贴进去；再在这个文件夹里新建一个 isso.cfg，把配置内容贴进去。

勾上「创建项目后立即启动」，镜像下完就起来了。数据都在命名卷 isso\_db 里，重建容器不会丢。

### 四、第一次打开后台

用公网地址打开 /admin/，输入配置里设的管理密码，就进了后台。待审核、已通过、已过期三个列表都在上面，每条评论后面有编辑、删除、通过三个按钮。

### 五、把评论区嵌进你的网页

在你网页要放评论的位置，加上这两行：

```
<script data-isso="http://你的NAS地址:8080/"
        src="http://你的NAS地址:8080/js/embed.min.js"></script>
<section id="isso-thread"></section>
```

第一行从评论服务加载脚本，data-isso 指向评论服务的地址；第二行是评论区占位。刷新页面，评论区就出来了。

读者打开网页，填个名字、写句话、点提交，留言就进去了，不用注册也不用登录。邮箱和网站都可以不填。

### 六、审核和回复

**审核。** 配置里开了 moderation 之后，新评论进待审队列：作者自己会看到「评论待审核」，别的读者看不到；你在后台点一下通过，它才出现在页面上。

**回复。** 每条评论下面有「回复」，点开填内容提交，回复会缩进排在父评论下面，谁跟谁说的清清楚楚。

**一个要提前改的开关。** 匿名留言不填邮箱时，所有匿名者的哈希是一样的，Isso 会把不同的人当成同一个人；配合默认的 reply-to-self=false，就会出现「15 分钟内回复被 403 挡回去」。所以配置里把 reply-to-self 设成 true，回复才顺畅。

**评论条数上限。** guard 里 direct-reply 默认是 3，同一个 IP 对同一个线程最多 3 条一级评论，超了会返回 403。自己家小站够用，需要放宽就把它调大。

### 几点说明

**数据在哪。** 评论全在命名卷 isso\_db 的 /db/comments.db 一个文件里，备份这个卷就够了；想搬走，把这个文件拷走即可。

**升级。** 镜像标签用的是 release，升级前先备份卷，再拉新镜像、重启项目。

**明文 HTTP。** 内网穿透给的是 http 地址，Isso 本身不需要 HTTPS 就能用；但评论里可能有别人的话，公网地址别到处发，不用了可以在插件里把隧道关掉。

**只做评论。** 页面托管是另一半，本文没展开；Isso 只负责把评论存下来、显示出来、让你审核。

---

原文：<https://www.passnat.com/posts/isso-nas-docker-self-hosted-comments-intranet-penetration>
