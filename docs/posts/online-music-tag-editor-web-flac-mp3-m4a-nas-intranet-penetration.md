---
title: "在线音乐标签编辑器 Web版 支持FLAC MP3 M4A 远程NAS音乐管理 内网穿透"
date: 2025-10-16
description: "Music Tag Web是功能强大的在线音乐标签编辑器，支持FLAC、MP3、M4A、OGG等主流音频格式。支持批量刮削、音乐指纹识别、智能整理、歌词翻译等功能。完美适配移动端，通过Web界面轻松编辑音乐元数据，适合Navidrome等远程音乐服务用户使用。"
canonical: https://www.passnat.com/posts/online-music-tag-editor-web-flac-mp3-m4a-nas-intranet-penetration
---

## 项目介绍

**Music Tag Web** 是一款功能强大的在线音乐标签编辑器，专为远程音乐管理而生。它支持FLAC、APE、WAV、AIFF、WV、TTA、MP3、M4A、OGG、MPC、OPUS、WMA、DSF、MP4等几乎所有常见音频格式，让你可以随时随地编辑音乐的标题、专辑、艺术家、歌词、封面等元数据信息。

这款工具特别适合使用Navidrome等远程音乐服务的用户。无论你的音乐库存储在NAS、云服务器还是其他远程设备上，都能通过Web界面轻松管理和修改音乐标签，就像使用本地的MusicTag或mp3tag一样便捷。

核心功能亮点：

🎵 支持主流音频格式的元数据查看、编辑和修改

🤖 智能批量刮削，自动识别和补充音乐信息

🔍 音乐指纹识别技术，即使没有元数据也能识别歌曲

📁 智能整理音乐文件，按艺术家、专辑自动分组

🔄 支持繁简体自动转换、文本替换、格式转换

✂️ 整轨音乐切割功能

🎤 多源音乐标签识别和歌词翻译

📊 播放记录统计，数据可视化展示

📱 完美适配移动端，手机也能轻松编辑

项目地址：[点我跳转](https://github.com/xhongc/music-tag-web)

## 部署安装

如果你想在家庭网络外也能访问这个项目，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在外出时也能随时管理你的音乐库。具体的配置步骤可以参考 [帕斯内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，新手也能轻松上手。

### 最快速部署方案（Docker单命令）

打开你的NAS设备或服务器终端，执行以下命令即可一键启动：

```bash
docker run -d -p 8002:8002 \
  -v /path/to/your/music:/app/media:rw \
  -v /path/to/your/config:/app/data \
  --restart=always \
  xhongc/music_tag_web:latest
```

参数说明：

`/path/to/your/music` - 替换为你的音乐文件夹路径

`/path/to/your/config` - 替换为配置文件保存路径（用于持久化存储设置和数据库）

例如，如果你的音乐在 `/mnt/music`，配置存储在 `/mnt/config`，命令应该是：

```bash
docker run -d -p 8002:8002 \
  -v /mnt/music:/app/media:rw \
  -v /mnt/config:/app/data \
  --restart=always \
  xhongc/music_tag_web:latest
```

### 端口说明

容器内端口：8002（不需要修改）

宿主机端口：8002（如果你的服务器上8002端口已被占用，可以改成其他端口，比如 `-p 9000:8002` 表示用9000访问）

### 访问应用

部署完成后，在浏览器中访问：

```
http://你的服务器IP:8002/admin
```

使用默认账号密码登录：

账号：admin

密码：admin

⚠️ 首次登录后请立即修改默认密码以保护你的音乐库安全！

## 部署完成

到这里你就可以开始使用Music Tag Web了。无论你身在何处，都能通过这个强大的Web应用轻松管理和编辑你的整个音乐库。配合内网穿透服务，即使在外出时也能随时随地对音乐标签进行修改和整理。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/online-music-tag-editor-web-flac-mp3-m4a-nas-intranet-penetration>


## 相关文章

- [自建私人音乐库随时随地听，Go轻量级音乐服务器Euterpe，NAS一键部署内网穿透](go-lightweight-music-server-euterpe-nas-one-click-deployment-intranet-penetration.md)
- [Alger Music Player NAS部署指南，本地音乐服务器搭建，网易云账号同步下载高品质音乐](alger-music-player-nas-deployment-guide-local-music-server-netease-cloud-sync-hq-audio.md)
- [个人音乐流媒体服务器Airsonic-Advanced部署教程，NAS内网穿透远程访问音乐库](airsonic-advanced-nas-deployment-guide-remote-music-streaming.md)
- [聚合三大平台海量音乐，NAS 极简部署 Musicn 音乐库，一键启动在线播放下载](musicn-nas-online-music-player-aggregator.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
