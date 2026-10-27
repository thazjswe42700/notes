---
title: "RustDesk 自建中继：NAS Docker 部署 + 内网穿透"
date: 2026-10-17
description: "开源的远程桌面，自己搭 ID 服务器和中继，连接不经过第三方；附两个端口怎么放出去、客户端三个格子怎么填。"
canonical: https://www.passnat.com/posts/rustdesk-nas-self-hosted-relay-remote-desktop-docker
---

## 项目介绍

RustDesk 是一个开源的远程桌面工具。控制端和被控端各装一个客户端，输入对方的 ID 就能连上看桌面、传文件、共用剪贴板，用起来跟常见的远控软件差不多；不一样的是，中间的「见面」和「转发」可以架在你自己的 NAS 上。

它把服务端拆成两个小容器：**hbbs** 是 ID 服务器，负责让两台机器互相找到、握手；**hbbr** 是中继，两边网络打不通的时候，画面和数据从它这里转。两个都架在自己 NAS 上，连接就不经过第三方的服务器。

项目地址：[点我跳转](https://github.com/rustdesk/rustdesk)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 把两个端口放出去，再用 Docker Compose 把服务端装上，最后在客户端里填三个格子。

### 一、先用帕斯内网穿透拿到两个公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。RustDesk 要建**两条**：

第一条给 ID 服务器：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **21116**。

第二条给中继：名字另起一个，本地端口填 **21117**。

两条都点开、按一下电源，状态变成运行中，详情里各有一个公网地址（形如 IP:端口），两个都记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 rustdesk，路径在「我的文件」下面挑一个空文件夹。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  hbbs:
    image: rustdesk/rustdesk-server:latest
    container_name: hbbs
    command: hbbs -r 你的中继公网地址:公网端口
    network_mode: bridge
    ports:
      - "21115:21115"
      - "21116:21116"
    volumes:
      - ./data:/root
    restart: unless-stopped

  hbbr:
    image: rustdesk/rustdesk-server:latest
    container_name: hbbr
    command: hbbr
    network_mode: bridge
    ports:
      - "21117:21117"
    volumes:
      - ./data:/root
    restart: unless-stopped
```

**hbbs 那条命令**：-r 后面填第一步拿到的**中继那条**的公网地址，端口一起写上（形如 1.2.3.4:51234）。这一格不填，客户端连上 ID 服务器也找不到中继，画面就出不来。

端口映射两行照抄：21116 给 ID 服务器、21117 给中继，跟第一步建的两条隧道一一对应。**../data** 是存密钥的文件夹，挂出来，下面拿公钥要用；重新部署之前先备份它，不然密钥会变、客户端全部要重填。

勾上「创建项目后立即启动」，点确认，两个镜像下完就起来了。

### 三、拿公钥

第一次启动会自动生成一对密钥，放在上面挂出来的 data 目录里。打开 **id\_ed25519.pub**，里面那一长串就是公钥，复制下来。客户端要拿它跟服务端对暗号，不填会被拒绝。

### 四、客户端填三个格子

两台电脑都装上 RustDesk 客户端，打开「设置」，切到「网络」，填三样：

**ID 服务器**：ID 服务器那条隧道的公网地址；**中继服务器**：中继那条隧道的公网地址；**Key**：刚才复制的那串公钥。两台填的必须一模一样。

填完确定，回到主界面。要连哪台，就在另一台客户端里输入它的九位 ID，点连接，第一次输一次密码（密码在主界面左边写着，可以点「改」换成自己的）。

### 五、连上之后

画面出来之后，顶部有「传输文件」「剪贴板」几个按钮：文件可以直接拖进去，剪贴板两边是通的，复制一段文字，那边就能粘。走中继延迟会高一点，日常用够。

### 几点说明

**只放了 TCP。** 编排里 21116 只映射了 TCP，没有放 UDP —— 两台机器打不成点对点，连接会走中继，能用但延迟高一点。想直连的话，中继那条之外再给 21116 加一条 UDP 隧道。

**21115 / 21118 没放。** 这两个端口是网页版客户端和判断 NAT 类型用的，只用桌面客户端可以先不开。

**地址变了。** 隧道删了重建、地址变了，要把编排里 hbbs 的 -r 和两台客户端的两个地址一起改掉，不然连不上。

**更新。** 升级之前把 data 目录备份一份；密钥换了，所有客户端都要重新填公钥。

---

原文：<https://www.passnat.com/posts/rustdesk-nas-self-hosted-relay-remote-desktop-docker>


## 相关文章

- [自建RustDesk远程桌面服务器,NAS极简部署无公网穿透远程控制,开源跨平台屏幕共享工具](rustdesk-nas-remote-desktop-server-self-hosted.md)
- [高性能Rust端口扫描工具RustScan快速部署指南，3秒全端口扫描内网穿透远程访问](rust-scan-high-performance-port-scanner-quick-deployment-guide.md)
- [Mumble 自建语音：NAS Docker 部署 + 内网穿透](mumble-nas-self-hosted-voice-chat-docker.md)
- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [Guacamole 自建桌面网关：NAS Docker 部署 + 浏览器远控](guacamole-nas-docker-browser-remote-desktop-gateway.md)
