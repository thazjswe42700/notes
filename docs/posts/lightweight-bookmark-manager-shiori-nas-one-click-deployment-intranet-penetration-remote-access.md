---
title: "轻量级书签管理工具Shiori，NAS一键部署，支持内网穿透远程访问书签"
date: 2025-10-16
description: "用 NAS 上的 Docker Compose 部署开源书签管理器 Shiori：保存网页标题、摘要和标签，还能生成页面快照，原网站下线也能读。中文标签筛选、关键词搜索，配帕斯内网穿透出门也能翻自己的书签库。"
canonical: https://www.passnat.com/posts/lightweight-bookmark-manager-shiori-nas-one-click-deployment-intranet-penetration-remote-access
---

## 项目介绍

Shiori 是一个开源的书签管理器，Go 写的，单个容器就能跑。它记的不只是网址：每个书签的标题、摘要、标签都会一起存下来，还可以顺手生成一份页面快照，把当时看到的正文和排版一起保留。

这一点和浏览器收藏夹不太一样。收藏夹里只剩一行标题，链接过几个月常常就打不开了；Shiori 的快照存在自己的 NAS 上，原网站后来改版、下线，快照照样能读。回头找资料时，按关键词或者中文标签一筛，散在各处的收藏就集中到眼前。

项目地址：[点我跳转](https://github.com/go-shiori/shiori)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿一个公网地址，再用 Docker Compose 把 Shiori 装上。Shiori 的编排本身不用填公网地址，但出门要用手机翻书签，隧道提前准备好会省事。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 shiori，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  shiori:
    image: ghcr.io/go-shiori/shiori:latest
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      # 数据目录（镜像默认就是 /shiori，这里写清楚，和下面的卷对上）
      SHIORI_DIR: /shiori
      # 固定一个随机串，不设的话每次重启登录态都会失效
      SHIORI_HTTP_SECRET_KEY: "换成你自己的随机字符串"
      TZ: Asia/Shanghai
    volumes:
      - ./data:/shiori
```

**SHIORI\_DIR**：数据目录，固定写成 /shiori，和下面卷的挂载点对上。

**SHIORI\_HTTP\_SECRET\_KEY**：换成你自己的随机字符串。不设的话每次重启都会换一个随机密钥，登录状态跟着失效。

**volumes**：把容器里的 /shiori 挂到 NAS 上的 ./data 文件夹，书签、标签、快照全在这里，备份把它整个拷走就行。

勾上「创建项目后立即启动」，点确认，镜像拉完就起来了。

### 三、第一次登录

浏览器打开公网地址，用默认账号登录：用户名 **shiori**，密码 **gopher**。

**登录后第一件事是改密码**：左上角齿轮进设置，账户那一行点「Change password」，填上当前密码和新密码保存。改完可以用新密码重新登录一次确认。

### 四、添加第一个书签

回到主页，点右上角的加号。把页面地址粘进去，标题和摘要会自动抓取（抓不到可以自己填），标签写几个中文词，比如「咖啡」「菜谱」，勾上「Create archive」，点 OK。稍等几秒，卡片就出现在墙上了。

点开卡片就是快照，默认显示可读版，正文和排版都在；右上角可以在「View Original」和「View Readable」之间切换。这时候把原网站关掉，快照还是照样打开，这就是它和普通收藏夹的区别。

### 五、怎么找回来

搜索框里输关键词，回车，标题、摘要和网址里能对上的书签都会列出来；中文标签可以直接点卡片上的标签，也可以在搜索框里写 tag:标签名 来筛。常用的资料按主题打几个标签，比翻收藏夹快得多。

**说明一下检索范围**：中文的标签筛选和摘要、网址关键词是可靠的；标题要输入完整标题词才能命中；正文的全文检索对中文分词支持有限，长句里的单个词不一定搜得到。要按正文找，建议在添加时把关键词写进标题或摘要。

### 几点说明

**数据在哪。** 书签、标签、存档都在映射出去的 ./data 目录里（容器内是 /shiori）。换机器时把这个目录拷过去，重新起容器就是原来的库。

**镜像里没有 shell。** Shiori 的镜像很精简，不能用 docker exec 进去 ls；要取数据直接把 ./data 目录拷出来。

**更新。** 重新拉取镜像、重启项目即可；升级前先把 ./data 备份一份。

---

原文：<https://www.passnat.com/posts/lightweight-bookmark-manager-shiori-nas-one-click-deployment-intranet-penetration-remote-access>


## 相关文章

- [自托管书签管理工具Servas无公网部署指南 NAS极简配置内网穿透远程访问](servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access.md)
- [mTab书签管理工具私有部署指南，跨平台同步无广告，内网穿透远程访问](mtab-bookmark-manager-private-deployment-guide-cross-platform-sync-no-ads-intranet-penetration-remote-access.md)
- [自建书签管理器Linkding极简部署指南，NAS内网穿透远程访问书签收藏库](linkding-bookmark-manager-nas-remote-access-guide.md)
- [自托管个人知识库Karakeep，AI智能标签书签管理，NAS极简部署内网穿透远程访问](karakeep-ai-bookmark-manager-nas-deployment.md)
- [自建私人书签管理库 一处部署随处访问，OneNav Docker极简快速部署指南](onenav-docker-bookmark-manager-deployment-guide.md)
