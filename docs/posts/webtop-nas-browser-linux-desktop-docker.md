---
title: "Webtop：NAS 上的网页桌面，Docker 部署 + 内网穿透"
date: 2026-10-17
description: "容器里跑一整套 Linux 桌面，浏览器打开就能用；附编排、资料目录怎么挂、https 那个坑怎么绕。"
canonical: https://www.passnat.com/posts/webtop-nas-browser-linux-desktop-docker
---

## 项目介绍

Webtop 是 LinuxServer 维护的一个镜像：容器里装着一整套 Linux 桌面（XFCE），画面实时串流到浏览器里。打开一个网页就是桌面，里面有 Chromium 浏览器、文件管理器、看图、看 PDF、解压缩这些常用工具，跟坐在电脑前没区别。

适合拿它当一台 **常驻的备用电脑**：家里没有多余的机器，或者不想为查个文件专门开电脑，NAS 一直开着，浏览器打开就能用。数据留在自己家里，桌面的配置和文件也都落在 NAS 的目录上。

这台机器的桌面跟着屏幕走：手机连上去，桌面就变窄成竖屏，窗口还是那些窗口，位置也还在原处。

项目地址：[点我跳转](https://github.com/linuxserver/docker-webtop)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 把网页端口放出去，再用 Docker Compose 把桌面装起来，最后在浏览器里打开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **3001**。

为什么是 3001 而不是 3000：Webtop 的网页桌面要求浏览器处在「安全上下文」里，也就是必须以 **https** 打开。镜像里 3000 是 http、3001 是 https（自签证书），所以放出去的一定是 3001。用 http 打开 3000，页面只会剩一行「This application requires a secure connection」。

建好点开隧道、按一下电源，状态变成「运行中」，详情里那一串就是公网地址，记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 webtop，路径在「我的文件」下面挑一个空文件夹。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  webtop:
    image: lscr.io/linuxserver/webtop:latest
    container_name: webtop
    restart: unless-stopped
    network_mode: bridge
    shm_size: "1gb"
    ports:
      - "3000:3000"
      - "3001:3001"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
      - LC_ALL=zh_CN.UTF-8
      - TITLE=Webtop
      - CUSTOM_USER=demo
      - PASSWORD=换成你自己的密码
    volumes:
      - ./config:/config
      - ./files:/files
```

几个地方照着改：**CUSTOM\_USER** 和 **PASSWORD** 这两行一定要改成你自己的——设了之后网页打开会先要一次账号密码，不改的话谁拿到地址都能进你的桌面。**PUID / PGID** 填 NAS 上你自己的用户，用 1000 一般就对。

**ports** 两行都留着：3000 是 http（一般就在局域网里自己看看），3001 是 https，刚才那条隧道已经指到它了。

**./config** 存桌面、应用和设置，**./files** 就是挂进桌面的资料目录——把 NAS 上想用的文件夹放这儿，桌面的文件管理器里直接能打开，改完还是存在 NAS 上。这个目录可以换成你自己的路径。

勾上「创建项目后立即启动」，点确认，镜像下载完（约 2.6 GB）就起来了。

### 三、第一次打开

用 https://公网地址 打开（TCP 隧道拿到的地址是 IP 加端口，前面要自己写 https://）。第一次会提示证书不安全，那是镜像自带的自签证书，点「继续访问」就行。

接着浏览器会弹一次账号密码框，填编排里的 **CUSTOM\_USER** 和 **PASSWORD**，确认，桌面就出来了：左边顶栏是「所有应用程序」，右上角是时间和输入法状态。

点左上角的应用程序菜单，Chromium、文件管理器、看图、看 PDF 都在里面；打开文件管理器，刚才挂进去的目录就排在左边「位置」里。

### 四、手机上用

同一个 https 地址，手机浏览器打开也是一样的桌面——Webtop 会按当前屏幕的尺寸重新编码画面，桌面变成竖屏，窗口还是原来那些窗口。在外面用流量也能打开，前提是家里的隧道通电、NAS 开着。

### 几点说明

**一定要用 https。** 这是这一期最容易踩的坑：用 http 打开，页面上只有一行「requires a secure connection」，桌面根本不出来。局域网里也一样 —— 除非你在本机用 127.0.0.1 / localhost 访问。

**隧道指 3001。** 指到 3000 的话，公网打开就是上面那行报错。

**shm\_size 别删。** 容器里跑浏览器要共享内存，1 GB 起步，删掉浏览器容易崩。

**备份 config。** 桌面布局、装过的软件、桌面上的文件都在 ./config，重建容器之前备份一份。

**账号密码不是可选项。** 这台电脑在你的 NAS 上，能进桌面就能翻你的文件，密码别用太简单的。

---

原文：<https://www.passnat.com/posts/webtop-nas-browser-linux-desktop-docker>
