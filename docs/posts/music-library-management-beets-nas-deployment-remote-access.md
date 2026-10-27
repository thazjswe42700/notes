---
title: "自建音乐库管理系统Beets，NAS极简部署一键启动，智能整理音乐收藏无公网远程访问"
date: 2025-10-16
description: "Beets是专业的音乐库管理系统，自动编目整理音乐文件并智能改进元数据。支持MusicBrainz、Discogs等多个数据库，集成强大插件系统。提供Web界面浏览播放、转码、去重、标签清理等功能，支持MPD协议与多种播放器集成。Docker快速部署，轻松实现音乐收藏一站式管理。"
canonical: https://www.passnat.com/posts/music-library-management-beets-nas-deployment-remote-access
---

## 项目介绍

Beets 是一款专为音乐发烧友打造的媒体库管理系统。它的核心目标是帮助用户一劳永逸地整理好自己的音乐收藏。Beets 能够自动对你的音乐文件进行编目，并在导入过程中智能地改进元数据信息，让你的音乐库始终保持井井有条。

这个项目集成了强大的插件系统，支持从多个音乐数据库（如 MusicBrainz、Discogs、Beatport）自动获取专辑封面、歌词、流派、节奏等丰富的元数据。无论是转码音频、检测重复曲目、清理标签错误，还是通过网页界面浏览和播放音乐，Beets 都能轻松胜任。它甚至支持 MPD 协议，可以与多种音乐播放器集成。

[点我跳转](https://github.com/beetbox/beets)

项目地址：[点我跳转](https://github.com/beetbox/beets)

## 部署安装

若想在外网也能访问你的音乐库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的跨平台配置指南可以 [点我查看](https://www.passnat.com/posts/p)，轻松几步即可实现远程访问。

**使用 Docker 快速部署**

打开飞牛 NAS 或其他 NAS 设备的管理界面

点击本地镜像 - 添加镜像 - 从 URL 添加，在镜像地址栏填入：

```
beetbox/beets:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

勾选开机自动启动选项，然后进入端口配置界面。默认情况下，Beets 的 Web 界面运行在容器内的 **8337** 端口，你可以将其映射到 NAS 上的任意可用端口（例如 8337）。如果遇到端口冲突，可自行修改宿主机端口，但需保持映射关系正确。

存储配置方面，建议在 NAS 上创建一个专用文件夹用于存放音乐库数据和配置文件。将容器内的 **/music** 目录映射到这个文件夹，这样即使容器重启，你的音乐收藏和配置也不会丢失。

环境变量设置（可选）：

BEETSDIR：指定 Beets 配置文件目录（默认：/root/.config/beets）

MUSICDIR：指定音乐库目录（默认：/music）

这些变量通常无需修改，保持默认即可。直接点击下一步启动容器。

创建成功后，在浏览器中访问 **http://你的NAS\_IP:8337**，即可进入 Beets 的 Web 管理界面，开始整理你的音乐收藏。

到这里部署就全部完成了。现在你可以尽情享受 Beets 强大的音乐管理功能，让你的音乐库变得井井有条。

本篇教程结束

---

原文：<https://www.passnat.com/posts/music-library-management-beets-nas-deployment-remote-access>


## 相关文章

- [聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载](musicn-nas-online-music-player-aggregator.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [家庭私人照片管理系统PhotoStructure，NAS一键部署，内网穿透远程访问你的回忆](photostructure-nas-private-photo-management-remote-access.md)
- [小爱音箱无限音乐播放器XiaoMusic，Docker极简部署内网穿透远程访问](xiaomi-xiaomusic-docker-lan-remote-access.md)
