---
title: "Rustpad 协同编辑器：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源协同编辑器 Rustpad：打开就生成文档，把带文档号的地址发给朋友，两边逐字实时同步、能看到谁在线；配 SQLite 持久化后重启不丢。用帕斯内网穿透拿公网地址，手机也能接着改。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/rustpad-nas-docker-collaborative-code-editor-intranet-penetration
---

## 项目介绍

Rustpad 是一个开源的协同文本编辑器，定位很简单：几个人同时改同一份文档，改动实时出现在所有人的屏幕上。它基于 operational transformation 算法处理并发编辑，前端是 TypeScript，后端是 Rust，一个人用、一群人用都行。

它没有账号系统，也不需要登录。打开服务地址就会自动生成一份新文档，地址里的文档号（形如 #abc123）就是这份文档的唯一标识；把这个带文档号的地址发给别人，对方打开就进了同一份文档。侧边栏会列出当前在线的人（随机生成的动物昵称），谁在改一目了然。

编辑器支持按语言做语法高亮：侧边栏的 Language 下拉里选 javascript、python、dockerfile，代码就会按对应语言着色。文档没有「保存」按钮，改动自动同步；要长期保留，需要在编排里配一个 SQLite 文件（下面会讲）。

项目地址：[点我跳转](https://github.com/ekzhang/rustpad)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先部署 Rustpad，再用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，把同一个文档地址发给朋友。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 rustpad，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  rustpad:
    image: ekzhang/rustpad:latest
    restart: unless-stopped
    ports:
      - "3030:3030"
    environment:
      EXPIRY_DAYS: "30"
      SQLITE_URI: "sqlite:///data/rustpad.db?mode=rwc"
    volumes:
      - rustpad-data:/data

volumes:
  rustpad-data:
```

整段编排里只有三类信息需要留意：

**端口 3030**：容器里监听 3030，冒号左边是宿主机端口。3030 被占用的话换一个数字，下面建隧道时本地端口跟着改。

**EXPIRY\_DAYS**：文档在内存里保留多少天（默认 1 天，这里设成 30）。超过这个时间没人打开，内存里的文档会被回收。

**SQLITE\_URI 和数据卷**：这是长期保存的关键。不配它，文档只活在内存里，容器一重启就没了；配上它并把 /data 挂成卷，服务会定时把文档快照写到 SQLite 文件里，重启后照着文件恢复。这条路径必须指向挂载出来的目录，否则快照会跟着容器一起消失。

勾上「创建项目后立即启动」，镜像拉完就起来了。首次启动几秒就好，浏览器打开宿主机地址加 3030 就能用。

### 二、用帕斯内网穿透拿公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3030（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 三、第一次打开：建文档、发地址

用公网地址打开，服务会立刻生成一份空文档，地址栏最后多出一段文档号。侧边栏的 Share Link 就是「当前地址 + 文档号」，点 Copy 复制，发给要一起写的人。

**注意：** 只发首页地址（不带文档号）的话，每个人打开都会得到一份新文档，互相看不见。一定要发 Share Link 里那条完整地址。

对方用浏览器打开就能直接编辑，不用注册、不用登录。侧边栏 Active Users 里会并排列出两个人，一边打字，另一边几乎同时出现。

### 四、一起写：实时同步怎么用

**分工写。** 两个人各占文档一段，谁改哪段就在哪段敲，改动逐字同步，不用来回复制粘贴。

**语法高亮。** 侧边栏 Language 下拉选对应的语言，代码按关键字、字符串、注释着色，看起来比纯文本清楚得多。

**看清谁在。** Active Users 里列着当前在线的人，用随机动物名区分；人走光了列表就空，不会留下登录状态。

**长期保存。** 按上面的编排配好 SQLITE\_URI 和卷，重启容器后重新打开同一个文档地址，内容还在（实测）。EXPIRE 时间按 EXPIRY\_DAYS 算，别把它当永久网盘。

### 五、把地址发给朋友

手机打开同一个公网地址和文档号，也能直接编辑，界面会自动收起侧边栏，只留编辑区。在电脑上改到一半，手机接着改，两边看到的是同一份内容。

要停掉分享，在插件里把隧道关掉就行，公网入口立刻断开；内网里照样能用。

### 几点说明

**数据在哪。** 配了 SQLITE\_URI 之后，文档快照在命名卷 rustpad-data 里的 SQLite 文件里。备份这个卷就够；想搬到别的机器，把卷导出来带走。没配的话没有持久化，重启即丢。

**升级。** 镜像标签是 latest，重新拉取镜像、重启项目即可。升级前先备份数据卷。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Rustpad 本身不需要 HTTPS 才能用；但文档内容在公网可读可改，地址别到处发，用完把隧道关掉。

**没有导出。** 这个项目不提供导出按钮，重要内容自己在编辑器里全选复制走。

---

原文：<https://www.passnat.com/posts/rustpad-nas-docker-collaborative-code-editor-intranet-penetration>


## 相关文章

- [Isso 评论区 NAS Docker 部署 + 内网穿透](isso-nas-docker-self-hosted-comments-intranet-penetration.md)
- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
- [自建开源协作编辑平台Etherpad，NAS极简部署，数据完全掌控，内网穿透远程访问](etherpad-nas-deployment-guide.md)
- [开源个人数据管理框架Eidos本地部署指南，SQLite私密数据库离线使用，NAS内网穿透远程访问完整教程](eidos-open-source-personal-data-management-local-deployment-guide-sqlite-private-database-offline-usage-nas-intranet-penetration-remote-access-complete-tutorial.md)
