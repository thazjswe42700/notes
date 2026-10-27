---
title: "HivisionIDPhotos 手机拍一张就出证件照：飞牛 NAS Docker 部署 + 内网穿透"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker Compose 部署 HivisionIDPhotos：上传一张正脸照，自动抠图换底，出一寸二寸标准照、高清照和六寸排版照，还能按报名网站要求调 KB 和 DPI；网页没有登录，用内网穿透 HTTP 模式的口令挡住公网再通电。"
canonical: https://www.passnat.com/posts/hivision-id-photos-professional-certificate-photo-generator
---

## 项目介绍

HivisionIDPhotos 是一个开源的证件照制作工具：上传一张正脸照，它自动抠图、换底色、按尺寸裁好，一次给你三张 —— 标准照、高清照，还有一张排版照（一张相纸上排满，拿去打印店直接冲）。

尺寸有一寸、二寸这些常用预设，底色有蓝、白、红几种，也能自己填 RGB。「高级参数」里还能把照片压到指定的 KB 大小、设 DPI —— 考试报名、社保这类网站对上传照片有大小限制，直接就能对上。

它装在自己的 NAS 上跑，照片不经过别人的服务器。界面本身是中文的。

项目地址：[Zeyi-Lin/HivisionIDPhotos](https://github.com/Zeyi-Lin/HivisionIDPhotos)

## 部署安装

装在自己的 NAS 上之后，它默认只有家里的网络能打开。证件照这种东西常常是人在外面临时要交的，家里没有公网 IP 的话，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件。具体的操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 建项目、贴编排

打开飞牛的 Docker，切到上面的 Compose，点「新增项目」，项目名填 idphoto。路径那一栏点右边的文件夹图标，新建一个空目录，选中确认。来源选「创建 docker-compose 文件」，把下面这段整个贴进去：

```yaml
services:
  idphoto:
    image: linzeyi/hivision_idphotos:latest
    container_name: idphoto
    restart: unless-stopped
    ports:
      # 左边 3437 是 NAS 上对外的端口，浏览器敲它；右边 7860 是容器里的
      - "3437:7860"
```

要认的就一处：ports 冒号左边的 3437 是 NAS 上对外的端口，浏览器打开和建隧道填的都是它，被占了就换一个；右边的 7860 是容器里的网页端口，不要改。镜像用官方的 linzeyi/hivision\_idphotos，下载量将近 1 GB。没有数据库，也不用挂目录。

勾上「创建项目后立即启动」，点确认，拉完镜像它自己就起来了。

### 两个环境变量别乱加

网上有些教程会让你加 RUN\_MODE=beast。那是官方说的「野兽模式」，模型常驻内存、二次出图更快，但官方建议 16GB 内存以上才开 —— 一般 NAS 就 8GB，别开。

还有 FACE\_PLUS\_API\_KEY / SECRET：那是旷视的在线人脸检测接口，要自己去注册账号拿 key，而且照片会发到外面去。不设的话人脸检测就用本地模型，完全够用。

### 配上内网穿透（先设口令，再通电）

HivisionIDPhotos 的网页没有登录，谁拿到地址都能打开用。所以开放到公网的时候，给这个地址加一道口令：

飞牛的应用中心里搜 PassNAT，装上插件打开，点「创建隧道」：名字随便起，协议选 HTTP，本地地址留它默认的不要动，端口填 3437；子域名自己起一个，顶级域名在下拉里挑一个带「自动解析」的；最下面「网页访问用户名 / 密码」两栏填上 —— 选 HTTP 协议才有这两栏。

⚠ 网页访问密码最多 12 个字符，超了点创建是没有提示的，隧道建不出来。

先把口令填好，再去通电：点开这条隧道的详情，按一下电源，等它转成「运行中」。面板上「公网连接」那一行就是分给你的地址 —— HTTP 模式给的是一个域名，不是 IP 加端口。以后任何人打开这个地址，都要先过这道口令。

这道口令挡的是公网这条路；家里局域网直接连 NAS 的 3437，还是不用口令的。

## 做一张证件照

打开公网地址（过了口令）就是它的页面。上面那排「人脸检测模型」默认是 retinaface-resnet50，在 NAS 这种低功耗 CPU 上它要好几秒；换成 mtcnn 快得多，效果一般照片够用。下拉里那个「face\_plusplus」是在线接口，没配 key 不要选。

左边点上传，挑一张正脸照（背景杂一点也没关系，它会抠）。底色选蓝色，尺寸挑一寸，点「开始制作」。右边会出来三张：标准照、高清照、排版照，每张右上角有下载按钮。

要换底色就点红色或白色，再点一次「开始制作」。「打印排版」页签里能选六寸、五寸、A4 这些相纸；「高级参数」页签里能设 KB 大小和 DPI。

页面最下面有一排示例照片，点一下会直接拿它来做 —— 自己用的时候上传你自己的照片就行。

### 实测数据

在一台 N5105（没有 AVX2）的飞牛 NAS 上：容器常驻内存三百多兆；用 mtcnn 出一张，模型推理 1.5 秒左右；用默认的 retinaface-resnet50 是 5 到 6 秒。第一次点「开始制作」要加载模型，会慢一些。

---

原文：<https://www.passnat.com/posts/hivision-id-photos-professional-certificate-photo-generator>
