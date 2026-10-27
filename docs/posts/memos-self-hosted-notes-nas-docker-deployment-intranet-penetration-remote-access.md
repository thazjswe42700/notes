---
title: "自建备忘录 Memos 飞牛 NAS 极简部署，Docker 一把梭随手记，内网穿透在外面照样翻"
date: 2026-09-07
description: "Memos 是开源自托管的备忘录应用，Markdown 写作、标签分类、时间线浏览。本文在飞牛 NAS 上用 Docker Compose 部署 Memos，并用帕斯内网穿透把 5230 端口开放到公网，注册管理员、切换中文界面和日常记录全程在公网地址上完成，在外面也能随手记。"
canonical: https://www.passnat.com/posts/memos-self-hosted-notes-nas-docker-deployment-intranet-penetration-remote-access
---

## 项目介绍

Memos 是一款开源的自托管备忘录应用，主打「想到什么马上记一条」。写完的内容按时间线倒序排好，正文前面加个井号就是标签，左边侧栏点一下标签就能把同一类的筛出来。整个服务是单个 Go 容器加一个 SQLite 文件，装在自己的 NAS 上，笔记始终在你自己手里。

项目地址：[点我跳转](https://github.com/usememos/memos)

## 部署安装

装在 NAS 上的服务，默认只有家里的网能打开。我家是移动宽带，没有公网 IP，配 IPv6 也嫌麻烦，所以这篇里容器一跑起来就直接用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，后面的注册管理员、改语言、记第一条，全部在公网地址上做。

### 第一步：在飞牛 Docker 里新建 Compose 项目

打开飞牛的 Docker 应用，切到 Compose 这一页，点「新增项目」，项目名称填 memos。路径那一栏点开目录选择器，在里面新建一个空文件夹再选中它 —— 要选空目录，别选已经放着 docker-compose.yml 的那种 —— 飞牛会提示「所选择的路径已包含 docker-compose 配置文件」。

### 第二步：把编排贴进去

「来源」选「创建 docker-compose.yml」，把下面这段整个贴进编辑框：

```yaml
services:
  memos:
    image: ghcr.io/usememos/memos:0.30.0
    container_name: memos
    ports:
      - 5230:5230
    volumes:
      - ./memos:/var/opt/memos
    restart: unless-stopped
```

要认的就三处：最上面 image 的版本号照着填，别用 latest；中间 ports 是端口 5230；下面 volumes 是数据目录，笔记和 SQLite 都存在这儿。勾上「创建项目后立即启动」，点确认。镜像压缩后只有二十多兆，很快就拉完了。

### 第三步：用 PassNAT 开放到公网

容器跑起来之后，先别急着在家里的网上打开。直接开飞牛里的 PassNAT 插件（应用中心搜 PassNAT 就能装，装上账号登一次就行），在插件里点「创建隧道」：起个名字，传输协议选 TCP，本地 IP 那一栏留空就是 127.0.0.1，不用动，端口填 5230。建好之后在隧道详情里按一下电源，状态转成运行中，详情面板里那条公网连接就是在外面要用的地址。

### 第四步：注册管理员，再把界面切成中文

用刚拿到的公网地址打开 Memos，第一次进去是「初始化实例」页，让你建管理员账号。这一步要快 —— 隧道已经开着但管理员还没建，谁先进来谁就是管理员，所以填好用户名和密码马上点「创建管理员账号」。

注册完进去，界面默认是英文的（新账号的语言项出厂就是 English，不跟浏览器语言走）。点左下角头像进设置，在「偏好设置」里把 Language 选成简体中文，界面当场就切过来了。

## 怎么用

进去就是顶上那个输入框，想到什么直接写，写完点保存就排到时间线最上面。正文里加个「#买菜」这样的标签，左边侧栏的标签列表里就会多一项，点一下就把这一类全筛出来 —— 买菜的、折腾 NAS 的、想看的书，各归各的。

因为整条链路都跑在公网地址上，人在外面随手记一条，回家打开还是同一个库，接着整理就行。

---

原文：<https://www.passnat.com/posts/memos-self-hosted-notes-nas-docker-deployment-intranet-penetration-remote-access>


## 相关文章

- [写公众号不用再手动排版：飞牛 NAS 用一个容器装 doocs/md，Markdown 实时出公众号样式，配内网穿透在公司也能写](professional-wechat-markdown-editor-nas-simple-deployment-doocs-md-ai-intelligent-layout-beautiful-graphic-content.md)
- [DailyTxT 加密日记：飞牛 NAS Docker 部署 + 内网穿透外网访问](private-diary-encryption-nas-local-deployment-dailytxt-intranet-penetration-remote-access.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
- [简单图床 EasyImages 自建：飞牛 NAS Docker 部署 + 内网穿透外网访问](easyimage-self-hosted-image-hosting-nas-docker-deployment-intranet-penetration.md)
- [开源评论引擎Comentario一键部署指南，NAS轻松搭建私人讨论系统，Docker快速启动](open-source-comment-engine-comentario-deployment-guide-nas-private-discussion-system-docker-quick-start.md)
