---
title: "自建音乐流媒体服务器Polaris，免费开源支持多格式，NAS一键部署内网穿透远程听歌"
date: 2025-10-16
description: "Polaris是免费开源的自建音乐流媒体服务器，支持多种音频格式和10万+歌曲库。提供美观深色主题、强大搜索、用户独立账户和播放列表功能，可在Android、iOS等设备随时享受音乐。配置灵活，界面简洁优雅，适合音乐发烧友和普通听众。"
canonical: https://www.passnat.com/posts/polaris-open-source-music-server-nas-remote-streaming
---

## 项目介绍

Polaris 是一个自建的音乐流媒体服务器，让你可以在任何电脑或移动设备上享受自己的音乐收藏。这是一个完全免费且开源的项目，没有任何付费版本。

项目致力于提供卓越的性能和响应速度，特别是对于拥有超过10万首歌曲的大型音乐库也能轻松应对。Polaris 支持 FLAC、MP3、MP4、MPC、OGG、OPUS、APE、WAV 和 AIFF 等多种音频格式，拥有美观的深色主题和可自定义的调色板。

你可以按照专辑、艺术家或流派浏览音乐，也支持文件树形浏览。项目内置了强大的搜索功能，支持按字段精确查询。每个用户都有独立的账户和播放列表，可以在 Android、iOS 等移动设备上随时随地享受音乐。

界面简洁优雅，配置灵活，无论你是音乐发烧友还是普通听众，都能找到满意的体验。

项目地址：[点我跳转](https://github.com/agersant/polaris)

## 部署安装

如果你想在外网也能访问自己的音乐库，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现，先注册好账号备用。具体的操作步骤可以参考 [这份上手攻略](https://www.passnat.com/posts/p)，详细讲解了全平台的配置方法。

### Docker 快速部署

打开你的 NAS 设备或其他支持 Docker 的主机

点击本地镜像 - 添加镜像 - 从 URL 添加，在镜像地址栏填入：

```
oskarasbrink/polaris:latest
```

等待镜像下载完成后点击启动按钮启动容器。

勾选开机自动启动选项，然后进入端口设置。

**端口映射设置：**

容器内端口：`5050`（这是 Polaris 的默认服务端口，不要修改）

宿主机端口：`5050`（如果你的 NAS 上 5050 端口已被占用，可以改成其他端口如 5051、5052 等，但要记住你设置的端口号）

**存储位置配置：**

/music - 用于存放你的音乐文件（必须）

/config - 用于保存 Polaris 的配置文件（可选但推荐）

在容器的路径映射中：

将你 NAS 上的音乐文件夹映射到容器内的 /music

将配置文件夹映射到容器内的 /data

**环境变量设置：**

Polaris 的环境变量配置相对简单，一般情况下不需要额外设置。如果你有特殊需求，可以参考官方文档进行调整。

完成以上配置后，点击下一步启动容器。

创建成功后，打开浏览器访问 `http://你的NAS地址:5050`（如果你修改了端口号，请使用你设置的端口），就可以看到 Polaris 的登录界面了。

首次使用时，你需要在 Polaris 的设置中指定你的音乐库路径，然后它会自动扫描并索引你的所有音乐文件。根据音乐数量的多少，这个过程可能需要几分钟到几小时不等。

到这里，Polaris 音乐服务器就部署完成了！现在你可以尽情享受自己的音乐收藏了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/polaris-open-source-music-server-nas-remote-streaming>


## 相关文章

- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [自架推送服务平台PushDeer，NAS极简部署，支持内网穿透远程推送，多设备同步私密消息](self-hosted-push-notification-service-pushdeer-nas-simple-deployment-intranet-penetration-remote-push-multi-device-sync-private-messages.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [自建Bark推送服务器,iOS消息通知极简部署,内网穿透远程接收,隐私安全有保障](bark-server-ios-notification-self-hosted-internal-network-penetration-privacy-secure.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
