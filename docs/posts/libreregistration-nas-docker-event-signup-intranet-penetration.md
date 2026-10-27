---
title: "LibreRegistration 活动报名页：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源报名系统 LibreRegistration：建活动、设名额，发公开地址让朋友免注册报名，后台看名单、备注和报名时间，一键导出 CSV；用帕斯内网穿透拿公网地址，手机在外也能报名。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/libreregistration-nas-docker-event-signup-intranet-penetration
---

## 项目介绍

LibreRegistration 是一个开源的在线报名页：管理员建一个活动、设好名额，把公开页面地址发出去，参加的人不用注册账号，填一个昵称就算报名。名单、备注、报名时间都在后台，名额进度自动走，还能一键导出 CSV。

它适合桌游局、读书会、小区活动、小型讲座这种几十人的场合：不需要复杂的票务，也不需要给每个人开账号。报名时邮箱是可选的——不填也能完成报名，只是拿不到自助取消的地址；反垃圾靠一道算术题，不用接第三方验证码。

技术上是 Go 写的单容器应用，默认用 SQLite，数据就是一个数据库文件加一个附件目录；没有 Redis、没有独立数据库、也不用配邮件服务就能跑。界面目前只有英文和法文两种，后台和报名页都是同一套。

项目地址：[点我跳转](https://github.com/toulibre/libreregistration)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 LibreRegistration 用 Docker Compose 装起来。先穿透、后部署：编排里的 BASE\_URL 要填这个公网地址，装完直接用它打开，报名页生成的地址也才是对的。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8088（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 librereg，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  librereg:
    image: ghcr.io/toulibre/libreregistration:latest
    container_name: librereg
    restart: unless-stopped
    ports:
      - "8088:8080"
    volumes:
      - data:/data
    environment:
      - UPLOAD_DIR=/data/uploads
      - DATABASE_DRIVER=sqlite
      - DATABASE_PATH=libreregistration.db
      - SESSION_SECRET=换成32位以上随机串
      - CSRF_KEY=换成32位以上随机串
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=换成你的管理员密码
      - BASE_URL=https://换成你的公网地址
volumes:
  data:
```

编排里要改的是这几行：

**SESSION\_SECRET**：换成 32 位以上的随机字符串，字母数字混着来。它用来签名登录会话，装好之后别再改，改了所有人都得重新登录。

**CSRF\_KEY**：同样换成 32 位以上的随机字符串，用来防跨站请求伪造。两个密钥都别用示例里的值。

**ADMIN\_USERNAME / ADMIN\_PASSWORD**：第一次启动时自动创建的管理员账号，默认用户名 admin，密码换成你自己的。这组值只在数据库里还没有用户时生效，之后改编排不会改已有密码。

**BASE\_URL**：填第一步拿到的公网地址，带上 http:// 前缀。报名页、取消报名地址都是按它拼出来的，填错的话发出去的地址就打不开。

端口 8088 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了；首次启动会自动建库、跑一遍数据库迁移，几秒钟就好。

数据都在 data 这个具名卷里：/data/libreregistration.db 是 SQLite 数据库，/data/uploads 是活动图片。备份这个卷就够了。

### 三、第一次打开：登录后台

用 BASE\_URL 那个公网地址打开，右上角「Log in」进后台，用户名 admin，密码就是编排里 ADMIN\_PASSWORD 填的那串。登录后是 Dashboard，左边有 Events、Users、Settings。

想改密码，点左侧下方的 Password；想加别的管理员，在 Users 里建账号、给 admin 或 manager 角色。报名的人不需要任何账号，他们只接触公开的活动页。

### 四、建一个活动、设名额

点「+ New event」，填这几项：

**Title**：活动名称，比如「周末桌游夜」。下面的 Slug 留空会自动按标题生成，也可以自己改成好记的英文。

**Description / Location / Event date**：活动说明（支持 Markdown）、地点、开始时间。

**Maximum capacity**：名额上限。填了之后报名页会显示「已报 / 总数」和进度条，报满自动停止；留空就是不限人数。

**Public attendee list**：勾上以后，报名页会公开显示已报名的人（昵称和备注）。不想公开就取消勾选，名单只在后台可见。

**Registrations open**：保持勾选，报名页才能提交。

保存后回到 Events 列表，活动状态是 Open，行里直接显示「已报人数 / 名额」。点开活动的公开页，就是发给朋友的报名页。

### 五、把报名页发出去

公开页地址是 BASE\_URL 加上活动 Slug，比如 https://你的地址/event/weekend-board-game-night。把这个地址发到群里就行，不需要邀请码，也不需要对方注册。

朋友打开后看到活动时间、地点、剩余名额和已经报名的人；往下是报名表单：

**Name or nickname** 必填，随便一个昵称即可。

**Email** 可选，填了才会收到确认邮件和取消报名地址；不填也能报名，只是不能自助取消，需要管理员在后台删。

**Comment** 可选，比如「我带一副牌」。

**Anti-spam** 是一道算术题，答对了才能提交；不需要接第三方验证码。

点 Register 之后，页面上会出现「Registration confirmed!」，名额进度和公开名单立刻更新。

### 六、后台看名单、导出 CSV

回到后台 Events，点活动右边的 Attendees：姓名、邮箱（没填就是横杠）、备注、报名时间都在一张表里，顶上可以按关键词过滤。右上角「Export CSV」直接下载当前名单，列就是表里这几列，拿去排座位、买零食都行。

要删掉某条报名，点行尾的 Delete；要改活动信息，回 Events 点 Edit。删除报名会立刻释放名额。

### 几点说明

**邮件是可选的。** 不配 SMTP 时，报名照样成功，只是确认邮件和取消地址不会发出去；需要的话在编排的 environment 里加 SMTP 相关变量即可。本文的路径完全不依赖邮件。

**界面语言。** 目前内置英文和法文，右上角可以切换；没有简体中文。后台字段名就是上面这些英文，照着填即可。

**数据备份。** 备份 data 卷（数据库 + 附件）就够了。换机器时把卷导出带走，再按同样的编排起一个容器。

**升级。** 镜像标签用的是 latest，升级前先备份 data 卷，再到 Docker 里重新拉取镜像、重启项目；数据库迁移会在启动时自动执行。

**公网地址。** 内网穿透给的是 http 地址，报名页本身不要求 HTTPS；但名单里有参与者的昵称和备注，地址别到处转，活动结束可以在插件里把隧道关掉。

---

原文：<https://www.passnat.com/posts/libreregistration-nas-docker-event-signup-intranet-penetration>
