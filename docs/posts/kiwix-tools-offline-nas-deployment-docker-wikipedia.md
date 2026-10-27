---
title: "Kiwix Tools离线知识库NAS部署指南，Docker快速搭建私人维基百科服务器"
date: 2025-10-16
description: "Kiwix Tools是开源离线内容访问工具集,支持在无网络环境下访问维基百科、古腾堡计划等海量知识资源。包含ZIM文件管理、全文搜索和HTTP服务功能,完全开源基于GPL v3许可,支持Docker快速部署,适合网络受限地区、离线学习和私人知识库服务器建设场景。"
canonical: https://www.passnat.com/posts/kiwix-tools-offline-nas-deployment-docker-wikipedia
---

## 项目介绍

Kiwix 是一个开源的离线知识库服务器。维基百科官方把整座百科打包成 ZIM 离线文件，Kiwix 负责把它变成一个网页：装到家里的 NAS 上之后，查词条不需要联外网，家里断网照样查，内容整份都在自己手里。

它的资源占用很低，一个容器就能跑，手机、电脑、平板打开浏览器就能用。除了维基百科，古腾堡计划、TED 演讲等许多站点也都有现成的 ZIM 离线包，一个文件就是一座图书馆。

项目地址：[点我跳转](https://github.com/kiwix/kiwix-tools)

## 部署安装

如果你希望出门在外也能查自己的离线知识库，建议配置内网穿透服务。可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，飞牛的应用中心里就有官方插件，下面会一起配置。

### 第一步：下载 ZIM 离线包

这一期用两个中文包做演示（下载后放到电脑上待用）：

[计算机主题维基百科（约 237MB，带图）](https://lb.download.kiwix.org/zim/wikipedia/wikipedia_zh_computer_maxi_2026-09.zim)

[维基百科精选版（约 264MB）](https://lb.download.kiwix.org/zim/wikipedia/wikipedia_zh_top_mini_2026-09.zim)

想要更全的包，可以去官方库 [library.kiwix.org](https://library.kiwix.org) 挑选：完整的中文维基百科也有，体积更大，NAS 空间够就装得下，方法一模一样。

### 第二步：把离线包传进 NAS

打开飞牛的文件管理，在「我的文件」下新建两个文件夹：一个 wiki-data，用来放离线包；一个 wiki-app，留空给编排用。把上面两个 ZIM 文件上传到 wiki-data 里，家里局域网一会儿就好。

### 使用 Docker Compose 部署

打开飞牛的 Docker 应用，切到 Compose，点右上角的「新增项目」。项目名称填 kiwix，路径选刚才留空的 wiki-app，来源选「创建 docker-compose.yml」，把下面的编排整个贴进来：

```yaml
services:
  kiwix:
    image: ghcr.io/kiwix/kiwix-serve:latest
    container_name: kiwix
    network_mode: bridge
    user: "0:0"
    ports:
      - "3485:8080"
    volumes:
      - ../wiki-data:/data:ro
    command:
      - /data/wikipedia_zh_computer_maxi_2026-09.zim
      - /data/wikipedia_zh_top_mini_2026-09.zim
    restart: unless-stopped
```

编排里要认三处：3485 是浏览器里访问的端口，可以换成别的没被占用的端口，冒号右边的 8080 别动，是容器里的服务端口；挂载这一行指向旁边的 wiki-data 文件夹，前面那行 user 让容器读得到这几个包，不加它起不来；最下面的 command 把两个包的文件名原样列给它，容器不会自己扫描目录。

勾上「创建后立即启动」，点「创建」，等列表变成「正在运行」就装好了。

### 用 PassNAT 开放到公网

Kiwix 自己没有账号系统，所以开放到公网之前，务必给隧道设一个网页访问口令。打开飞牛应用中心，搜索 PassNAT，装上这个穿透插件。

打开插件，点「创建隧道」：名字随便起，端口填 3485，类型切换成 HTTP，会分到一个好记的域名；在下面把网页访问口令设好。创建之后先别急着通电——回到 Docker 确认容器正在运行，再回插件按一下最左边的电源键，等状态变成「运行中」，面板上面的网址就是出门用的公网地址。

### 出门也能查

在外面的浏览器打开公网地址，第一次会先问一次口令，输完就进到欢迎页，两座图书馆都在这里。在搜索框里输入关键词回车，相关词条一整页全出来，点开就是带图的正文——这些内容都是从你自己的 NAS 里读出来的，不走外网。

本篇教程到此结束，祝你玩得开心！

---

原文：<https://www.passnat.com/posts/kiwix-tools-offline-nas-deployment-docker-wikipedia>
