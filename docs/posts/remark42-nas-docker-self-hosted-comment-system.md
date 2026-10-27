---
title: "Remark42 自建评论系统：NAS Docker 部署 + 匿名登录 + 嵌入网页完整步骤"
date: 2026-10-19
description: "单容器、bolt 库、免注册匿名登录：Compose 怎么起、REMARK_URL 和 SECRET 怎么填、网页怎么嵌、匿名登录怎么开、数据怎么备份。"
canonical: https://www.passnat.com/posts/remark42-nas-docker-self-hosted-comment-system
---

## 项目介绍

Remark42 是一个开源的网站评论系统：在你的文章页面下面嵌一段代码，就有了一块评论区。访客不用注册账号，填个名字就能留言、回复、点赞；所有评论数据都存在你自己的 NAS 里，不经过任何第三方的评论托管。

它跟「评论框插件」不是一回事：社论托管服务把读者留言存在别人家，关站、限流、改条款都不由你。Remark42 是一个 Go 写的单容器程序，自带 bolt 数据库，不需要另装 MySQL，一个容器就跑起来。

支持匿名登录（可开关）、嵌套回复、点赞、八种排序、RSS 追更、Markdown 格式，界面自带简体中文。

项目地址：[点我跳转](https://github.com/umputun/remark42)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。单容器，一份 Compose 编排就够了。

### 一、想在外面打开，先把穿透建好

Remark42 有一个 **REMARK\_URL** 参数要填它的服务地址。只打算在局域网里自己用，填局域网地址就行；要让读者从外面也能留言，就先把这个地址定下来 —— 在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起，本地 IP 留默认的 127.0.0.1，本地端口填 **8080**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是后面要填进编排的服务地址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 remark42，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  remark42:
    image: umputun/remark42:latest
    container_name: remark42
    restart: unless-stopped
    environment:
      REMARK_URL: "https://你的公网地址"
      SITE: demo
      AUTH_ANON: "true"
      SECRET: "换成一串随机字符"
      TIME_ZONE: Asia/Shanghai
    ports:
      - "8080:8080"
    volumes:
      - remark42_data:/srv/var

volumes:
  remark42_data:
```

**REMARK\_URL**：上一步拿到的公网地址（只在局域网用就填 **http://NAS的局域网IP:8080**）。读者的浏览器要从这个地址加载评论组件，写错了评论区出不来。

**SITE**：站点 ID，随便起一个英文小写的名字（比如 demo），页面嵌入时要用同一个。

**SECRET**：一串随机字符，用来给登录凭据签名，**必填** —— 不填容器起不来，这是最容易漏的一项。

**AUTH\_ANON**：打开匿名登录，访客填个名字就能留言。注意这版参数名就是 AUTH\_ANON，旧文档里的 AUTH\_ANONYMOUS 不生效。

数据全部存在名为 **remark42\_data** 的卷里（bolt 数据库、头像、备份都在里面），容器怎么重建数据都不丢。勾上「创建项目后立即启动」，点确认。镜像几十兆，容器状态变成「运行中」就可以接网页了。

### 三、把评论区嵌到你的网页

在你文章页面的 HTML 里做三件事：

**留一个位置**：在正文下面放一个 **<div id="remark42"></div>**。

**写配置**：host 填上一步的服务地址（公网地址），site\_id 填编排里的 SITE，再加一个 locale: "zh" 让界面显示中文。

**引脚本**：把官方 embed 那段 loader 脚本贴在配置后面。

刷新页面，评论区就出现在文章下面。嵌入代码在官方文档的「frontend embedding」一节，照抄再把两个值换成你自己的就行。

### 四、用起来

访客这边：评论区右下角点「登录」，弹层里只有一个匿名登录，填上名字（中文可以）就完成了，之后打字、发送，评论马上出现在列表里；别人的评论可以点赞、可以回复，回复会缩进一层挂在下面。

评论列表右上角可以按「最新 / 最热」等排序；composer 右下角有 RSS 入口，把新回复交给 RSS 追更；写评论的框子支持 Markdown，代码、引用都排得出来。

### 几点说明

**想管评论。** 给自己当管理员：先匿名登录一次，记下自己的用户 ID（登录接口的返回里有），再把它填进编排的 **ADMIN\_SHARED\_ID** 并重建容器，你就带着管理身份了。注意这版镜像里没有单独的后台页面，管理动作都在评论区组件里做。

**备份。** 数据卷里的备份目录有它自己留的底，但最稳的是直接备份整个 remark42\_data 卷；想恢复到别的机器，把卷带过去就行。

**端口冲突。** 宿主 8080 很热门，被占了就改编排里冒号前面那个数字，REMARK\_URL 跟着一起改。

**换公网地址要慎重。** REMARK\_URL 参与读者浏览器加载组件的地址，隧道删了重建地址变了，编排里那一行也要跟着改，不然旧文章里的评论区会空。

**更新。** 拉新镜像、重建容器即可，数据在卷里不会丢。

**它是公开区域。** 评论区对互联网公开，自己的演示页别放内部服务地址、真实个人信息；开了匿名登录就难免有陌生人留言，数据在自己手里，看着不合适的处理起来也直接。

---

原文：<https://www.passnat.com/posts/remark42-nas-docker-self-hosted-comment-system>


## 相关文章

- [Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问](lychee-nas-self-hosted-photo-album-share-link-password.md)
- [台下扫码就能提问、投票：飞牛NAS 用 Docker 部署 Claper，内网穿透开放到公网](claper-interactive-presentation-docker-deployment-nas-reverse-proxy-remote-access.md)
- [开会讲课现场互动：飞牛NAS 部署开源 Claper，台下提问投票、结果实时上大屏](claper-nas-deployment-interactive-audience-feedback-remote-access.md)
- [自建网盘 Cloudreve：飞牛 NAS 一个容器就能装，文件传上去右键甩个链接，配内网穿透在外面也能收发](nas-self-hosted-cloud-storage-docker-cloudreve-intranet-penetration-remote-file-share.md)
- [自托管URL缩短器Pckd：免费开源短链服务，NAS一键部署快速追踪链接数据](pckd-self-hosted-url-shortener-free-open-source-short-link-service-nas-one-click-deployment-fast-tracking-link-data.md)
