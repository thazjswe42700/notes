---
title: "WPS Office 浏览器在线办公：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "把 WPS Office 的 Linux 版装进 Docker 跑在自己的 NAS 上，浏览器打开网页就是熟悉的 WPS 桌面，写好的文档存在 NAS 里、不经过云。用帕斯内网穿透拿公网地址，在外面用手机、笔记本也能接着改。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/wps-office-nas-docker-browser-online-office-intranet-penetration
---

## 项目介绍

WPS Office 是金山办公的办公套件，文字、表格、演示三件套大家都不陌生。平时要么在电脑上装一整套，要么用网页版、但文件默认存在厂商的云里。这一篇讲的第三种用法：把 WPS 的 Linux 版装进 Docker，跑在自己 NAS 上，用浏览器连过去就是一台带 WPS 的桌面，写好的文档直接存在 NAS 里，不经过任何云。

这里用的是 LinuxServer 打包的镜像（lscr.io/linuxserver/wps-office:chinese），里面已经装好了中文版 WPS，再用 KasmVNC 把桌面搬到浏览器里。你打开网页看到的就是熟悉的 WPS 界面，字体、字号、加粗、对齐、表格、演示都在。

项目地址：[点我跳转](https://github.com/linuxserver/docker-wps-office)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。这个应用打开不需要登录、也没有账号页，装好直接就是 WPS 桌面，所以先装、后做内网穿透都可以；想在外面用手机、笔记本打开，就用 [帕斯内网穿透](https://passnat.com) 拿一个公网地址。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 wps-office，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  wps-office:
    image: lscr.io/linuxserver/wps-office:chinese
    container_name: wps-office
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
    ports:
      - "3000:3000"
    shm_size: "1gb"
    volumes:
      - ./config:/config
```

**ports**：左边 3000 是宿主机端口，右边 3000 是容器里的网页端口，一般不用改。

**shm\_size**：给 1GB 共享内存，网页桌面要靠它，别删。

**volumes**：./config 是 NAS 上存数据的文件夹，你的文档、WPS 的设置都在里面，备份就把它整个拷走。

**PUID / PGID**：填你 NAS 用户的 uid 和 gid（飞牛一般是 1000），文件权限才对。勾上「创建项目后立即启动」，点确认，镜像有点大（几个 G），拉完就起来了。

镜像标签用的 chinese 是中文版；不想要中文界面可以换 latest。第一次打开如果弹「发现新版本，是否更新」，点「下次再说」就行。

### 二、用帕斯内网穿透拿到公网地址（在外面也能用）

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3000（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口）。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 三、打开、用起来

浏览器打开公网地址（在家里也可以直接用 NAS 的局域网地址加 3000），没有登录页，直接就是 WPS 的桌面。点「新建」，选「文字」就是一个空文档，选「表格」「演示」就是另外两个。

在空白页上敲字，跟桌面版 WPS 一样：上面一排是字体、字号、加粗、斜体、下划线、对齐。写完按保存，存到「我的电脑」里——它其实就是容器里那个 /config 文件夹，也就是 NAS 上你挂载的那个 config 目录。

### 四、文档存在哪、怎么备份

你保存的文档都在 ./config 里（具体在 /config/Documents 下面）。重启容器、升级镜像、甚至换一台机器，只要把 config 这个文件夹带上，文档和设置都还在。要备份，直接把这个文件夹整个拷走就行。

### 几点说明

**中文输入。** 网页桌面里打字用的是浏览器键盘，中文一般靠系统的输入法；如果发现打不出中文，可以在客户端这边把文字先打好再粘贴进去，或者用 WPS 自带的「插入」里的符号。

**几个 G 的镜像。** 第一次拉镜像会比较久，属正常；NAS 上留够空间，8G 内存的机器跑它没问题。

**要不要加密码。** 默认没有鉴权，谁拿到地址都能进桌面。只在家里用、或者经内网穿透出去但地址只有自己知道，问题不大；想更稳妥，可以在编排的环境变量里加 CUSTOM\_USER 和 PASSWORD，打开时会先弹一个用户名密码框。

**更新。** 到 Docker 里重新拉取镜像、重启项目即可；升级前把 config 文件夹备份一份。

---

原文：<https://www.passnat.com/posts/wps-office-nas-docker-browser-online-office-intranet-penetration>
