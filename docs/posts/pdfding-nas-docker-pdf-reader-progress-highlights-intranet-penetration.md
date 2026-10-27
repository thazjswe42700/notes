---
title: "PdfDing：NAS 上的 PDF 阅读库，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "把 PDF 放进 NAS，浏览器里打开就能读；读到哪一页、划过哪些高亮都存在服务端，电脑和手机换着看也接得上：用 Docker Compose 部署开源 PDF 阅读库 PdfDing，用帕斯内网穿透开放到公网，含 HOST_NAME 配置和上传方法。"
canonical: https://www.passnat.com/posts/pdfding-nas-docker-pdf-reader-progress-highlights-intranet-penetration
---

## 项目介绍

PdfDing 是一个自己部署的 PDF 阅读库：把 PDF 放进 NAS，用浏览器打开就能读，不用在每台设备上装阅读软件。它把两件最麻烦的事放到了服务端：读到哪一页，以及你在书上划的高亮和写的笔记。

列表里每本 PDF 都带一条进度条和百分比，比如「58% - Page 7 of 12」，点开就回到上次停下的位置。换一台设备、换一个浏览器，进度和批注都还在。

除了阅读，它还能按工作区、合集和多级标签整理，支持星标和归档；读过的书可以导出全部高亮，也能生成只读分享链接发给朋友。所有文件和数据都在自己的 NAS 上。

项目地址：[点我跳转](https://codeberg.org/mrmn/PdfDing)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：用 Docker Compose 把 PdfDing 装上，再用 [帕斯内网穿透](https://passnat.com) 开放到公网，出门在手机上也能接着读。

这个应用的配置里要填访问地址，所以先建隧道、拿到公网地址，再创建容器。

### 一、先在应用中心建隧道

打开飞牛的「应用中心」，搜索 PassNAT，安装后打开插件，点「创建隧道」：名字随便起，不超过 12 个字符；接入节点和传输协议留默认；本地 IP 保持 127.0.0.1；本地端口填 8000，和下面编排里的端口对应。

建好点开这条隧道，按一下电源，状态变成「运行中」，详情里那一串就是公网地址，先复制下来。现在容器还没建，这个地址暂时打不开，是正常的。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 pdfding，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  pdfding:
    image: mrmn/pdfding:latest
    container_name: pdfding
    restart: unless-stopped
    ports:
      - "8000:8000"
    environment:
      - TZ=Asia/Shanghai
      - HOST_NAME=你的公网地址,你的NAS局域网地址
      - SECRET_KEY=换成你自己的随机字符串
      - CSRF_COOKIE_SECURE=FALSE
      - SESSION_COOKIE_SECURE=FALSE
      - ACCOUNT_EMAIL_VERIFICATION=FALSE
    volumes:
      - pdfding_db:/home/nonroot/pdfding/db
      - pdfding_media:/home/nonroot/pdfding/media

volumes:
  pdfding_db:
  pdfding_media:
```

HOST\_NAME 这一行填两个地址，用英文逗号隔开：前面是刚才复制的公网地址，后面是 NAS 的局域网地址。少了哪一个，从那个地址打开就会报 400，容器本身没坏。

SECRET\_KEY 换成一段你自己的随机字符串，别照抄。两个 COOKIE\_SECURE 在普通 HTTP 下必须是 FALSE；以后如果改成 HTTPS 访问，再把它们改成 TRUE。

ACCOUNT\_EMAIL\_VERIFICATION 设成 FALSE，是不配邮件服务也能注册、直接登录用的做法。如果你自己有 SMTP，可以改回 TRUE，让新账号先收验证邮件。

数据用两个具名卷：pdfding\_db 放数据库，pdfding\_media 放 PDF 原文件和封面。用命名卷不用提前建目录、也不用改属主，镜像会自己处理权限。勾上「创建项目后立即启动」，点确认。

### 三、注册并上传 PDF

容器起来之后，用 NAS 的局域网地址加 8000 端口打开，就是登录页。第一次点「注册」，填邮箱和两遍密码。因为关掉了邮件验证，注册完直接回到首页。

右上角的加号是「Add PDF」：填书名，标签用空格分开、不用带井号，不存在的标签会自动创建；合集和描述可以留空。选好文件点 Submit。要一次传很多本，切到上面的 Bulk 页签。

传完点顶部的布局图标，把列表切成 Grid，就是带封面的书架。封面上是 PDF 第一页，下面是进度条、百分比和页码。

### 四、读起来：进度、高亮和批注

点开任意一本，进入阅读器。翻页的时候进度就记下来了，关掉再打开会回到这一页；列表上的百分比也跟着变。

要划重点，点工具栏的 Highlight，选一个颜色，在文字上拖过去，再点保存。划过的句子在页面里是高亮，全部高亮还能在左侧「Annotations」里集中看，也可以导出。

手机用同一个地址打开就行，页面是竖屏排版。上班路上读几页，回家用电脑打开同一本，位置和批注都还在。

### 几点说明

**数据和升级。** 数据库和 PDF 都在 pdfding\_db、pdfding\_media 两个卷里。升级前先把这两个卷备份一份，再到 Docker 里重新拉取 latest 镜像、重启项目，数据不会丢。

**注册开关。** 家里就自己用的话，注册完自己的账号，可以在环境变量里加一行 DISABLE\_USER\_SIGNUP=TRUE，关掉公开注册，再重启项目。

**分享。** 详情页的 Share 可以生成只读链接，还能设密码和有效期。不需要的时候别开，公网地址也别到处发。

---

原文：<https://www.passnat.com/posts/pdfding-nas-docker-pdf-reader-progress-highlights-intranet-penetration>
