---
title: "Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问"
date: 2026-09-23
description: "出去玩的照片建个相册，发条链接家里人点开就能看，还能加密码。本文在飞牛 NAS 上用 Docker Compose 部署 Lychee，用帕斯内网穿透开放到公网，含 APP_URL、首启管理员、自动生成密钥、时区这几处必看项。"
canonical: https://www.passnat.com/posts/lychee-nas-self-hosted-photo-album-share-link-password
---

## 项目介绍

Lychee 是一个自己部署的相册网站：建相册、传照片，想给谁看就发谁一条链接，对方不用注册账号，点开就能看。相册可以设成公开、隐藏（只有拿到链接的人能打开），也可以加一个密码，只让知道密码的人看。

跟手机备份类的相册应用不一样，它的重点是**「给别人看」**：出去玩回来建一个相册，把链接发到家里的群里，长辈点开就是一整页照片，不用装任何东西。照片全存在你自己的 NAS 上。

项目地址：[点我跳转](https://github.com/LycheeOrg/Lychee)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 装 Lychee，再用 [帕斯内网穿透](https://passnat.com) 把它开放到公网。

⚠ **顺序跟一般的项目反过来：先建隧道，再部署。** Lychee 显示照片用的是一个完整的网址，这个网址是拿编排里的 APP\_URL 拼出来的 —— 它填的不是你实际访问的那个地址，页面照样能打开，但**所有照片都是裂图**。所以要先拿到公网地址，编排里才填得对。

### 一、先建隧道，拿到公网地址（先别通电）

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3430（下面编排里对外的那个端口）。

建好之后，列表里这一行就已经分配好了公网地址（形如 IP:端口），把它记下来。**先别按电源** —— 容器还没装，现在通电没有意义。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 lychee，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去。

```yaml
services:
  lychee:
    image: lycheeorg/lychee:latest
    container_name: lychee
    ports:
      - "3430:8000"
    environment:
      APP_URL: http://换成隧道的公网地址
      TIMEZONE: Asia/Shanghai
      APP_KEY_FILE: /key/app.key
      DB_CONNECTION: mysql
      DB_HOST: lychee_db
      DB_PORT: 3306
      DB_DATABASE: lychee
      DB_USERNAME: lychee
      DB_PASSWORD_FILE: /key/db.pass
      QUEUE_CONNECTION: sync
    volumes:
      - ./uploads:/app/public/uploads
      - ./key:/key
    depends_on:
      lychee_key:
        condition: service_healthy
      lychee_db:
        condition: service_healthy
    restart: unless-stopped

  lychee_key:
    image: lycheeorg/lychee:latest
    container_name: lychee_key
    entrypoint:
      - sh
      - -c
      - |
        [ -s /key/app.key ] || echo "base64:$$(head -c 32 /dev/urandom | base64)" > /key/app.key
        [ -s /key/db.pass ] || head -c 18 /dev/urandom | base64 > /key/db.pass
        trap 'exit 0' TERM
        sleep infinity & wait
    volumes:
      - ./key:/key
    healthcheck:
      test: ["CMD", "test", "-s", "/key/db.pass"]
      interval: 3s
      timeout: 3s
      retries: 10
    restart: unless-stopped

  lychee_db:
    image: mariadb:11
    container_name: lychee_db
    environment:
      MARIADB_DATABASE: lychee
      MARIADB_USER: lychee
      MARIADB_PASSWORD_FILE: /key/db.pass
      MARIADB_RANDOM_ROOT_PASSWORD: "1"
    volumes:
      - ./db:/var/lib/mysql
      - ./key:/key:ro
    depends_on:
      lychee_key:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 3s
      retries: 10
    restart: unless-stopped
```

要改的只有三处，都在最上面：

① 冒号左边的 3430 是 NAS 上对外的端口，建隧道填的就是它；冒号右边的 8000 是容器里自己监听的端口，别改。

② **APP\_URL 换成第一步记下来的那个公网地址**，写成 http://IP:端口 的样子，末尾不要带斜杠。这一行填错不会报错，只会让照片全部裂图。

③ TIMEZONE 这一行别漏，不填的话时间全按格林尼治算，跟你这边差八个小时。⚠ 注意变量名就叫 TIMEZONE —— 官方示例编排里写的是 APP\_TIMEZONE，那只是示例里转了一道手的名字，直接写它不会生效。

**编排里一个口令都没有，这是故意的。** Lychee 启动时必须有一把 32 字节的密钥（APP\_KEY），镜像不会自己生成，缺了容器会直接退出；官方文档让你用命令行生成，而飞牛上没地方敲命令。所以编排里多了一个 lychee\_key 服务：第一次启动时它生成密钥和数据库密码，写进 ./key 文件夹，主程序和数据库都从文件里读。以后重启、重建都不会重新生成，每个人照抄出来的也都不一样。

数据都在这个项目文件夹里：./uploads 是照片，./db 是数据库，./key 是密钥。备份就把整个文件夹拷走。

勾上「创建项目后立即启动」，点确认。Lychee 的镜像六百多兆，第一次要下一会儿；下完之后数据库初始化、跑迁移，再等一分钟左右端口才通。

### 三、通电，马上建管理员

回到 PassNAT 插件，点开这条隧道按一下电源，状态转成运行中，公网地址就通了。

🔴 **紧接着用公网地址打开它。** Lychee 第一次打开会让你建管理员账号（Username / Password），而且**谁先打开谁就是管理员** —— 隧道一开，公网上任何人都能访问到这一页。所以通电之后第一件事就是把管理员建好，别拖。

进来之后在左上角菜单里找「Admin」→「Settings」，第一屏就是「Language used by Lychee」，往下拉到最底下选 zh\_CN，保存后界面大部分就是中文了（有少数几处上游还没翻译）。

### 四、建相册、传照片

回到首页，点右上角的加号，「新建相册」，起个名字。进到相册里点「上传照片」，把照片选进去（也可以直接拖进页面），几张照片几秒钟就传完了，缩略图、拍摄时间自动整理好。

### 五、把相册发给家里人

点相册右上角的下拉箭头打开相册设置，往下找到「公开」，打开它。这时候没登录的人也能看这个相册了 —— 把这个相册的网址发给家里人就行，对方不用账号。

⚠ 相册页上有个分享按钮，里面的「复制链接」在 http 的地址上点了**没有反应**（浏览器只在 https 页面上才允许网页写剪贴板）。直接复制浏览器地址栏里的网址就好，是同一个东西。

公开下面还有几个子开关：「隐藏」打开后首页上看不到这个相册，只有拿到链接的人能进；「原图」决定别人能不能看原始分辨率；「可下载」决定能不能整本下载。

只想让家里人看的话，再打开「密码保护」，设一个密码。别人打开链接会先看到「此相册受密码保护」，输对密码才看得到照片。

### 几点说明

**全程用公网地址访问。** APP\_URL 填的是公网地址，所以用局域网 IP 打开时照片会裂（浏览器会拦下跨地址的图片），这不是坏了，换回公网地址就好。

**镜像选哪个。** 官方镜像同时发在 Docker Hub（lycheeorg/lychee）和 GitHub 的镜像仓库，两边的 latest 是同一个镜像。飞牛默认连 Docker Hub 更顺，所以编排里用的是前者。

**人脸识别那个容器没放进来。** 官方完整模板里还有一个人脸识别服务，它依赖的计算库需要较新的 CPU 指令集，很多 NAS 的低功耗 CPU 跑不起来，而且不影响相册本身。

---

原文：<https://www.passnat.com/posts/lychee-nas-self-hosted-photo-album-share-link-password>
