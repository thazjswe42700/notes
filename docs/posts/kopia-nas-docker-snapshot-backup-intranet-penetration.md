---
title: "Kopia 快照备份：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源备份工具 Kopia：重要目录按时间做成只读快照，删了、改坏了翻回某一天就能恢复，恢复完与删除前逐字节一致；仓库加密后落在本机硬盘，再用帕斯内网穿透拿公网地址，出门也能翻快照。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/kopia-nas-docker-snapshot-backup-intranet-penetration
---

## 项目介绍

Kopia 是一个开源的备份工具，做法是给文件夹做快照：每次备份都把当时的样子记成一份只读副本，按时间排好。文件被误删、改坏了，翻回之前某一天的那份快照，把文件恢复出来就行。

它不复制整盘：没改过的内容不会重复存，快照占的空间比想象中小。内容在本地先加密再写进仓库，仓库可以放在本机硬盘、外接盘，也可以放到别的存储上；这一篇只讲放在本机硬盘这一种，不接任何云端密钥。

网页界面能看快照列表、浏览快照里的文件、按目录或单个文件恢复，也能看到每次任务的日志，恢复了哪些文件一行一行写着。

项目地址：[点我跳转](https://github.com/kopia/kopia)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Kopia 服务端装起来。装完直接用公网地址打开，以后在外面也能翻家里的快照。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 51515（就是下面编排里冒号左边的端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 kopia，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  kopia:
    image: kopia/kopia:latest
    container_name: kopia
    environment:
      KOPIA_PASSWORD: 换成你自己的仓库密码
    entrypoint: ["/bin/sh", "-c"]
    command:
      - |
        kopia repository create filesystem --path=/repository 2>/dev/null || true
        exec kopia server start \
          --insecure \
          --address=0.0.0.0:51515 \
          --server-username=kopia \
          --server-password=换成你自己的登录密码 \
          --config-file=/app/config/repository.config \
          --ui
    ports:
      - "51515:51515"
    volumes:
      - ./repository:/repository
      - /vol1/1000/要备份的目录:/data:ro
      - ./config:/app/config
      - ./cache:/app/cache
      - ./logs:/app/logs
    restart: unless-stopped
```

整段编排里只有三处要改：

**KOPIA\_PASSWORD**：仓库密码。仓库里的内容用它加密，第一次启动会用它建好仓库，以后每次启动都要能读到同一个值。换成你自己的一长串，字母数字混着来；这个值丢了，仓库就打不开了。

**--server-password**：网页登录密码。浏览器打开时用 Basic Auth 验证，用户名是编排里的 kopia。也换成一长串。

**要备份的目录**：把 /vol1/1000/要备份的目录 换成你 NAS 上真正要保护的那个文件夹，例如放文档或照片的目录。挂进容器后统一叫 /data，只读挂载，备份过程不会改动原文件。

端口 51515 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了；第一次启动会自动在 ./repository 里建仓库，几秒钟就好。

仓库、配置、缓存、日志分别落在四个目录里：repository 是备份数据，config 是仓库配置，cache 和 logs 是缓存与日志。要搬家或者做异地副本，把 repository 整个拷走就行。

### 三、第一次打开：登录、做第一份快照

用公网地址打开，浏览器会弹登录框，用户名 kopia，密码就是编排里 --server-password 填的那串。

**先做一份快照。** 点左上角的 New Snapshot，路径填 /data，点 Snapshot Now。几秒钟后回到 Snapshots 列表，就能看到 /data 这一行：大小、最近一次快照时间都写着。快照里的文件按当时的目录结构排好，点进去就能浏览。

想让备份自动跑，可以给这个源建一条策略（Policy），设成按小时或按天快照；保留数量也在策略里，默认会给 latest、hourly、daily、weekly、monthly、annual 各留一份，旧的到期自己清理。

### 四、误删之后怎么恢复

**先确认文件真的没了。** 在 NAS 上删掉一个文件，回到网页，进 Snapshots，点开 /data 那一行，再点某一份快照的时间，就能看到那份快照里的文件列表——刚删掉的文件原样躺在里面。

**再恢复。** 点 Restore Files & Directories，目标路径填 /data，把 Overwrite Files 和 Overwrite Directories 勾上，点 Begin Restore。目标是已经有内容的目录时，这两个勾必须勾，否则任务会立刻失败并提示目录非空。

恢复任务在 Tasks 里能看到结果：恢复了几个文件、多少字节，日志里逐行写着 WriteFile 了哪个文件。恢复完拿原文件的校验值对一下，跟删除前一模一样。

只想拿回单个文件的话，在快照文件列表里直接点文件名就能下载，不用整目录恢复。

### 几点说明

**加密。** 仓库用 AES256-GCM-HMAC-SHA256 加密，哈希是 BLAKE2B-256-128，这些在 Repository 页面都能看到。仓库文件被人拷走也读不出内容，前提是仓库密码没泄露。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Kopia 服务端用 --insecure 起，浏览器能正常登录；它管的是自己家的文件，公网地址别到处发，用完了可以在插件里把隧道关掉。

**升级。** 镜像标签用的是 latest，升级前先把 repository 目录备份一份，再到 Docker 里重新拉取镜像、重启项目；仓库格式是向后兼容的，升级后原有快照还能读。

**不接云端。** 这一篇只用了本地文件系统仓库。Kopia 还支持对象存储、SFTP 等仓库类型，需要异地副本的时候再按官方文档配，别把云端密钥写进编排里。

---

原文：<https://www.passnat.com/posts/kopia-nas-docker-snapshot-backup-intranet-penetration>
