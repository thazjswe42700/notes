---
title: "code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码"
date: 2026-09-07
description: "code-server 把 VS Code 搬进浏览器，装在飞牛 NAS 上，公司电脑、iPad 打开浏览器就是同一套开发环境。本文用 Docker Compose 部署官方镜像 codercom/code-server，再用内网穿透开放到公网，在外面也能改代码、跑终端。"
canonical: https://www.passnat.com/posts/codeserver
---

## 项目介绍

code-server 是 coder 团队开源的项目，把 VS Code 完整地跑在服务器上，你在任何设备的浏览器里打开一个地址，看到的就是熟悉的 VS Code：文件树、编辑器、集成终端、扩展市场，跟桌面版一样。

把它装在飞牛 NAS 上，代码就只有一份、放在 NAS 里。公司电脑、家里台式机、iPad，开个浏览器就是同一套环境，不用来回同步文件，也不用在每台机器上装一遍工具链。

项目地址：[点我跳转](https://github.com/coder/code-server)

## 部署安装

code-server 装在内网之后，出了门就连不上了。想在外面也能用，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 code-server；路径选一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  code-server:
    image: codercom/code-server:4.135.0
    environment:
      - PASSWORD=换成你自己的强口令
    ports:
      - "8443:8080"
    volumes:
      - ./project:/home/coder/project
    restart: always
```

从上往下要认三处：image 用的是 coder 官方镜像 codercom/code-server，带版本号 4.135.0，不用 latest，这样以后重建也是同一个版本；environment 里的 PASSWORD 是登录口令，一定要换成你自己的强口令——这个服务后面要开放到公网，口令就是唯一的门；ports 左边的 8443 是你浏览器要敲的端口，右边的 8080 是容器内部端口，别改。volumes 把容器里的 /home/coder/project 挂到项目目录下的 project 文件夹，你的代码都放这里，备份的时候拷这个文件夹就行。

勾上「创建项目后立即启动」，点确认。镜像压缩后三百多兆，拉起来要等一会儿，列表里看到「运行中」就好了。

### 二、用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面的操作都在公网地址上做。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 codeserver），接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 8443。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。

### 三、在公网地址上打开，改代码、跑终端

浏览器打开刚才那个公网地址，输入编排里设的口令，进来就是完整的 VS Code。左边文件树里是 project 目录，点开一个文件改一行，Ctrl+S 保存，改的直接就是 NAS 上的文件。

底下 Ctrl+` 拉出终端，就是容器里的 shell。官方镜像是干净的 Debian，没有预装 Python 或 Node，需要什么在终端里用 apt 装一下就行（容器里的 coder 用户可以免密 sudo）：

```bash
sudo apt update && sudo apt install -y python3
python3 hello.py
```

装好之后 python3 直接跑，跟本地一模一样。注意这样装的东西在容器里，重建容器要再装一次；代码在 project 目录里，不受影响。

## 几个提醒

一、公网上开着的是一个带终端的完整开发环境，口令泄漏等于别人拿到容器的 shell。PASSWORD 一定用长的随机口令，别用 123456 这种。

二、想换中文界面，在扩展市场搜 Chinese (Simplified) 装上，然后 F1 输入 Configure Display Language 选中文，重启一下就是中文。

三、如果以后重建隧道、公网端口变了，浏览器里换成新地址就行，code-server 本身不用改配置。

---

原文：<https://www.passnat.com/posts/codeserver>


## 相关文章

- [自托管编程统计平台Wakapi快速部署指南，Docker Compose一键启动自己的数据私密服务](wakapi-self-hosted-deployment-guide-docker-compose.md)
- [自建私密文件转换服务器Morphos，支持图片文档电子书格式互转，Docker一键部署NAS](private-file-conversion-server-morphos-image-document-ebook-docker-nas.md)
- [私有社区服务器Circled.me极简部署指南，NAS一键启动无需公网穿透](circled-me-private-community-server-simple-deployment-guide-nas-one-click-startup-no-public-network-penetration.md)
- [跨平台音乐服务器Lyrion Music Server部署指南，NAS音乐流媒体服务器快速安装教程](lyrion-music-server-nas-deployment-guide.md)
- [自建私人音乐库服务器，Auddly Music Server 极简部署，内网穿透远程访问享受高品质音乐](self-hosted-private-music-server-auddly-music-server-minimal-deployment-internal-network-penetration-remote-access-high-quality-music.md)
