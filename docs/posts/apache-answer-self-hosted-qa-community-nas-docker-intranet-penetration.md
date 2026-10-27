---
title: "自建问答社区 Apache Answer：飞牛 NAS Docker 部署，家里人问过的事记一次"
date: 2026-09-20
description: "在飞牛 NAS 上用 Docker Compose 部署 Apache Answer 自建问答社区，家里人、同事问过的事记一次，下回直接搜出来；配上内网穿透，在外面也能提问和回答。装完第一件事要把邮箱验证关掉，文中单开一节讲清楚。"
canonical: https://www.passnat.com/posts/apache-answer-self-hosted-qa-community-nas-docker-intranet-penetration
---

## 项目介绍

Apache Answer 是一个开源的问答社区程序，Apache 软件基金会的顶级项目。它解决的是一件很具体的事：\*\*同一个问题不用回答两遍\*\*。家里人问过的、同事问过的，在群里答一次就沉下去了，下次还得再答一遍；记在这儿，下回直接搜出来。

它跟笔记软件不一样的地方在于：内容是\*\*一问一答\*\*的形状，可以打标签、可以采纳答案、可以全文搜。谁都能提问，会的人来答，答案就沉淀下来了。

项目地址：[apache/answer](https://github.com/apache/answer)

## 部署安装

装在自己的 NAS 上之后，它默认只有家里的网络能打开。可提问这件事经常发生在外面 —— 家里人在公司、孩子在学校，想问的时候打不开就等于没有。家里没有公网 IP 的话，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件，不用另外找工具。具体的操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。下面这套流程在飞牛 NAS 上从头到尾走一遍。

打开飞牛的 Docker 应用，切到上面的 Compose 标签，点新增项目，项目名填 answer。

路径这一栏点右边的文件夹图标，在自己的文件夹里新建一个空目录再选中它。

来源选「创建 docker-compose 文件」，把下面这段整个贴进去：

```yaml
services:
  answer:
    image: apache/answer:latest
    container_name: answer
    restart: unless-stopped
    ports:
      - "3408:80"
    volumes:
      - ./data:/data
```

\*\*一共九行，一个服务。\*\* ports 左边的 3408 是 NAS 上对外的端口，右边的 80 是容器里的，右边那个不要改。镜像是官方的 apache/answer，五十多兆。

🔴 \*\*最后那一行 volumes 千万别省。\*\* 问答的内容、账号、上传的图片全在容器里的 /data，不挂出来的话删一次容器就全没了。

⚠ 这里有个和上游文档不一样的地方：官方给的编排用的是\*\*具名卷\*\*（answer-data:/data），而飞牛的 Docker 界面里没有「存储卷」这一页，具名卷建了就删不掉。所以这份改成了 \*\*./data:/data\*\* 这种绑定挂载 —— 目录就落在你刚才选的项目目录里，将来想重来一次，把目录清空就行。

最后勾上「创建项目后立即启动」，点确认，拉完会自己起来。

### 配上内网穿透

容器跑起来之后紧接着把隧道建上。在飞牛的应用中心里搜 PassNAT 装上，打开插件，账号登一次。点创建隧道：名字随便起，协议选 TCP，本地地址那一栏留它默认的不要动，端口填 3408。

建好之后点开这条隧道的详情，把电源按一下，等状态转成运行中，面板上那一行就是分给你的公网地址。\*\*后面的初始化全在这个地址上做\*\*，别在内网做完再说。

### 第一次打开：装一遍

用公网地址打开，它会先带你走一遍安装：

第一步选语言，下拉里有\*\*简体中文（100%）\*\*，选它。

第二步选数据库。默认是 MySQL，\*\*改成 SQLite\*\* —— 这样就不用再起一个数据库容器，库文件会落在 /data/answer.db，也就是你刚才挂出来的那个目录里。

第三步填站点名字、给自己建管理员账号。这一页还有一个「网站网址」，它会自动填成你当前访问用的地址 —— 既然你是从公网地址进来的，这里就该是公网地址。⚠ 顺手确认一下，填成内网地址的话，站内的链接以后都会指着内网。

### 🔴 装完第一件事：把邮箱验证关掉

这一步很容易被跳过，但不做的话后面会卡住。

Answer 出厂\*\*要求新用户验证邮箱\*\*。家里人拿公网地址注册完，会停在「就差一步！我们发送了一封激活邮件到 …」这一屏，账号是不能用的 —— 你去提问页会被弹回登录页。而你并没有配发信服务器，那封邮件\*\*永远不会来\*\*。

关掉的地方在：后台管理 → 登录，把「Require email verification」那一项的勾去掉，保存。关掉之后再注册，提交完直接就是登录状态，可以马上提问。

⚠ 这一项的文案是英文的（`Email verification` / `Require email verification`），而它上下两项都是中文。界面\*\*基本\*\*都是中文，但这一处还没翻译，别找不到。

### 两个要说清楚的地方

第一，\*\*头像是从 gravatar.com 拉的\*\*。每打开一页都会往那儿发一次请求，拿的是你邮箱的哈希对应的头像。用假邮箱或者没注册过 Gravatar 的邮箱，出来的是一张自动生成的几何图案。所以准确的说法是：\*\*内容和账号都在你自己的 NAS 上\*\*，而不是「一个请求都不往外发」。

第二，装完\*\*不是空站\*\*，自带一条英文的示例问题「What is a tag?」，是用来演示标签的。不用删，自己发几条问题它就被挤下去了。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/apache-answer-self-hosted-qa-community-nas-docker-intranet-penetration>


## 相关文章

- [Web-Check 网站体检：NAS Docker 部署 + 内网穿透](open-source-website-security-analysis-tool-web-check-docker-one-click-deployment-40-plus-deep-detection-local-run.md)
- [Lychee 自建私人相册：飞牛 NAS Docker 部署 + 内网穿透外网访问](lychee-nas-self-hosted-photo-album-share-link-password.md)
