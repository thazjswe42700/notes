---
title: "Scribble.rs 自建你画我猜：NAS Docker 部署 + 朋友点链接进房"
date: 2026-10-18
description: "单容器、免登录的开源你画我猜：Compose 怎么起、私人房间链接怎么发、中文词库怎么换、在外面怎么用。"
canonical: https://www.passnat.com/posts/scribble-rs-nas-docker-draw-and-guess-game
---

## 项目介绍

Scribble.rs 是一个开源的「你画我猜」：一个人抽到词，在网页画布上画，其他人在右边输入框里猜，谁先猜中谁得分。整个程序是一个 Go 写的小服务，没有数据库、也不用注册登录，起一个容器就能玩。

它把「一局游戏」放在一个房间里：房主选好画图时间、轮数、人数，点创建，就得到一个房间链接；把这个链接发给朋友，他们各自填个昵称、点准备，人齐了自动开局。

项目地址：[点我跳转](https://github.com/scribble-rs/scribble.rs)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。单容器，一份 Compose 编排就够了。

### 一、想在外面玩，先把 8080 放出去

只在局域网里玩，这一步可以跳过，直接看第二步。要让在外面的人也能进来，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **8080**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 scribble，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  scribble:
    image: biosmarcel/scribble.rs:latest
    container_name: scribble
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      PORT: "8080"
```

容器里固定监听 8080，冒号前面那个数字是 NAS 上的端口，被别的项目占了就改它，后面的不用动。

勾上「创建项目后立即启动」，点确认。镜像很小，几秒钟就下完，容器状态变成「运行中」就可以打开网页了。

### 三、第一次打开

浏览器打开 **http://NAS的局域网地址:8080**，看到的就是创建房间的页面。上面一块是 Create Lobby：语言、计分方式、画图时间、轮数、人数；下面一块是 Join Lobby，列出当前别人开着的公开房间。

想和朋友单独玩，点 **Create Private Lobby**，会直接进房间；房间链接就是地址栏里 **/lobby/** 后面那一串，复制下来，谁拿到这个链接谁就能进来。

### 四、一局是怎么走的

每个人进来先在中间的昵称框填名字、点 Apply，然后勾上 Ready。人齐了自动开局，第一个画的人从候选词里挑一个，挑完中间那块就变成画布。

画的人左边有调色板和笔刷粗细，右上角是倒计时 120 秒；其他人看到的是同一块画布，但顶部词是横线遮住的，在右边的输入框里打字猜，猜错会出现在聊天记录里，猜中会变绿、并且拿到分数。

一轮画完换下一个人画，默认画 4 轮，全部走完显示总分。

### 五、把词库换成中文

创建房间的时候把 Language 选成 **custom**，下面会多出一个 Custom Words 输入框，把词用逗号隔开填进去就行，比如：

```
太阳, 小猫, 雨伞, 香蕉, 自行车
```

再上面那格 Custom Words Per Turn 决定每局给画手几个候选词，填 1 就只有一个。词库存在内存里，不用改文件也不用重启。

### 六、把房间链接发出去

房间建好之后，把地址栏那一串（**/lobby/** 加一串编号）发到群里。朋友用手机或电脑打开，填个昵称，点 Ready 就进来了，不用装 App、也不用注册账号。

在外面用手机流量打开，就是走第一步建的那条隧道 —— 手机浏览器同样能画画、能猜词，横屏玩起来最舒服。

### 几点说明

**房间是内存里的。** 服务重启（比如 NAS 重启、容器更新）之后，房间就没了，大家重新开一个就行，本来也没有要保存的数据。

**公开房间谁都能看见。** 首页的 Join Lobby 会列出公开房间。只想自己人玩，就用 Create Private Lobby，房间不进列表，只有拿到链接的人进得来。

**想再加一道门槛。** 应用本身没有密码，挂到公网时可以在隧道那一层打开网页访问口令 —— 朋友打开链接先输一次口令，再进游戏。

**WebSocket 要通。** 这个应用靠 WebSocket 实时同步画布，用隧道或者自己反向代理的时候，注意把连接升级用的请求头放过去。

**端口冲突。** 宿主 8080 很热门，被占了就改编排里冒号前面那个数字。

**更新。** 拉新镜像重建容器即可；没有数据库和卷，不存在迁移。

---

原文：<https://www.passnat.com/posts/scribble-rs-nas-docker-draw-and-guess-game>


## 相关文章

- [DocuSeal 自建电子签署：NAS Docker 部署 + 签署链接怎么发](docuseal-nas-docker-e-signature-contract.md)
- [Lute 自建外语阅读工具：NAS Docker 部署 + 生词状态怎么用](lute-nas-docker-foreign-language-reading-vocabulary.md)
- [OpenGist 自建代码片段库：NAS Docker 部署 + 修订差异怎么看](opengist-nas-docker-snippet-share-revision-history.md)
- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [Koillection 自建收藏品目录：NAS Docker 部署 + 照片标签 + 借出记录](koillection-nas-docker-collection-catalog-loans.md)
