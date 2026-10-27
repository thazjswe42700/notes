---
title: "私密PDF处理工具Stirling本地部署指南，Docker容器化方案50+功能完整教程"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker 自建 PDF 工具箱 Stirling PDF：一份编排贴进 Compose 就能跑，五十多个工具合并、压缩、转 Word 全在自己家里处理，文件不出门；再用帕斯内网穿透把它开放到公网，人在外面照样用。含编排要认的四处、建隧道每一栏怎么填，以及它没有登录口令该怎么用。"
canonical: https://www.passnat.com/posts/stirling-pdf-local-deployment-guide-docker-containerization-50-plus-features-complete-tutorial
---

## 项目介绍

Stirling PDF 是一个跑在自己机器上的网页版 PDF 工具箱，右边一栏五十多个工具：合并、拆分、压缩、加页码、加水印、转格式、签名、去密码，浏览器打开就能用，不用装客户端，也不用注册账号。

它跟随手一搜就有的在线 PDF 网站最大的区别是文件不出门 —— 传进去的文件在你自己的 NAS 上处理完就删掉，不会经过别人家的服务器。合同、证件、报销单这类东西，这一点比什么功能都重要。

装在 NAS 上之后一个网页地址就顶掉了一堆装在电脑上的小工具，家里几台设备、手机上都是开同一个地址。

项目地址：[点我跳转](https://github.com/Stirling-Tools/Stirling-PDF)

## 部署安装

装在自己的 NAS 上之后，它默认只有家里能打开 —— 而临时要合并两份 PDF、把一份转成 Word，多半就发生在你不在家的时候。我家是移动宽带，没有公网 IP，所以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件，不用另外找工具。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

下面这份编排可以直接用，整段复制走。

```yaml
services:
  stirling-pdf:
    image: stirlingtools/stirling-pdf:latest
    container_name: stirling-pdf
    restart: unless-stopped
    ports:
      - "2223:8080"
    volumes:
      - ./config:/configs
    environment:
      TZ: Asia/Shanghai
      SYSTEM_DEFAULTLOCALE: zh-CN
      SECURITY_ENABLELOGIN: "false"
      SYSTEM_ENABLEANALYTICS: "false"
```

要认的地方有四处，从上往下。

端口 "2223:8080"：冒号左边的 2223 是 NAS 上对外的端口，浏览器里输的就是它，建隧道时也填它；右边的 8080 是容器里的，别改。2223 被别的服务占了就换左边那个，比如 "2280:8080"。

配置目录 ./config:/configs：配置存在编排文件旁边的 config 文件夹里，容器删了重建设置还在。这个项目处理完的文件是随手就删的，所以要留的只有这一份配置。

时区 TZ：写 Asia/Shanghai。不写的话容器里是 UTC，日志时间差八个小时。

最后三行：SYSTEM\_DEFAULTLOCALE 写 zh-CN，界面一打开就是中文，不写会跟着浏览器语言走；SECURITY\_ENABLELOGIN 写 false 是打开就能用，官方默认是 true 也就是要先登录，想要账号口令就把它改成 true 再按官方文档配初始账号；SYSTEM\_ENABLEANALYTICS 写 false 是关掉匿名统计，不写的话第一次打开会弹一个授权框。

镜像这一行跟官方 README 一致，没有钉版本号 —— 官方给的就是不带 tag 的写法，跟着上游更新走。

### 第二步：在飞牛上建 Compose 项目

回飞牛桌面打开 Docker，切到 Compose，点新增项目，名称填 stirlingpdf。

路径这儿点一下，在「我的文件」下面新建一个空文件夹，再选中它。编排和上面那个 config 目录以后都放这儿 —— 一个项目一个文件夹。

来源选「创建 docker-compose.yml」，把编排整段贴进去。勾上「创建项目后立即启动」，点创建。这个镜像有点大，拉完就跑起来了，列表里显示「正在运行」就成了。记住 2223 这个端口，下一步建隧道要填它。

### 第三步：用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面用它处理文件就都在公网地址上做了。

在飞牛的应用中心里搜 PassNAT，装上，账号登一次。打开插件点创建隧道：名字随便起；接入节点和传输协议留默认；本地 IP 这一栏留空就好，默认就是 127.0.0.1；本地端口填上面那个 2223。

点开这条隧道的详情，按最左边那个电源，状态转成运行中。面板上那一行公网地址，就是在外面打开要用的。换成公网地址打开，还是同一个界面 —— 人在外面，NAS 上的工具照样用。

## 怎么用

第一屏就是工具网格，五十多个工具按类别排着，找工具直接在上面搜名字。

合并：把两份 PDF 传进去，右边点合并，一下就拼成一份；顺序可以在列表里拖。

转 Word：传一份 PDF，右边点转换，格式选 DOCX，一两秒左边就多了一个 Word 文件，整个过程都在自己 NAS 上跑，文件没出过家门。

其它常用的还有压缩（发邮件超大小的时候）、加页码、加水印、拆成单页、以及给 PDF 去掉打开口令。处理完的结果就在页面上下载，服务器上不留。

## 一些使用上的细节

按上面这份编排跑，是打开地址就能用、没有登录口令的。开到公网上就意味着拿到地址的人都能用它处理文件 —— 所以我的用法是：要用的时候在 PassNAT 插件里把隧道打开，用完顺手关掉，内网照常用。想一直开着的话，把 SECURITY\_ENABLELOGIN 改成 true，按官方文档配上账号口令再开。

配置在项目文件夹的 config 目录里。想搬到另一台机器，把整个文件夹拷过去、用同样的编排起一个容器就行。

手机上打开同一个公网地址也能用，页面是自适应的；传文件走的是浏览器自带的选择器，拍一张扫描件直接传上去转成 PDF 也行。

---

原文：<https://www.passnat.com/posts/stirling-pdf-local-deployment-guide-docker-containerization-50-plus-features-complete-tutorial>
