---
title: "Cronicle Edge 定时任务：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署 Cronicle Edge 定时任务面板：网页里建任务、写 Shell 脚本、按天或按周排期，任务列表一眼看到状态和耗时，点进去就是完整执行日志；用帕斯内网穿透拿公网地址，在外面也能改任务、看日志。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/cronicle-edge-nas-docker-scheduled-tasks-web-ui-job-logs-intranet-penetration
---

## 项目介绍

Cronicle Edge 是一个自托管的定时任务面板（Cronicle 的社区分支）。它把「什么时候跑、跑什么、跑得怎么样」这三件事放进一个网页里：任务、时间、脚本都在浏览器里写，不用去碰系统里的定时任务表；任务列表里一眼看到每个任务的时间、状态和耗时，点进去就是完整的执行日志。

任务用插件执行，常见的有 Shell Script、HTTP Request、SSH、Docker 等。一个任务可以设成每天、每周、每月、按间隔，或者干脆只手动跑；还能设超时、失败重试、日志保留天数、资源上限。跑完的记录都在 Completed 里，按事件筛、按时间翻。项目地址：[点我跳转](https://github.com/cronicle-edge/cronicle-edge)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Cronicle Edge 用 Docker Compose 装起来，以后在外面也能打开面板、改任务、看日志。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3012（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 cronicle，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  cronicle:
    image: ghcr.io/cronicle-edge/cronicle-edge:main
    command: manager
    hostname: cronicle
    restart: unless-stopped
    ports:
      - "3012:3012"
    environment:
      CRONICLE_secret_key: 换成你自己的密钥
      TZ: Asia/Shanghai
    volumes:
      - cronicle_data:/opt/cronicle/data

volumes:
  cronicle_data:
```

这段编排里要改的只有密钥：

**CRONICLE\_secret\_key**：换成你自己的、长一点的字符串。它用来签名登录会话，换掉之后原来的登录会失效，重新登一次就行。

**command: manager**：官方镜像的启动参数。manager 是主节点，单机部署就它一个；多机才需要另起 worker。

**hostname: cronicle**：这一行别删。容器重建（换镜像、改编排）之后 hostname 会变，面板会一直停在「Waiting for manager server...」，加一个固定 hostname 就没事。

端口 3012 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像拉下来之后十几秒就起来了。

### 三、第一次打开：管理员账号

数据目录是空的，第一次启动时容器会自己建一个管理员账号，用户名 admin、密码 admin。用公网地址打开就是登录页，用它登进去，第一件事是到右上角的「My Account」把密码改掉——这个面板能在 NAS 上执行脚本，别留着默认口令。

数据都在 cronicle\_data 这个具名卷里，包括任务、运行历史、日志和账号。备份这一个卷就够了；重启、升级镜像都不会丢。

### 四、建一个定时任务

点顶部的「Schedule」，再点「Add Event」。表单从上往下：Event Name 写任务名；Category 选 General；Target 选 All Servers；Plugin 选「Shell Script」，下面就会出现脚本编辑器，直接把命令写进去。

再往下是 Timing：选 Daily，在 Timing Details 里勾上几点跑、几分跑（演示里用的是每天 12:00）。同一个面板里还能设 Timeout 超时、Retries 失败重试、Expire Days 日志保留天数。保存之后任务就出现在列表里，到点自动跑。

演示用的脚本只操作容器里挂进来的演示目录，比如把下载目录里的文件按日期归档：

```
#!/usr/bin/env bash
cd /demo/downloads || exit 1
count=$(ls -1 | wc -l)
echo "扫描下载目录，共 $count 个文件"
target="/demo/sorted/$(date +%Y-%m-%d)"
mkdir -p "$target"
for f in *; do
  [ -e "$f" ] || continue
  mv "$f" "$target/" && echo "  已归档：$f"
done
echo "整理完成，归档到 $(date +%Y-%m-%d)"
```

脚本是在容器里跑的，容器里只能看到你挂进去的目录。要让它整理 NAS 上真实的某个文件夹，得在编排的 volumes 里把这个目录挂进容器，再在脚本里用容器内的路径。

### 五、跑一次，看日志

不用等到点，点任务行里的「Run」就立刻跑一次。跑完回到列表，状态一栏是绿色的 Success；点任务名进「Job Details」，上面是开始/结束时间、耗时、CPU 和内存，下面「Console Output」黑底的那一块就是执行日志，脚本打印的每一行都在，出没出错一眼就能看到。

想看全部历史，点顶部的「Completed」；按事件筛出来，每次运行一行，点 Job ID 进去还是同一份日志。

### 几点说明

**数据在哪。** 任务、历史、日志都在 cronicle\_data 卷里，备份这个卷就行；想搬到别的机器，把卷导出来带走。

**升级。** 镜像标签用的是 main，升级前先把卷备份一份，再到 Docker 里重新拉取镜像、重启项目。编排里固定了 hostname，重建后不会卡在等待主节点。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，面板本身不需要 HTTPS 就能用；但它能在 NAS 上执行脚本，公网地址别到处发，用完可以在插件里把隧道关掉，口令也一定改掉。

**脚本权限。** 容器里的任务默认以 root 身份跑，写脚本时留意别误删文件；只操作专门挂进去的目录，别把 NAS 的系统目录整个挂进来。

---

原文：<https://www.passnat.com/posts/cronicle-edge-nas-docker-scheduled-tasks-web-ui-job-logs-intranet-penetration>


## 相关文章

- [开源命令行可视化工具OliveTin，NAS一键部署，内网穿透远程访问，无公网也能随时执行](olivetin-nas-open-source-cli-visualization-tool-intranet-penetration-remote-access.md)
- [自建私人书签管理库 一处部署随处访问，OneNav Docker极简快速部署指南](onenav-docker-bookmark-manager-deployment-guide.md)
- [轻量级工作流引擎Dagu无代码部署，NAS一键启动内网穿透远程管理任务](dagu-lightweight-workflow-engine-nas-remote-management-no-code.md)
- [mTab书签管理工具私有部署指南，跨平台同步无广告，内网穿透远程访问](mtab-bookmark-manager-private-deployment-guide-cross-platform-sync-no-ads-intranet-penetration-remote-access.md)
- [高性能Rust端口扫描工具RustScan快速部署指南，3秒全端口扫描内网穿透远程访问](rust-scan-high-performance-port-scanner-quick-deployment-guide.md)
