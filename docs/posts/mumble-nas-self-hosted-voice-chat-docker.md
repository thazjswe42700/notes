---
title: "Mumble 自建语音：NAS Docker 部署 + 内网穿透"
date: 2026-10-17
description: "开源的语音聊天，频道在大厅下面自己开，语音走自己家；附 64738 怎么放出去、客户端地址和进服密码怎么填。"
canonical: https://www.passnat.com/posts/mumble-nas-self-hosted-voice-chat-docker
---

## 项目介绍

Mumble 是一个开源的语音聊天服务端加客户端。频道像一棵树，你在大厅下面自己开：一队、二队、挂机房，谁想换队自己点过去；有人那边太吵，右键静音；自己不方便说话，点一下麦克风闭麦。

它比分头开语音群省心的地方在于：服务端只有一个容器，**语音数据走你自己家**，不经过第三方的服务器；客户端在 Windows、macOS、Linux 和手机上都有，朋友不用装什么特别的东西。

项目地址：[点我跳转](https://github.com/mumble-voip/mumble)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 把端口放出去，再用 Docker Compose 把服务端装上，最后在客户端里填地址。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。Mumble 只要一条：

名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **64738**，协议选 TCP。

点开这条隧道、按一下电源，状态变成运行中，详情里就是公网地址（形如 IP:端口），记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 mumble，路径在「我的文件」下面挑一个空文件夹。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  mumble:
    image: mumblevoip/mumble-server:latest
    container_name: mumble
    restart: unless-stopped
    ports:
      - "64738:64738"
      - "64738:64738/udp"
    environment:
      MUMBLE_SUPERUSER_PASSWORD: "换成你的管理员口令"
      MUMBLE_CONFIG_SERVERPASSWORD: "换成你的进服密码"
      MUMBLE_CONFIG_WELCOMETEXT: "欢迎回来，频道自己开"
      MUMBLE_CONFIG_USERS: "20"
    volumes:
      - ./data:/data
```

两个端口都放出来：**64738/tcp** 是聊天和连接用的，**64738/udp** 是语音用的，主机和容器两边都写 64738。只放 TCP 也能聊，但语音会回落到 TCP，延迟会高一点。

**MUMBLE\_SUPERUSER\_PASSWORD** 是管理员口令（SuperUser 用来开频道、设权限），**MUMBLE\_CONFIG\_SERVERPASSWORD** 是进服密码，谁连都要输。这两个自己换掉，别用文章里的。另外两个是欢迎语和人数上限。

**./data** 是数据库和证书目录，挂出来，重新部署不丢频道和权限；重装之前先备份它。

勾上「创建项目后立即启动」，点确认，镜像下完就起来了。

### 三、客户端连上去

客户端打开，点「添加服务器」：地址填第一步拿到的公网地址，端口 64738，用户名随便起一个，确认。

第一次连会弹一个证书提示，说验证失败 —— 因为证书是服务端自己生成的，点「是」接受，之后不再问。然后输一次进服密码，就进去了。

在家里用局域网地址连（就是 NAS 的 IP），出门用公网地址，两个都存成收藏夹，切换只要点一下。

### 四、开频道、管人

进去以后左边是频道树，根频道下面可以自己开子频道：一队、二队、挂机房。谁想换队，右键自己 → 移动，或者拖过去。

有人那边太吵，右键他 → 静音；自己不方便说话，点工具栏的麦克风闭麦。要长期管人，用 SuperUser 登进去设权限、开注册用户。

### 几点说明

**只建了 TCP 隧道的时候，语音走 TCP。** Mumble 会自动把语音也放进那条 TCP 连接里，能正常聊，延迟比局域网高一点；两端网络都好的时候它会自己走 UDP，用不着你管。想彻底走 UDP，得让 64738/udp 也能被外面访问到。

**证书自签是正常的。** 服务端第一次启动会生成自己的证书，客户端第一次连会问一次。别把 data 目录删了，删了证书和频道一起没。

**人数上限。** 编排里 MUMBLE\_CONFIG\_USERS 是 20，够一个群用；开大之前先看 NAS 的带宽和内存。

**地址变了。** 隧道删了重建、地址变了，客户端的收藏夹里那条也要改，不然连不上。

---

原文：<https://www.passnat.com/posts/mumble-nas-self-hosted-voice-chat-docker>
