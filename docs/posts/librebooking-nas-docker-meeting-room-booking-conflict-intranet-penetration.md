---
title: "LibreBooking 预约系统：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署 LibreBooking 会议室/器材预约系统：管理员建好资源，把预约页地址发到群里，同事登录后自己挑时间段，同一资源时间重叠会被当场拦下并列出冲突；支持中文界面、审批和权限分组。用帕斯内网穿透拿公网地址，在外面也能预约。含 MariaDB 编排、安装向导和管理员注册步骤。"
canonical: https://www.passnat.com/posts/librebooking-nas-docker-meeting-room-booking-conflict-intranet-penetration
---

## 项目介绍

LibreBooking 是一个自托管的会议室和器材预约系统。管理员先把房间、投影仪、相机这类「资源」建好，然后把预约页地址发到群里；同事用自己的账号登录，挑一个日期和时间段提交预约，系统会当场判断有没有撞时间——同一间会议室同一时段已经有人订了，第二条预约会被直接拦下来，并列出冲突的日期和资源。

它适合公司、实验室、社区活动室这种「东西不多但总有人抢」的场景。预约可以设成需要审批，也可以设最小/最大时长、提前预约天数、连续预约限制；资源可以分组，权限按用户和群组分配。界面自带简体中文，手机浏览器上也能正常操作。项目地址：[点我跳转](https://github.com/LibreBooking/librebooking)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 LibreBooking 用 Docker Compose 装起来。这个应用有一项配置要填公网地址（后面编排里的 LB\_SCRIPT\_URL），所以先穿透、后部署，装完就能直接用公网地址打开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来，下一步要把它填进编排。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 librebooking，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: mariadb:11
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD: change-me-root
      MARIADB_DATABASE: librebooking
      MARIADB_USER: lbuser
      MARIADB_PASSWORD: change-me-db
    volumes:
      - lb-db:/var/lib/mysql
  app:
    image: librebooking/librebooking:develop
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      LB_DATABASE_NAME: librebooking
      LB_DATABASE_USER: lbuser
      LB_DATABASE_PASSWORD: change-me-db
      LB_DATABASE_HOSTSPEC: db
      LB_INSTALL_PASSWORD: change-me-install
      LB_DEFAULT_TIMEZONE: Asia/Shanghai
      LB_SCRIPT_URL: http://nas.local:8080/Web
      LB_LOGGING_LEVEL: error
    depends_on:
      - db
volumes:
  lb-db:
```

这段编排里要改三处口令和一处地址：

**change-me-root**：数据库的 root 口令，只在安装向导里用一次。

**change-me-db**：应用连接数据库用的口令，出现两次（数据库容器和应用容器），两处要一致。

**change-me-install**：安装口令。装完之后打开 /install 页面，要先输入它才让继续。

**LB\_SCRIPT\_URL**：把 http://nas.local:8080/Web 整串换成你刚才拿到的公网地址，后面保留 /Web。具体地址在插件详情里，写 http://你的公网地址/Web，末尾保留 /Web。这个值会写进系统生成的链接里，装好之后再改比较麻烦，建议一次填对。

端口 8080 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，第一次会拉 MariaDB 和 LibreBooking 两个镜像，起来之后稍等十几秒。

### 三、跑一次安装向导

浏览器打开「公网地址/install」，例如 http://你的公网地址/install（会自动跳到 /Web/install/）。

第一屏输入编排里的安装口令 change-me-install，点 Next。第二屏确认数据库名、用户、主机（这些已经由编排里的环境变量填好），然后填 MariaDB 的 root 用户名 root 和 root 口令 change-me-root，勾上「Create the database」和「Create the database user」，点 Run Installation。看到一排 Success 和「Installation completed successfully!」就装好了。

这一屏不要勾「Import sample data」——示例数据里有一堆英文人名和演示资源，自己建干净的更省事。

### 四、注册管理员、切成中文

安装完成页下面有一个 Register 链接，点进去注册第一个账号，这个账号就是管理员。填用户名、邮箱、密码、姓名，时区选 Asia/Shanghai，按提示填图片验证码，提交后直接进控制台。

界面默认英文，点右上角「Change Language」选「简体中文」即可；这是每个账号自己的语言设置。

想关掉自助注册的话，进「程序管理 → Application Configuration」，在注册相关设置里把 allow.self.registration 关掉，以后新成员由管理员在「用户」里添加。

### 五、建资源、发地址、预约

进「程序管理 → 资源」，点「增加资源」，填名称（比如「会议室 A」）和地区（比如「三楼东侧」），保存。投影仪、相机这类器材也可以建成资源，放在同一个时间表里。

回到「时间表 → 预订」，能看到一周的格子：左边是资源，上面是日期和时段。点某个资源某一天的名字，进入新建预约页：填预约名称、选开始和结束时间、写一句说明，点「建立」。成功后页面会给出一个参考号。

把「时间表 → 预订」这个页面的地址发到群里，同事登录自己的账号就能照着订。有人订了 10:00–11:00，另一个人再提交 10:30–11:30，系统会弹出「没办法建立您的预约」，下面列出冲突的日期和资源，让他换个时间——不需要谁去盯着表格手动对。

### 几点说明

**数据在哪。** 数据库在 lb-db 这个具名卷里，备份这个卷就够了；上传的图片和附件默认也在应用容器里，需要长期保留可以再挂目录。

**邮件通知是可选的。** 不配 SMTP 也能正常注册、预约、看时间表，只是不会发邮件提醒和找回密码邮件；需要邮件通知时再在「Application Configuration」里填 SMTP。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，LibreBooking 本身不需要 HTTPS 才能用；但预约页谁拿到地址都能打开登录框，公网地址别到处发，用完了可以在插件里把隧道关掉。

**升级。** 镜像标签用的是 develop，升级前先备份数据库卷，再到 Docker 里重新拉取镜像、重启项目；大版本升级后按官方说明打开 /install/configure.php 升级数据库。

---

原文：<https://www.passnat.com/posts/librebooking-nas-docker-meeting-room-booking-conflict-intranet-penetration>


## 相关文章

- [LibreRegistration 活动报名页：NAS Docker 部署 + 内网穿透](libreregistration-nas-docker-event-signup-intranet-penetration.md)
- [Calibre-Web 自建电子书库：飞牛 NAS Docker Compose 部署，内网穿透在外面也能翻自己的书](calibre-web-self-hosted-ebook-library-nas-docker-deployment-intranet-penetration.md)
- [Jupyter Notebook：NAS Docker 部署 + 内网穿透](jupyter-notebook-nas-docker-data-analysis-intranet-penetration.md)
- [自建书签管理器Linkding极简部署指南，NAS内网穿透远程访问书签收藏库](linkding-bookmark-manager-nas-remote-access-guide.md)
- [LibrisLog 读书清单：NAS Docker 部署 + 内网穿透](librislog-nas-docker-reading-list-progress-reviews-intranet-penetration.md)
