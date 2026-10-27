---
title: "Moodist 环境音效：NAS Docker 部署 + 内网穿透"
date: 2025-10-16
description: "在 NAS 上用 Docker Compose 部署开源环境音播放器 Moodist：84 种雨声、森林、鸟鸣、咖啡馆声音，多张卡片同时叠放，每种独立调音量，还有睡眠定时器和组合保存；用帕斯内网穿透拿公网地址，手机浏览器打开就能听。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/self-hosted-ambient-sound-platform-moodist-75-curated-sounds-meditation-privacy-first-open-source-free
---

## Moodist — 把雨声、森林和咖啡馆放进 NAS

### 项目介绍

Moodist 是一个开源的环境音播放器：雨、风、鸟鸣、海浪、咖啡馆，一排排声音卡片铺成一面墙，点一下就开，再点一下关，几种声音可以同时叠着放。

现在版本内置 84 种声音，按自然、雨、动物、城市、场所、交通、物品、噪音八类分好，也可以直接搜。每种声音有自己的音量滑杆：雨声推大一点、咖啡馆压低一点，组合出来就是一间自己的屋子。

除了混音，它还有睡眠定时器、倒计时、番茄钟、呼吸练习和几档白噪音；调好的组合可以起个名字存下来，下次点开接着听。声音文件全部由容器本地提供，不依赖外部音频服务。

项目地址：[点我跳转](https://github.com/remvze/moodist)

### 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先在飞牛的「应用中心」装 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Moodist 装起来。先穿透、后部署，装完直接用公网地址打开，手机也能听。

#### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字写 moodist，接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边的端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

#### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 moodist，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  moodist:
    image: ghcr.io/remvze/moodist:latest
    container_name: moodist
    restart: unless-stopped
    ports:
      - "8080:8080"
```

这段编排没有要改的密钥：镜像用上游官方那个，容器端口 8080，冒号左边是宿主机端口，被占用就换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，等镜像拉完、状态变成运行中就好了。

Moodist 是纯前端应用，声音文件都在镜像里，没有数据库和附件卷要备份。保存的组合存在浏览器本地，换浏览器或清缓存会丢，重要的组合记得重新存一次。

#### 三、用起来：混音、音量、定时器

用公网地址打开，就是一整面声音墙。上面八个分类可以直接跳转，也可以搜。点一张卡片就开启，卡片左侧变成黑底，滑杆亮起，拖到合适的位置就是这一种声音的音量。

想叠几种就开几张卡，每个的音量各调各的。顶部固定着暂停、保存组合、清空三个按钮：暂停只停声音不丢组合，清空是一次关掉全部。

点保存按钮，给当前组合起个名字，之后在「Your mixes」里就能一键重放。

睡前点右下角菜单里的 Sleep Timer，填小时和分钟，点 Start，右上角会出现倒计时，时间到了自动停；中途想取消就点 Reset。

#### 四、手机上听

手机浏览器打开同一个公网地址，布局自动变成一列，卡片铺满屏，点按和滑杆都能用。通勤、午休、睡前戴耳机就行，不用装 App。

#### 几点说明

**声音从哪来。** 所有音频文件都在容器镜像里，页面不会去请求外部音频服务；断网的内网环境里也能放。

**组合存在哪。** 保存的组合写在浏览器的本地存储里，不上传服务器；换设备不会自动同步，想带走就重新存一次。

**没有下载功能。** 页面只负责播放，没有导出或下载音频的入口，想离线听请用浏览器的 PWA 安装和缓存能力。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Moodist 不需要 HTTPS 才能用；自己家的地址别到处发，不用了可以在插件里把隧道关掉。

**升级。** 镜像标签用的是 latest，升级时在 Docker 里重新拉取镜像、重启项目即可，组合数据在浏览器里，不受影响。

---

原文：<https://www.passnat.com/posts/self-hosted-ambient-sound-platform-moodist-75-curated-sounds-meditation-privacy-first-open-source-free>


## 相关文章

- [RStudio 数据分析：NAS Docker 部署 + 内网穿透](rstudio-nas-docker-r-data-analysis-intranet-penetration.md)
- [Mattermost 团队聊天：NAS Docker 部署 + 内网穿透](mattermost-team-chat-nas-docker-intranet-penetration.md)
- [Grist 家庭台账：NAS Docker 部署 + 内网穿透](grist-nas-docker-linked-records-family-ledger-intranet-penetration.md)
- [Docmost 共享文档库：NAS Docker 部署 + 内网穿透](docmost-nas-docker-family-shared-wiki-intranet-penetration.md)
- [GodoOS 网页办公桌面：NAS Docker 部署 + 内网穿透](godoos-nas-docker-web-office-desktop-docs-sheets-files-intranet-penetration.md)
