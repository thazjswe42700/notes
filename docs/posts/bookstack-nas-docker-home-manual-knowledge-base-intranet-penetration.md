---
title: "BookStack：NAS 上的家庭知识库，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "把说明书、保修卡、装修尺寸和网络设置整理成一本本「书」，存在自己 NAS 上，浏览器和手机都能查：用 Docker Compose 部署开源知识库 BookStack，用帕斯内网穿透开放到公网，含 APP_KEY 生成、建管理员和中文设置。"
canonical: https://www.passnat.com/posts/bookstack-nas-docker-home-manual-knowledge-base-intranet-penetration
---

## 项目介绍

BookStack 是一个自己部署的文档知识库。它用「书架 → 书 → 章节 → 页面」四层来放资料：一本书记一类内容，比如家电说明书、装修尺寸、家庭网络设置；每一页就是一条具体记录。

页面里能写标题、段落、表格、清单和代码，也能传图片和附件。每次保存都会留下一个版本，改错了可以翻修订历史找回上一版；整本书还能一键导出成 HTML、PDF、Markdown 或 ZIP。

所有内容都存在自己的 NAS 上，浏览器打开就能用，电脑和手机都不用装客户端。搜索按标题和正文匹配，型号、品牌这类关键词最好用；中文短词的全文索引不是每个词都可靠，比如演示里「空调」搜不到，但「滤芯」能搜到，用型号搜最稳。

项目地址：[点我跳转](https://codeberg.org/bookstack/bookstack)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：用 Docker Compose 把 BookStack 装上，再用 [帕斯内网穿透](https://passnat.com) 开放到公网，出门在手机上也能查。

这个应用的 APP\_URL 要填访问地址，所以先建隧道、拿到公网地址，再创建容器。

### 一、先在应用中心建隧道

打开飞牛的「应用中心」，搜索 PassNAT，安装后打开插件，点「创建隧道」：名字随便起，不超过 12 个字符；接入节点和传输协议留默认；本地 IP 保持 127.0.0.1；本地端口填 8080，和下面编排里的端口对应。

建好点开这条隧道，按一下电源，状态变成「运行中」，详情里那一串就是公网地址，先复制下来。现在容器还没建，这个地址暂时打不开，是正常的。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 bookstack，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  bookstack:
    image: lscr.io/linuxserver/bookstack:latest
    container_name: bookstack
    environment:
      PUID: 1000
      PGID: 1000
      APP_URL: http://nas.local:8080
      APP_KEY: base64:换成你自己生成的密钥
      DB_HOST: db
      DB_PORT: 3306
      DB_USERNAME: bookstack
      DB_PASSWORD: 换成你自己的数据库口令
      DB_DATABASE: bookstack
    ports:
      - "8080:80"
    volumes:
      - bookstack_config:/config
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: mariadb:11
    environment:
      MYSQL_ROOT_PASSWORD: 换成你自己的数据库口令
      MYSQL_DATABASE: bookstack
      MYSQL_USER: bookstack
      MYSQL_PASSWORD: 换成你自己的数据库口令
    volumes:
      - bookstack_db:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 5s
      retries: 20
    restart: unless-stopped

volumes:
  bookstack_config:
  bookstack_db:
```

APP\_URL 填刚才复制的公网地址，后面不带斜杠。它是页面里链接和跳转的基准地址，填错会出现登录后又跳回登录页。

APP\_KEY 是一串 32 字节的随机密钥，用来加密会话。在 NAS 的终端里跑 openssl rand -base64 32，把输出前面加上 base64: 整串填进去；也可以换成自己的随机串，别照抄示例。

数据库口令要一致：上面服务的 DB\_PASSWORD 和下面服务的 MYSQL\_PASSWORD 填同一串，MYSQL\_ROOT\_PASSWORD 是数据库 root 自己的口令。示例里的占位文字全部换成自己的密码。

DB\_USERNAME 和 DB\_PASSWORD 是 BookStack 认的变量名；写成 DB\_USER、DB\_PASS 不会报配置错误，而是容器一直提示 Access denied for user database\_username。

配置和数据用两个具名卷存：bookstack\_config 放应用配置和上传的图片，bookstack\_db 放数据库。数据库服务带健康检查，BookStack 会等它准备好再启动。勾上「创建项目后立即启动」，点确认。

### 三、第一次启动和建管理员

第一次启动要跑一遍数据库迁移，大约三到四分钟。这段时间页面打不开、或者打开是 500，都是正常的，等容器日志里出现「Running migrations」跑完再说。

BookStack 默认关闭前台注册，第一个管理员要用命令行建。在飞牛的终端里执行：

```bash
docker exec -it bookstack php /app/www/artisan bookstack:create-admin --email=you@example.com --name=你的名字 --password=你的口令
```

邮箱、名字和口令换成自己的。执行成功会提示 Admin account with email ... successfully created。然后用这个邮箱和口令登录，右上角头像 →「我的账户」→ 个人资料，把语言切到「简体中文」再保存。

语言是每个用户各自设置的，界面默认是英文。切换只影响界面文字，已经写进书里的内容不会变。

### 四、建书、建页面、找东西

登录后点顶部的「书籍」→「创建书籍」，填书名和简介，封面可以以后再加。进到书里点「新页面」，标题写「客厅空调」，正文里用工具栏插表格，把型号、滤网尺寸、保修到期日填进去，保存。

同一本书里继续建页面，左侧导航会自动按顺序排好；页面多了可以再分章节，点「新章节」就行。顶部的搜索框直接输入关键词或型号，书和页面会一起列出来，点结果就跳到对应页面。

手机用同一个地址打开，页面自动变成竖屏排版，左侧导航折叠进菜单。出门在外查型号、查尺寸，和在家里电脑上看到的是同一份。

### 几点说明

**注册开关。** 想让家里其他人也有账号，在管理员设置里打开注册，或者用同样的 create-admin 命令给每个人建号；不想开放注册就保持默认关闭。

**公开访问。** 「设置 → 功能与安全」里的「允许公开访问」默认关闭。打开后不登录的人只能看 Guest 用户有权限的内容，建议保持关闭，需要分享时单独给某本书设权限。

**备份和升级。** 数据都在 bookstack\_config 和 bookstack\_db 两个卷里。升级前把卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

---

原文：<https://www.passnat.com/posts/bookstack-nas-docker-home-manual-knowledge-base-intranet-penetration>


## 相关文章

- [Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问](lychee-nas-self-hosted-photo-album-share-link-password.md)
- [PairDrop 自建隔空投送：飞牛 NAS Docker 部署 + 内网穿透跨设备传文件](pairdrop-cross-device-file-transfer-nas-docker-deployment-intranet-penetration.md)
- [自架推送服务平台PushDeer，NAS极简部署，支持内网穿透远程推送，多设备同步私密消息](self-hosted-push-notification-service-pushdeer-nas-simple-deployment-intranet-penetration-remote-push-multi-device-sync-private-messages.md)
- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [StackEdit开源Markdown编辑器完整部署指南，Docker一键启动支持内网穿透远程访问](stackedit-docker-deployment-guide-internal-penetration-remote-access.md)
