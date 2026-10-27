---
title: "自建文件转换器 VERT：飞牛 NAS Docker 部署，图片音频在浏览器里转，文件不上传"
date: 2026-09-19
description: "在飞牛 NAS 上用 Docker Compose 部署 VERT 自建文件格式转换器，图片音频文档在浏览器里转、文件不上传；配上内网穿透，在外面也能用。视频那条路为什么用不了，文中单开一节讲清楚。"
canonical: https://www.passnat.com/posts/vert-self-hosted-file-converter-nas-docker-vertd-intranet-penetration
---

## 项目介绍

VERT 是一个开源的文件格式转换器，装在自己 NAS 上，用浏览器打开就能用。它跟网上那些在线转换站最大的区别是：\*\*转换是在你自己浏览器里完成的\*\*，文件不会被传到任何服务器上。光图片就有一百三十多种目标格式，音频、文档也能转。

几个常见的用法：从网页上存下来的 webp 打不开，转成 jpg；给网站配图压成小图或者 avif；音频换个容器格式；Markdown 导成 docx。实测每一种都是一秒上下就完事。

⚠ \*\*视频这条路在本文这套装法里用不了\*\*，原因不是装错了，是浏览器的限制 —— 后面单开一节讲清楚，包括为什么换个穿透方案也解决不了。

项目地址：[VERT-sh/VERT](https://github.com/VERT-sh/VERT)

## 部署安装

装在自己的 NAS 上之后，它默认只有家里的网络能打开。可要转个文件这事儿经常发生在外面 —— 别人发来的图打不开、要交的文件格式不对。家里没有公网 IP 的话，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件，不用另外找工具。具体的操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。下面这套流程在飞牛 NAS 上从头到尾走一遍。

打开飞牛的 Docker 应用，切到上面的 Compose 标签，点新增项目，项目名填 vert。

路径这一栏点右边的文件夹图标，在自己的文件夹里新建一个空目录再选中它。

来源选「创建 docker-compose 文件」，把下面这段整个贴进去：

```yaml
services:
  vert:
    image: ghcr.io/vert-sh/vert:latest
    container_name: vert
    restart: unless-stopped
    ports:
      - "3405:80"
```

\*\*一共就七行，一个服务。\*\* ports 左边的 3405 是 NAS 上对外的端口，右边的 80 是容器里的，右边那个不要改。

\*\*不需要挂载任何目录，也不需要填环境变量，更没有数据库。\*\* 因为 VERT 根本不在服务端存东西 —— 它就是一个静态网站，真正干活的代码跑在你的浏览器里。镜像四十多兆。

最后勾上「创建项目后立即启动」，点确认，拉完会自己起来。

### 配上内网穿透

容器跑起来之后紧接着把隧道建上。在飞牛的应用中心里搜 PassNAT 装上，打开插件，账号登一次。点创建隧道：名字随便起，协议选 TCP，本地地址那一栏留它默认的不要动，端口填 3405。

建好之后点开这条隧道的详情，把电源按一下，等状态转成运行中，面板上那一行就是分给你的公网地址。后面所有操作都在这个地址上做。

### 实际转两个文件

先转张图片：把一个 webp 拖进去，目标格式选 jpg，点 Convert all。实测一秒转完，下载下来的文件头是 ff d8 ff —— 是真的 JPEG，不是把原文件换个后缀还给你。

再转段音频：mp3 拖进去选 wav，同样一秒。这两步全程在浏览器里跑，抓包看的话，\*\*转换过程中一条外部请求都没有\*\*。

### 🔴 视频那条路为什么用不了

VERT 的视频转换要用到浏览器的 WebCrypto 接口（`crypto.subtle`）来算文件摘要。而浏览器规定：\*\*只有「安全上下文」才提供这个接口\*\* —— 也就是 HTTPS，或者 localhost。用普通的 http 地址打开时，`crypto.subtle` 干脆是 undefined，点转换之后什么都不会发生：不报错、不提示、按钮变灰，页面上连一句错误信息都没有。

\*\*这不是内网穿透的问题，是 http 的问题。\*\* 这一点很容易搞混，所以说清楚：在家里用局域网地址（也是 http）打开，同样转不了视频。换成别的穿透方案、别的端口，只要还是 http，结果都一样。

那配上 HTTPS 行不行？理论上行，实际上这一步对大多数人不是一步能过的事：PassNAT 的 HTTPS 模式虽然能挑一个自动解析的域名，但\*\*证书和密钥要你自己提供\*\*，而那个域名的 DNS 又不在你手里，签证书这一步本身就挺折腾。所以本文这套装法就把视频这条路留着不用。

顺带说一句：\*\*正因为它在算摘要那一步就停住了，你的视频也不会被偷偷传出去。\*\* 实测点完转换，无论是官方的转码服务器还是别的地址，一条请求都没有发出去。

### 两个要说清楚的地方

第一，\*\*它并不是一点网都不用\*\*。转换用的内核（WebAssembly）没有打进镜像里，第一次用的时候会从公共 CDN 上拉一次；页面打开时还会连一下地理定位和官方的转码服务器探活。所以准确的说法是：\*\*你的文件不上传到别人的服务器\*\*，而不是「一个请求都不往外发」。

第二，用 http 打开的时候，页面右下角会弹一条提示，说当前不是安全上下文、部分功能可能不正常。这条提示是真的，上面那节讲的视频就是被它挡住的那一项。不过图片、音频、文档这几类转换实测照常能用，不受影响。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/vert-self-hosted-file-converter-nas-docker-vertd-intranet-penetration>


## 相关文章

- [自建私密文件转换服务器Morphos，支持图片文档电子书格式互转，Docker一键部署NAS](private-file-conversion-server-morphos-image-document-ebook-docker-nas.md)
- [自建文件服务器 GoHTTPServer Docker极简部署，NAS内网穿透远程访问文件](self-hosted-file-server-gohttpserver-docker-deployment-nas-intranet-penetration-remote-access.md)
- [copyparty 自建文件服务器：飞牛 NAS Docker 部署 + 内网穿透外网访问](copyparty-self-hosted-file-server-nas-docker-deployment-intranet-penetration.md)
- [自建远程文件浏览器Mikochi，极简部署NAS文件管理，支持内网穿透外网访问](mikochi-remote-file-browser-nas-file-management-intranet-penetration.md)
- [轻量级文件服务器 DUFS 快速部署指南 NAS Docker 一键启用文件共享搜索上传](lightweight-file-server-dufs-quick-deployment-guide-nas-docker-one-click-enable-file-sharing-search-upload.md)
