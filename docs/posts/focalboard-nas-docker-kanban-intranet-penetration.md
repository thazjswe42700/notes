---
title: "Focalboard 看板：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源看板 Focalboard：任务写成卡片，按状态分列，拖动就换状态；卡片里填负责人、优先级、预计工时，团队进度一块板看全。用帕斯内网穿透拿公网地址，在外面用手机也能打开。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/focalboard-nas-docker-kanban-intranet-penetration
---

## 项目介绍

Focalboard 是一个开源的看板工具，用法跟 Trello 那类产品一样：任务写成一张张卡片，按状态分成几列，拖一下卡片就换了状态。区别是整套东西装在自己家的 NAS 上，数据在自己的硬盘里，不用把项目进度交给别人托管。

一块看板上可以放很多张卡片，每张卡片有标题，还能加属性：负责人、优先级、预计工时、截止日期。看板默认按「状态」这个属性分列，列名就是状态名，可以按自己的流程改。项目进度不用挨个问，整块板子扫一眼就知道谁手上压着什么事、哪一步卡住了。

界面目前是英文的，用法很直白，照着下面的步骤走一遍就会了。

项目地址：[点我跳转](https://github.com/mattermost/focalboard)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Focalboard 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后在外面用手机也能看团队的进度。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3710（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 focalboard，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  focalboard:
    image: mattermost/focalboard:latest
    container_name: focalboard
    restart: unless-stopped
    ports:
      - "3710:8000"
    environment:
      FOCALBOARD_PORT: "8000"
      FOCALBOARD_SERVER_ROOT: "http://nas.local:3710"
    volumes:
      - fb_data:/opt/focalboard/data

volumes:
  fb_data:
```

整段编排里只有一处要改：

**FOCALBOARD\_SERVER\_ROOT**：换成刚才那串公网地址，写成 http://公网地址:端口 的形式。它用来生成分享链接这类绝对地址，填错了应用照样能打开，只是分享出去的地址会指错地方。如果暂时只在家里用，也可以先填局域网地址，以后拿到公网地址再回来改。

端口 3710 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了。这是一个单容器应用，数据库用的是内置的 SQLite，不用另外装数据库。

数据都在 fb\_data 这个具名卷里（容器里的 /opt/focalboard/data），备份这一个卷就够了。

### 三、第一次打开：注册账号

用公网地址打开，第一次进来是登录页，点下面的「create an account」注册一个账号：填邮箱、用户名、密码，提交就直接登录进去了。这个实例是私有的，账号自己定，不需要邮箱验证码。

注册完会落在看板首页，左边是看板列表，右边是模板。自己用的话，从空白看板开始最清楚。

### 四、建看板、加列、加卡片

**建一块看板。** 点「+ Add board」，选「Create an empty board」，给看板起个名字。新看板默认按「Status」分组，还没有状态选项，所以只有一列「No Status」。

**把状态列建出来。** 点列头旁边的「+ Add a group」，或者在「属性」菜单里编辑 Status 属性，把列名按自己的流程加上：待办、进行中、卡住、已完成，想叫什么就叫什么。

**加卡片。** 每列下面都有「+ New」，点一下就能写一张卡片，把要做的事写上去。卡片可以随时拖到别的列，状态跟着变。

如果懒得从空白开始，也可以直接用自带的项目任务模板：它已经把状态列和几张示例卡片建好了，把示例卡片改成自己的事就行。

### 五、卡片里填负责人和优先级

点开一张卡片，右边能看到属性：Status、Priority、Assignee、Estimated Hours、Due Date。点 Assignee 输入用户名就能把人加上，Priority 选高、中、低，Estimated Hours 填个大概工时。

这些都填上之后，整块看板就不只是「事情列出来了」：谁负责、哪件最急、大概要多久，都在卡片上。开会前扫一眼，谁手上压着几件事、哪一步卡在 Blocked，不用挨个问。

### 几点说明

**数据在哪。** 数据库和上传的附件都在 fb\_data 卷里，备份这一个卷就行；想搬到别的机器，把卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先把 fb\_data 卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Focalboard 本身不需要 HTTPS 才能用；但看板上是团队的真实任务，公网地址别到处发，用完了可以在插件里把隧道关掉。

**分享和协作。** 卡片右上角有 Share，可以生成看板的分享链接；这个链接用的就是编排里 FOCALBOARD\_SERVER\_ROOT 填的地址，所以换地址之后记得回来同步改一下。这篇只写了单人建板这条最稳的路，多人协作等熟了自己再摸。

---

原文：<https://www.passnat.com/posts/focalboard-nas-docker-kanban-intranet-penetration>


## 相关文章

- [开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程](focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial.md)
- [Kanboard：NAS Docker 任务看板 + 内网穿透](kanboard-nas-docker-family-task-board-intranet-penetration.md)
- [Excalidraw 白板：NAS Docker 部署 + 内网穿透](excalidraw-nas-docker-whiteboard-diagrams-intranet-penetration.md)
- [ReDoc 接口文档：NAS Docker 部署 + 内网穿透](redoc-api-docs-nas-docker-openapi-intranet-penetration.md)
- [Leantime 项目管理：NAS Docker 部署 + 内网穿透](leantime-nas-docker-project-management-goals-timesheets-intranet-penetration.md)
