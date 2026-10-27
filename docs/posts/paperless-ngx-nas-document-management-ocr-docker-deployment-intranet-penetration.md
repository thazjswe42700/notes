---
title: "Paperless-ngx 自建家庭电子档案馆：飞牛 NAS Docker 部署中文 OCR 文档管理，内网穿透在外面拍张账单就归档"
date: 2026-09-07
description: "Paperless-ngx 把账单、保修卡、说明书这类纸质文件扫进去自动 OCR 认字、打标签、全文可搜。本文用 Docker Compose 在飞牛 NAS 上部署官方镜像（SQLite、中文 OCR），再用内网穿透开放到公网，人在外面手机拍一张扔进去就能归档、随时搜到。"
canonical: https://www.passnat.com/posts/paperless-ngx-nas-document-management-ocr-docker-deployment-intranet-penetration
---

## 项目介绍

Paperless-ngx 是一个开源的自托管文档管理系统。账单、合同、保修卡、说明书这类纸质文件，拍照或扫描之后扔进去，它会自动 OCR 把文字认出来，按日期、往来方、标签归档，之后想找哪份，搜个词就秒出，不用再翻文件夹。

数据全在自己的 NAS 上，不经过任何第三方云。装在飞牛 NAS 上，家里的纸堆就变成了一个能搜的电子档案馆。

项目地址：[点我跳转](https://github.com/paperless-ngx/paperless-ngx)

## 部署安装

装在内网之后，出了门就连不上了。想在外面拍完就能扔进去，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 paperless；路径选一个空文件夹。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  redis:
    image: redis:8-alpine
    restart: always
  paperless:
    image: ghcr.io/paperless-ngx/paperless-ngx:3.1.3
    ports:
      - "8010:8000"
    volumes:
      - ./data:/usr/src/paperless/data
      - ./media:/usr/src/paperless/media
      - ./consume:/usr/src/paperless/consume
      - ./export:/usr/src/paperless/export
    environment:
      PAPERLESS_REDIS: redis://redis:6379
      PAPERLESS_ADMIN_USER: admin
      PAPERLESS_ADMIN_PASSWORD: 换成你自己的强口令
      PAPERLESS_SECRET_KEY: 换成一长串随机字符
      PAPERLESS_OCR_LANGUAGE: chi_sim+eng
      PAPERLESS_OCR_LANGUAGES: chi-sim
      PAPERLESS_TIME_ZONE: Asia/Shanghai
    restart: always
```

从上往下要认几处：paperless 用的是官方镜像 ghcr.io/paperless-ngx/paperless-ngx，带版本号 3.1.3，不用 latest；redis 是它的任务队列，两个容器就够，数据库不另起，不写 db 服务就是内置的 SQLite。ports 左边的 8010 是你浏览器要敲的端口，右边的 8000 是容器内部端口，别改。

environment 里：PAPERLESS\_ADMIN\_USER 和 PAPERLESS\_ADMIN\_PASSWORD 是首次启动自动创建的管理员，口令一定换成你自己的强口令，这个服务后面要开放到公网；PAPERLESS\_SECRET\_KEY 随便打一长串随机字符，3.x 起必须设，不设不启动；PAPERLESS\_OCR\_LANGUAGE 写 chi\_sim+eng 是中文加英文识别，下面那行 PAPERLESS\_OCR\_LANGUAGES: chi-sim 让容器启动时把中文语言包装上，镜像默认只带英语和几种欧洲语言，少了这一行中文认不出来。注意两行的拼法不一样，一个下划线一个连字符，照抄就行。PAPERLESS\_TIME\_ZONE 是时区，文档的添加日期按它显示。

四个卷都落在项目目录下：data 是数据库和索引，media 是原件，这两个要备份；consume 是「扔进去就归档」的文件夹，export 给导出用。

勾上「创建项目后立即启动」，点确认。镜像压缩后八百多兆，拉起来要等一会儿，列表里看到「运行中」就好了。

### 二、用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面的操作都在公网地址上做。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 paperless），接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 8010。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。

### 三、在公网地址上上传、搜索

浏览器打开刚才那个公网地址，账号口令就是编排里设的那个。进来是仪表盘，左边点「文档」是文档列表。

人在外面，手机拍一张发票或者保修卡，点仪表盘右上角的「上传文件」扔进去（或者直接拖到页面上），它就开始认字了。一页的照片十几秒认完，自动出现在列表里，标题默认是文件名。

顶上的搜索框敲个词（至少三个字，比如「保修卡」），下拉里当场就出结果；点开文档，「内容」这一栏就是 OCR 认出来的文字，中文也没问题。

## 几个提醒

一、公网上开着的是一个带口令的文档库，管理员口令一定用长的随机口令，别用生日、手机号这种。

二、想批量归档，把文件放进项目目录下的 consume 文件夹，Paperless 会自动把它们吃进去。

三、如果以后重建隧道、公网端口变了，浏览器里换成新地址就行，Paperless 本身不用改配置。

---

原文：<https://www.passnat.com/posts/paperless-ngx-nas-document-management-ocr-docker-deployment-intranet-penetration>
