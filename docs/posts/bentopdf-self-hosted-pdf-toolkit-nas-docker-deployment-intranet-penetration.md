---
title: "自建 BentoPDF：飞牛 NAS Docker 部署隐私优先的 PDF 工具箱，内网穿透外网访问"
date: 2026-09-04
description: "在线 PDF 网站要把文件传上去才能处理。BentoPDF 全程在浏览器里跑，文件不出本机。本文在飞牛 NAS 上用 Docker Compose 部署 BentoPDF，再用内网穿透开放到公网，在外面也能用。"
canonical: https://www.passnat.com/posts/bentopdf-self-hosted-pdf-toolkit-nas-docker-deployment-intranet-penetration
---

## 项目介绍

平时要合并几页 PDF、压一下体积、加个水印，第一反应往往是搜一个在线 PDF 网站。问题是这类站点的处理都在它自己的服务器上完成——文件得先上传，处理完还可能在对方缓存里留一份。合同、发票、体检报告这类东西，传出去心里总是不踏实。

BentoPDF 是一个开源的、隐私优先的自托管 PDF 工具箱。它把 PDF 处理全放在浏览器里用 WebAssembly 完成，文件不上传服务器——服务端只是一个静态站点，从头到尾不碰你的文件。

功能覆盖得挺全：合并、分割、压缩、旋转、裁剪、提取页面、加水印、页码、签名、各种格式互转（图片、Word、Excel、PPT、EPUB 等转 PDF），还有一个可视化的 PDF 多功能工具，合并、排序、删页、旋转在同一个界面里拖着做。界面自带中文。

项目地址：[点我跳转](https://github.com/alam00000/bentopdf)

## 部署安装

BentoPDF 装在内网之后，出了门就访问不到了 —— 我家是移动宽带，没有公网 IP。想在外面也能用，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、先挑对镜像

官方发了两个镜像，别拿错：

· ghcr.io/alam00000/bentopdf-simple:latest —— Self-Hosted build，自建就用这个。PDF 工具一个不少，只是去掉了官网首页那些营销区块（介绍页、常见问题、页脚）。官方文档原话是「拿不准就拉这个」。

· ghcr.io/alam00000/bentopdf:latest —— Commercial build，是 bentopdf.com 官网自己在跑的那一份，带完整的营销站，给持有商业授权、做对外公开部署的人用。

自己在 NAS 上用，选前面那个 simple 版就对了。

### 二、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 bentopdf；路径选一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  bentopdf:
    image: ghcr.io/alam00000/bentopdf-simple:latest
    container_name: bentopdf
    restart: unless-stopped
    ports:
      - "3800:8080"
```

要认的就两处：image 那行末尾是 simple；ports 那行左边的 3800 是你浏览器要敲的端口，右边的 8080 是容器内部的端口，别改。3800 被占用的话换成别的没占用的端口就行。

这个编排没有环境变量、没有数据卷、也不需要数据库——BentoPDF 不在服务端存任何东西，所以不用挂载目录。

勾上「创建项目后立即启动」，点确认。镜像压缩后一百多兆，拉起来很快。

### 三、内网先打开看看

浏览器访问 http://你的NAS地址:3800 ，看到工具列表就是好了。随便点一个「合并 PDF」，把两个文件拖进去点合并，一秒就出结果并直接下载。

上传区域下面那行「您的文件从未离开您的设备」不是一句口号：打开浏览器的开发者工具看网络请求，处理过程中没有任何文件上传。

### 四、用内网穿透开放到公网

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 bentopdf），接入节点和传输协议都留默认；「本地IP」这一栏**留空不用填**，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 3800。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。拿这个地址在外网打开，界面和内网完全一样。

顺带说一句，这个组合有点巧：BentoPDF 的处理全在浏览器里做，穿透只负责把页面本身送出去，PDF 文件既不经过 NAS，也不经过穿透的服务器。

## 几个提醒

一、部分功能（比如 OCR、压缩、格式转换）用到的 WASM 模块是浏览器在运行时从 CDN 拉的，第一次用某个功能会稍微等一下。需要完全离线的话，官方仓库里有 air-gapped 打包脚本。

二、镜像跟着上游更新得挺勤，想锁版本就把 latest 换成具体的版本号 tag。

三、BentoPDF 采用 AGPL-3.0 与商业授权双许可。自己用、内部用走 AGPL 就行。

---

原文：<https://www.passnat.com/posts/bentopdf-self-hosted-pdf-toolkit-nas-docker-deployment-intranet-penetration>
