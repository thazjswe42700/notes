---
title: "Jupyter Notebook：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源数据分析笔记本 Jupyter Notebook：代码和运行结果存在同一个文件里，笔记本和数据都留在 NAS 上，电脑、手机打开浏览器就能接着算；用帕斯内网穿透拿公网地址，在外面也能看结果。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/jupyter-notebook-nas-docker-data-analysis-intranet-penetration
---

## 项目介绍

Jupyter Notebook 是一个开源的交互式笔记本：代码写在一个个格子里，点运行，输出就贴在格子下面。它最早是给数据分析和教学用的，现在也常被拿来当随手算数、整理表格、画图的小工具。

一个笔记本文件里同时存着代码、说明文字和运行结果，发给别人，对方打开就能看到你是怎么算的，而不是只拿到一张截图。官方镜像 jupyter/base-notebook 里带着 Python 3、JupyterLab 和经典的 Notebook 界面，浏览器打开就能用，本机不用装 Python。

项目地址：[点我跳转](https://github.com/jupyter/docker-stacks)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Jupyter 用 Docker Compose 装起来。装好以后，笔记本和数据都留在 NAS 上，电脑、手机打开浏览器就能接着算。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8888——也就是下面编排里冒号左边的端口。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 jupyter，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  jupyter:
    image: jupyter/base-notebook:latest
    container_name: jupyter
    restart: unless-stopped
    ports:
      - "8888:8888"
    environment:
      - JUPYTER_TOKEN=换成一串自己的口令
      - NB_UID=1000
      - NB_GID=100
    volumes:
      - jupyter_work:/home/jovyan/work

volumes:
  jupyter_work:
```

整段编排里只有一处要改：

**JUPYTER\_TOKEN**：换成你自己的一长串口令，字母数字混着来。它会变成打开笔记本时要求输入的那串口令，别用 123456 或者项目名这种一眼能猜到的。装好之后想改它，改这里再重启项目就行，已经打开的浏览器需要重新输入一次。

端口 8888 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完（约 1 GB）就起来了，日志里出现 “Jupyter Server … is running at” 就可以访问了。

笔记本和 CSV 都在 work 目录里，挂在具名卷 jupyter\_work 上：重建容器、升级镜像，文件都还在。备份这个卷就够了。

### 三、第一次打开：输入口令进 JupyterLab

用公网地址打开，会先看到一个要求输入口令的页面。把编排里 JUPYTER\_TOKEN 填的那串贴进去，点登录，就进到 JupyterLab。

左边是文件栏，当前在 work 目录；右边是主工作区，可以新建 Notebook、打开已有文件，也可以开一个终端。登录状态会记在浏览器里，下次打开同一个地址不用再输一遍；换一台设备、换一个浏览器才需要重新输入。

### 四、把分析跑起来

把手机导出的数据整理成 CSV，放进 work 目录（JupyterLab 文件栏上方有上传按钮，也可以直接拖进去）。然后新建一个 Notebook，写上读文件、按周汇总的代码，按 Shift + Enter 一格一格运行。

每个格子下面会立刻显示输出：print 出来的文字、生成的表格、画出来的图，都跟着代码一起存进这个 .ipynb 文件。关掉浏览器、过几天再打开，上次的运行结果还在，不用从头再跑。

**关于依赖库：** base-notebook 是精简镜像，只带 Python 标准库和 Jupyter 本身，没有预装 pandas、numpy、matplotlib。读 CSV、算总数、用 HTML 画一个条形表格，标准库的 csv、collections 加上 IPython.display 就够了，装完就能跑。确实需要 pandas 的，在 Notebook 里执行 pip install pandas 即可，但容器重建后要重装，建议另做一个自己的镜像。

### 五、手机看结果

同一个公网地址，手机浏览器打开，输入口令，进到同一个 work 目录，点开笔记本就能看上次跑出来的表格和数字。也可以直接在手机上改一行代码再运行，结果马上刷新。

不用在手机上装 Python，也不用把文件传来传去：算的东西一直在 NAS 上，浏览器只是个窗口。

### 几点说明

**数据在哪。** 所有笔记本、CSV 和其他文件都在 jupyter\_work 卷里，容器里的路径是 /home/jovyan/work。想搬到别的机器，把这个卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先备份 jupyter\_work 卷，再到 Docker 里重新拉取镜像、重启项目。个人文件在卷里，不受镜像升级影响。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Jupyter 本身不需要 HTTPS 才能用；但它能执行任意 Python 代码，等于一台可以远程操作的机器，口令要设得够长，公网地址别到处发，用完可以在插件里把隧道关掉。

**资源占用。** 空载时内存占用不高，跑大一点的数据分析时才会吃内存；在 NAS 上同时跑别的服务，建议先看一眼剩余内存再开大任务。

---

原文：<https://www.passnat.com/posts/jupyter-notebook-nas-docker-data-analysis-intranet-penetration>


## 相关文章

- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
- [Mattermost 团队聊天：NAS Docker 部署 + 内网穿透](mattermost-team-chat-nas-docker-intranet-penetration.md)
- [轻量级客户端加密笔记本，NAS一键部署Notepad，口令登录随时随地访问笔记](lightweight-encrypted-notes-nas-one-click-deploy-notepad-password-login.md)
- [自托管私密笔记应用Notea，Docker极简部署，云存储灵活配置，内网穿透远程访问](self-hosted-private-notes-notea-docker-deployment-cloud-storage-remote-access.md)
