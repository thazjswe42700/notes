---
title: "OpenGist 自建代码片段库：NAS Docker 部署 + 修订差异怎么看"
date: 2026-10-18
description: "单容器、SQLite 的开源代码片段服务：Compose 怎么起、第一条片段怎么发、修订 diff 怎么看、朋友不用注册怎么打开。"
canonical: https://www.passnat.com/posts/opengist-nas-docker-snippet-share-revision-history
---

## 项目介绍

OpenGist 是一个开源的代码片段服务：把一段配置、一个脚本、一截日志贴进去，它给你一个地址；把这个地址发给别人，对方用浏览器就能看，带语法高亮，不用登录。

它和「聊天框里粘代码」的区别在版本：同一条片段每改一次都会留下一次修订，打开「修订」页能看到每次改了哪几行、加了几行删了几行，改坏了随时翻回上一版。

整个程序是一个 Go 写的服务，数据放在一个 SQLite 文件里，起一个容器就能用，没有外部数据库。

项目地址：[点我跳转](https://github.com/thomiceli/opengist)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。单容器，一份 Compose 编排就够了。

### 一、想在外面打开，先把 6157 放出去

只在局域网里用，这一步可以跳过，直接看第二步。要让在外面的人也能打开你发的地址，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **6157**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 opengist，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  opengist:
    image: ghcr.io/thomiceli/opengist:1.15.2
    container_name: opengist
    restart: unless-stopped
    ports:
      - "6157:6157"
    environment:
      UID: 1000
      GID: 1000
      OG_SSH_GIT_ENABLED: disabled
      OG_SECRET_KEY: 换成你自己的随机字符串
    volumes:
      - ./data:/opengist
```

容器里固定监听 6157，冒号前面那个数字是 NAS 上的端口，被别的项目占了就改它，后面的不用动。下面那行 **OG\_SECRET\_KEY** 换成你自己的随机字符串（登录会话和二次验证数据用它加密），换完以后不要随便改，改了已登录的会话会失效。

勾上「创建项目后立即启动」，点确认。镜像只有几十兆，下完容器状态变成「运行中」就可以打开网页了。

### 三、第一次打开：建管理员

浏览器打开 **http://NAS的局域网地址:6157**，看到的是「所有 Gists」页，右上角有「注册」。第一个注册的账号自动就是管理员。

注册完点右上角用户名 → 「管理」→「配置」，把 **Disable signup** 打开：这个实例是你自己的，关掉注册以后别人就不能自己建号了。

想让朋友也能登录着改、不只是看，就在管理面板的「邀请」里生成邀请链接 —— 邀请链接可以限人数、限有效期，也能绕过刚才关掉的注册开关。

### 四、发一条片段

点右上角「新建」：左边是代码编辑器，右边是文件信息。

先给文件起名（带扩展名，比如 **archive-photos.sh**），语法高亮按扩展名走；再填标题、描述，主题用空格隔开，别人可以在 Topics 里翻到。

下面是可见性和过期时间：默认是公开的，任何拿到地址的人都能看；想只给自己看就切成私密。过期时间可以设 1 小时、1 天、7 天，或者不设。临时的配置、一次性的日志，设一个短过期时间很省心。

点「创建公开的 Gist」，页面跳到这条片段自己 —— 地址是 **你的地址/用户名/一串编号**。右上角「代码」下拉里还有嵌入代码、HTTP 克隆地址和下载 ZIP。

### 五、改一版，看差异

点左边的「Edit files」就能改，改完保存，修订数从 1 变成 2。

点「修订」，这一条片段的所有版本按时间倒序列出来，每次提交都带一块红绿 diff：绿色是加的行，红色是删的行，右上角还写着这一版改了几个文件、加了几行、删了几行。

点某个版本的提交号，就回到那一刻的完整内容。改坏了不用慌，照着旧版本抄回去就行。

### 六、把地址发给朋友

公开的片段，朋友不用注册、不用登录，直接用浏览器打开地址就能看，手机上也是同样的页面，代码可以复制、可以下载。

如果第一步建了隧道，这个地址在外面用手机流量也打得开 —— 发给朋友的就是那个公网地址，他们不需要知道你家 NAS 在哪儿。

### 几点说明

**关掉注册。** 实例建好第一件事就是管理 → 配置里关掉注册，不然谁找到地址谁都能建号。

**私密不等于加密。** 私密的片段只是不在列表里显示，拿到地址的人还是能看；真正的敏感信息（口令、密钥）不要往这里贴。

**过期时间只是不显示了。** 到了过期时间，列表和地址都看不到了，文件本身还在数据目录里。想彻底清掉，就在管理面板里删掉那条片段。

**数据都在 ./data。** SQLite 库和每条片段背后的 Git 仓库都在这个目录里。备份就备份这个目录；删掉它等于清空实例。

**端口冲突。** 宿主 6157 被占了，就改编排里冒号前面那个数字，容器里的 6157 不用动。

**更新。** 拉新镜像重建容器即可，数据目录不动。升级前先备份 ./data。

---

原文：<https://www.passnat.com/posts/opengist-nas-docker-snippet-share-revision-history>


## 相关文章

- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [Rustpad 协同编辑器：NAS Docker 部署 + 内网穿透](rustpad-nas-docker-collaborative-code-editor-intranet-penetration.md)
- [Isso 评论区 NAS Docker 部署 + 内网穿透](isso-nas-docker-self-hosted-comments-intranet-penetration.md)
- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
- [Donetick 自建家务清单：NAS Docker 部署 + 全家分工 + 积分](donetick-nas-docker-family-chore-list-points.md)
