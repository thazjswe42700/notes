---
title: "自建Bark推送服务器,iOS消息通知极简部署,内网穿透远程接收,隐私安全有保障"
date: 2025-10-16
description: "Bark是免费安全的iOS推送通知工具，支持自建服务器部署、端到端加密、消息分组、自定义图标铃声等高级功能。充分利用APNs服务，高效推送消息无需消耗设备电量。支持系统监控告警、应用提醒、自动化工作流触发，提供Docker一键部署方案，完美保护隐私安全。"
canonical: https://www.passnat.com/posts/bark-server-ios-notification-self-hosted-internal-network-penetration-privacy-secure
---

## 项目介绍

Bark 是一款免费、简洁且安全的推送通知工具。它充分利用 Apple 推送通知服务（APNs），在不消耗设备电量的前提下，为用户提供高效的消息推送体验。

Bark 支持 iOS 通知的多项高级功能，包括消息分组、自定义推送图标、铃声设置、时效性通知、紧急警报等丰富的个性化选项。同时，项目支持自建服务器部署，提供端到端加密推送功能，全方位保护你的隐私和数据安全。无论是系统监控告警、应用通知提醒，还是自动化工作流触发，Bark 都能完美胜任。

项目地址：[点我跳转](https://github.com/Finb/Bark)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务以确保随时随地都能接收推送通知。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号以备后用。详细的操作步骤可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台快速入门指南。

Bark 提供了开箱即用的 Docker 部署方案，以下是最快速的上手方式：

### 使用 Docker 单命令部署

打开你的 NAS 设备或 Docker 管理面板，执行以下命令即可一键启动 Bark 服务：

```bash
docker run -d --name bark -p 8080:8080 finb/bark:latest
```

**参数说明：**

-d 后台运行

--name bark 容器名称

-p 8080:8080 端口映射（宿主机:容器内）

finb/bark:latest 官方镜像

如果你的 8080 端口已被占用，可以修改前面的端口号（如 -p 8888:8080），但容器内的 8080 端口需保持不变。

**启动成功后，访问以下地址即可使用：**

```
http://你的NAS_IP:8080
```

打开应用后，复制测试 URL，即可开始发送推送通知。你可以通过 GET 或 POST 请求向该 URL 发送消息，格式如下：

```
/:key/:body 
/:key/:title/:body 
/:key/:title/:subtitle/:body
```

例如：

```
http://你的NAS_IP:8080/yourkey/Hello/这是一条测试消息
```

**高级功能参数：**

url 点击通知跳转链接

group 消息分组显示

icon 自定义推送图标（iOS 15+）

sound 自定义铃声

call 重复播放声音 30 秒

level 设置通知优先级（timeSensitive/critical/passive）

到这里，Bark 推送服务就已经成功部署并可以正常使用了。你现在可以在任何地方通过 API 调用来发送推送通知到你的 iOS 设备。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/bark-server-ios-notification-self-hosted-internal-network-penetration-privacy-secure>


## 相关文章

- [自建音乐流媒体服务器Polaris，免费开源支持多格式，NAS一键部署内网穿透远程听歌](polaris-open-source-music-server-nas-remote-streaming.md)
- [自架推送服务平台PushDeer，NAS极简部署，支持内网穿透远程推送，多设备同步私密消息](self-hosted-push-notification-service-pushdeer-nas-simple-deployment-intranet-penetration-remote-push-multi-device-sync-private-messages.md)
- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
- [私有部署开源聊天应用VoceChat，NAS轻松搭建安全私密聊天系统，内网穿透远程访问](vocechat-private-deployment-nas-internal-network-penetration.md)
