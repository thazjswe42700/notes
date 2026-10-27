---
title: "Koillection 自建收藏品目录：NAS Docker 部署 + 照片标签 + 借出记录"
date: 2026-10-18
description: "两个容器跑起开源收藏品目录：Compose 怎么写、首次连接怎么建管理员、收藏集和藏品怎么建、标签和搜索怎么用、借出归还怎么记。"
canonical: https://www.passnat.com/posts/koillection-nas-docker-collection-catalog-loans
---

## 项目介绍

Koillection 是一个开源的收藏品目录：书、影碟、漫画、模型、手办、老相机，拍张照记进去，填上名称、数量、标签和想记的信息；借给谁了，也在这件东西下面记一笔，还回来再点一下归还。东西多了以后，最有用的是搜索：输入一个关键词，就知道它在哪个收藏集里、有没有借出去。

它分两部分：一个是网页应用，一个是 PostgreSQL 数据库，用一份 Compose 编排一起起。界面有完整的简体中文，手机浏览器打开也能用。

项目地址：[点我跳转](https://github.com/benjaminjonard/koillection)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。两个容器，一份编排就够了。

### 一、想在外面也能用，先把 8080 端口放出去

只在局域网里用，这一步可以跳过，直接看第二步。要让手机在外面也能打开，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **8080**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 koillection，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  koillection:
    image: koillection/koillection:latest
    container_name: koillection
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      - APP_ENV=prod
      - HTTPS_ENABLED=0
      - PHP_TZ=Asia/Shanghai
      - DB_DRIVER=pdo_pgsql
      - DB_NAME=koillection
      - DB_HOST=db
      - DB_PORT=5432
      - DB_USER=koillection
      - DB_PASSWORD=换成你自己的数据库密码
      - DB_VERSION=16
      - FRANKENPHP_WORKERS_NUMBER=4
      - FRANKENPHP_THREADS_NUMBER=8
    volumes:
      - ./koillection/uploads:/uploads
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:16
    container_name: koillection-db
    restart: unless-stopped
    environment:
      - POSTGRES_DB=koillection
      - POSTGRES_USER=koillection
      - POSTGRES_PASSWORD=换成你自己的数据库密码
    volumes:
      - ./postgresql:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U koillection -d koillection"]
      interval: 5s
      timeout: 5s
      retries: 10
```

两个地方要改：**DB\_PASSWORD** 换成你自己的数据库密码，上下两处要一样；冒号前面那个 8080 是 NAS 上的端口，被占了就换一个，后面的 80 不用动。

目录说明：**./koillection/uploads** 存上传的藏品图片，**./postgresql** 存数据库。数据库那段 healthcheck 是让应用等数据库就绪再起，别删掉。

里面还有两个 FrankenPHP 的参数：**FRANKENPHP\_WORKERS\_NUMBER** 和 **FRANKENPHP\_THREADS\_NUMBER**。这两个是给 PHP 进程池用的，默认值只有一个 worker，打开藏品详情页时整个站点会卡住；按上面的 4 和 8 填就正常了，别删。

勾上「创建项目后立即启动」，点确认。第一次要拉两个镜像，加起来不到两个 G，等容器状态变成「运行中」就可以打开网页了。

### 三、第一次打开：创建管理员账号

浏览器打开 **http://NAS的局域网地址:8080**，第一次会停在“首次连接”页面：填用户名、邮箱、密码，时区选 Asia/Shanghai，日期格式按自己习惯选（后面填借出日期会按这个格式解析）。

第一个注册的账号就是管理员。之后这个地址打开就是登录页，用刚才的账号密码进去。

### 四、建收藏集和藏品

左侧菜单第一项是「收藏集」。可以按柜子、按类别来建，比如「书房」放书和漫画，「模型柜」放模型和相机。点收藏集页面上方的加号新建，填个标题就行，其他的保持默认。

进到收藏集里，再点加号新建藏品：

**名称**：写清楚一点，比如「灌篮高手 完全版」「RX-78-2 高达」，搜索时用的就是它。

**图片**：点上传，选一张照片或者封面图。列表和详情页都会显示这张图。

**标签**：输入一个词回车就加上，比如「绝版」「科幻」「手办」。标签是跨收藏集的，以后按标签找东西不用记住它在哪个柜子。

**附加字段**：需要记价格、购买日期、作者、存放位置，就在「添加字段」里加一个，填完跟着这件藏品一起存。

填好点提交。想连着录好几件，用「提交并继续添加藏品」，不用每次返回。

### 五、借出和归还

打开一件藏品的详情页，点画笔旁边的「借出此藏品」：填借出日期和借用人（借用人直接写名字），提交。之后在左侧「借出记录」里能看到这一条，写着借给谁、哪天借的、已经过去多久。

东西还回来了，在借出记录里点右边那个勾，这条就进「已归还」，不再算在借出中。东西多的家庭，这一步比在聊天记录里翻「上次那本谁拿走了」省事得多。

### 六、搜索和标签

左上角的搜索框输入关键词回车，结果页分「藏品 / 收藏集 / 标签 / 愿望单 / 相册」几栏。找具体某件东西输名称，想按类别找就点标签；勾上「搜索数据字段」还会把附加字段里的内容一起搜。

### 几点说明

**备份两处。** 图片在 uploads 目录，数据在 postgresql 目录，两个都要备份。换机器时把两个目录一起拷过去，编排里的密码保持一致就能接上。

**端口要对应。** 编排里改了宿主端口，PassNAT 隧道的本地端口也要改成同一个数字。

**公网访问要保护。** 它自带账号密码，但收藏品清单毕竟是家里东西的明细，挂到公网时建议在隧道那一层再加一道网页访问口令。

**数据库别用默认口令。** 编排里那处密码一定要改，两个容器要填一样的值。

**更新。** 拉新镜像重建容器即可；数据库结构升级会在启动时自动处理，升级前先备份 postgresql 目录。

---

原文：<https://www.passnat.com/posts/koillection-nas-docker-collection-catalog-loans>


## 相关文章

- [自托管个人收藏管理系统Koillection，NAS私有部署Docker快速上手指南](koillection-nas-docker-deployment-guide.md)
- [Lute 自建外语阅读工具：NAS Docker 部署 + 生词状态怎么用](lute-nas-docker-foreign-language-reading-vocabulary.md)
- [Donetick 自建家务清单：NAS Docker 部署 + 全家分工 + 积分](donetick-nas-docker-family-chore-list-points.md)
- [DocuSeal 自建电子签署：NAS Docker 部署 + 签署链接怎么发](docuseal-nas-docker-e-signature-contract.md)
- [OpenGist 自建代码片段库：NAS Docker 部署 + 修订差异怎么看](opengist-nas-docker-snippet-share-revision-history.md)
