---
title: "OnlyOffice 文档服务器：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署 OnlyOffice 文档服务器：浏览器打开就能编辑 Word、Excel、PPT，几个人同时改同一份文档；装 PassNAT 插件拿公网地址，手机在外面也能接着改。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/onlyoffice-nas-docker-online-office-intranet-penetration
---

## 项目介绍

OnlyOffice 文档服务器（DocumentServer）是一个开源的在线 Office：浏览器打开一个页面，就能编辑 Word、Excel、PowerPoint，存出来的还是 docx、xlsx、pptx，桌面版 Office 可以直接打开。

它和网盘的区别在于：文档不用先下载到本地、改完再传回去。几个人可以同时打开同一份文档，改动实时同步，谁正在改哪一段也有标记；手机浏览器打开同一个页面，也能接着改。

项目地址：[点我跳转](https://github.com/ONLYOFFICE/DocumentServer)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 Docker Compose 把文档服务器装起来，再装上[帕斯内网穿透](https://passnat.com)拿一个公网地址，出门在手机上也能打开同一份文档。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 onlyoffice，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  onlyoffice:
    image: onlyoffice/documentserver:latest
    container_name: onlyoffice
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      # 换成你自己的随机字符串：字母数字混着来，越长越好
      JWT_SECRET: "change-me-to-a-long-random-string"
      JWT_ENABLED: "true"
      # 打开自带的示例页：装完就能新建文档、表格、演示文稿
      EXAMPLE_ENABLED: "true"
    volumes:
      - ./data:/var/www/onlyoffice/Data
      - ./lib:/var/lib/onlyoffice
      - ./logs:/var/log/onlyoffice
```

整段编排里只有一处必改：**JWT\_SECRET** 换成你自己的随机字符串，字母数字混着来，越长越好。它用来给服务之间的请求签名，装好之后别再改，改了正在编辑的页面会掉线。

**EXAMPLE\_ENABLED** 设成 true，装完就自带一个示例页：可以直接新建文档、表格、演示文稿，不用另接系统就能先试。镜像比较大，第一次启动要等一会儿；勾上「创建项目后立即启动」，镜像下完就会自己起来。

端口 8080 被占用的话，把冒号左边的数字换一个，后面隧道里的本地端口也跟着改。

### 二、装帕斯内网穿透，拿公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 三、第一次打开：自带示例页

浏览器打开公网地址，先看到文档服务器的欢迎页；进示例页（地址后面加 /example/），左边是新建区，右边是已有的文档列表。

点 Document 建一份文档，填个名字，编辑器就在浏览器里打开了。顶上是菜单和工具栏：字体、字号、加粗、列表、插入表格都在；底下的状态栏会显示「所有更改已保存」，不用惦记手动保存这件事。

Spreadsheet 是表格，Presentation 是演示文稿，操作方式和桌面版 Office 差不多。

### 四、几个人一起改

把同一个地址发给同事，大家打开同一份文档就能一起编辑。一个人加一行，另一个人那边马上能看到；编辑器里会标出每个人正在改的位置。

手机浏览器打开同一个地址也能编辑，出门在外补一行周报、改一个表格数字都可以。

### 几点说明

**数据在哪。** 编排挂了三个目录：./data 存 WOPI 密钥，./lib 存文档和转换缓存，./logs 存日志。备份 ./lib 和 ./data 两个目录就够了。

**示例页和正式接入。** 示例页是文档服务器自带的一个小前端，适合先试手和几个人临时协作；要接进自己的网盘、项目系统，按官方文档用 WOPI 或 API 对接，文档仍然存在原来的地方。

**升级。** 镜像标签用的是 latest，升级前先把上面两个目录备份一份，再重新拉取镜像、重启项目。

**关于 JWT。** JWT\_ENABLED 保持 true，服务之间的请求会带签名；示例页会自动用同一个密钥，不用另配。示例页本身没有账号体系，适合自己人用，公网地址别公开转发。

---

原文：<https://www.passnat.com/posts/onlyoffice-nas-docker-online-office-intranet-penetration>
