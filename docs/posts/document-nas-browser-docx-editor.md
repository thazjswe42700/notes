---
title: "document 飞牛 NAS 部署：浏览器改 Word/Excel/PPT"
date: 2026-10-04
description: "在 NAS 上部署 ranuts/document 在线文档编辑器，浏览器直接打开和编辑 Word、Excel、PPT，文件全程不上传。包含单容器编排与飞牛 PassNAT 隧道步骤。"
canonical: https://www.passnat.com/posts/document-nas-browser-docx-editor
---

## 项目介绍

document 是一个开源的在线文档编辑器：在浏览器标签页里直接打开和编辑 Word（docx）、Excel（xlsx）、PPT（pptx），还能打开 PDF 做批注、把各种格式导出成 PDF。编辑引擎（OnlyOffice）和格式转换器都以 WebAssembly 的形式跑在访问者自己的设备上，文件不会被上传到任何服务器，也不需要注册账号。

它是一个纯静态的网页应用：NAS 上只需要跑一个静态文件容器，不依赖数据库，不用挂载目录，内存占用极小。官方界面支持 8 种语言、编辑器界面支持 45 种语言（含简体中文），按浏览器语言自动切换。

项目源码：[ranuts/document](https://github.com/ranuts/document)

官方在线演示：[edit.chaxus.com](https://edit.chaxus.com/)

## 部署安装

下面在飞牛 NAS 上用 Docker Compose 部署，再通过 [帕斯内网穿透](https://passnat.com) 让没有装 Office 的电脑和手机也能打开它。

## 一、在飞牛 Docker 新建 Compose 项目

打开 Docker，切到 Compose，点新增项目。项目名称填 document；路径在「我的文件」里选一个空文件夹，没有就新建一个。来源选择创建 docker-compose.yml，把下面整段编排贴进去。

```yaml
services:
  document:
    image: ghcr.io/ranuts/document:latest
    container_name: document
    network_mode: bridge
    ports:
      - "3470:80"
    restart: unless-stopped
```

3470 是 NAS 对外的端口，80 是容器内的默认端口，后面创建隧道填 3470。network\_mode: bridge 让容器直接使用 Docker 的默认网络，不额外占用一个网段。整个编排没有卷、没有数据库、没有环境变量 —— 因为应用是纯前端的，文档只在浏览器的标签页里处理。

勾选创建项目后立即启动，确认，等待容器运行。

## 二、创建隧道

在飞牛应用中心搜索 PassNAT，安装并打开插件，账号登录一次。点击创建隧道，名字自己起，协议选 HTTP，子域名自己起一个好记的，本地 IP 保持默认（127.0.0.1），本地端口填 3470，创建。按隧道这一行的电源，等待状态显示运行中。

这个应用服务端不存任何数据，打开公网地址的每个人都只是在向自己的浏览器加载页面代码，文档只在各自设备上处理，所以不需要额外设置访问口令。

## 三、打开和编辑文档

用公网地址打开网站，点 Open DOCX（或直接把文件拖进页面），选一个 Word 文档。第一次打开会加载编辑引擎，之后界面按浏览器语言显示。标题、表格、图片都和本地 Office 里看到的一样。

编辑之后点保存，文件直接写回你自己的原文件（Chromium 内核浏览器支持；其他浏览器会下载一份新文件）。要发给别人，就在菜单里导出 PDF。

## 数据与隐私

所有转换、编辑、导出都在浏览器标签页里完成，文件不上传；误关标签页的内容会自动保存在你自己浏览器的「Saved documents」里，保留 7 天，可随时删除。NAS 这边除了静态页面什么都没有，也就没有需要备份的数据。

---

原文：<https://www.passnat.com/posts/document-nas-browser-docx-editor>
