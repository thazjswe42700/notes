---
title: "Kanboard：NAS Docker 任务看板 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署 Kanboard：任务做成卡片，拖到不同列就更新进度，给家人或小组成员分配任务；用帕斯内网穿透拿公网地址，手机在外面也能看和改。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/kanboard-nas-docker-family-task-board-intranet-penetration
---

## 项目介绍

Kanboard 是一个自托管的任务看板：一个项目分成几列，任务做成卡片，从左边拖到右边就是更新进度。它不依赖外部数据库，默认用 SQLite；整个应用是一个 PHP 容器，装上就能用，适合放在 NAS 上给家里人或者小团队共用。

每个项目可以有自己的列和泳道，任务能指派给成员、设到期日、加描述和评论。项目成员各用自己的账号登录，谁负责哪张卡片一眼能看出来；也可以给项目开一个只读的公开访问地址，不想让人改的时候只发出去看。

它是单容器结构，数据就放在 /var/www/app/data 一个目录里，备份这个目录就够了。项目地址：[点我跳转](https://github.com/kanboard/kanboard)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。Kanboard 默认监听容器里的 80 端口，编排里映射到宿主 8080，不跟 NAS 自带的服务抢端口。

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 kanboard，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  kanboard:
    image: kanboard/kanboard:latest
    container_name: kanboard
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      TZ: Asia/Shanghai
    volumes:
      - kanboard_data:/var/www/app/data
      - kanboard_plugins:/var/www/app/plugins

volumes:
  kanboard_data:
  kanboard_plugins:
```

两个卷分别放数据和插件：**kanboard\_data** 里是 SQLite 库和上传的附件，**kanboard\_plugins** 留给以后装插件用。换机器或者重装时把这两个卷带走，项目和任务都还在。

勾上「创建项目后立即启动」，等容器起来。第一次启动会自己建库，通常十几秒就能打开。

### 一、第一次打开：先改默认密码

用 http://NAS地址:8080 打开，默认账号是 admin，默认密码也是 admin。登录之后第一件事是右上角头像 → 修改密码，把默认密码换掉；换完会退出登录，用新密码再进一次。

如果是给家里人用，建议再切一下中文：打开右上角的 Settings → 应用设置，把语言选成「中文(简体)」，时区选 Asia/Shanghai。这个设置是全局的，之后新建的成员默认也是中文。

### 二、建一个项目，先看清四列

点顶部「新建团队项目」，名称写你要管的事情，比如「家庭事务」，标识符可以留空或者写一个英文缩写。建好之后进「看板」，默认有四列：待办、预备、进行中、完成。列名可以在项目设置的「栏目」里改，也可以加列；每个项目有自己的列，互不影响。

看板顶部每一列都显示任务数，右边还有一个总数。一眼看过去，哪一列堆得多、哪一列快空了，就知道进度卡在哪。

### 三、建任务，指派给具体的人

在「待办」列点加号，或者进项目后点「新建任务」。最少只填一个标题，比如「预订周六的营地」；下面的「指派给」选负责人，「栏目」选它从哪一列开始。到期时间、颜色、优先级都可以按需要填。

保存之后卡片就出现在对应的列里。卡片上会显示负责人和到期日，谁的事、什么时候要，不用点开也能看见。同一个人可以同时负责多张卡片，任务多的时候还能用标签或者泳道再分组。

### 四、拖一下，就是更新进度

事情开始做了，把卡片从「待办」拖到「进行中」；做完了拖到「完成」。松手之后进度会立刻写回服务端，刷新页面或者换一台设备打开，看到的都是新位置。

拖拽是 Kanboard 最主要的操作方式，不需要进编辑页改状态。家里人各看各的手机，谁把卡片拖走了，另一边刷新就能看到，不用再在群里问「那件事做到哪了」。

### 五、加一个家庭成员进来

先到右上角「用户管理」→「新用户」建账号：填用户名、姓名和密码，角色选「用户」。然后回项目，打开「权限」，在名称框里输入刚建的用户名，选中之后点「添加」，角色选「项目成员」。

加进项目之后，任务的「指派给」下拉里才会出现这个人。每个成员用自己的账号登录，看到的是同一块看板；不需要把管理员密码发给所有人。

### 六、在外面用：给看板做内网穿透

看板只在局域网里，出门就用不了。用 [帕斯内网穿透](https://passnat.com) 把 8080 映射出去：飞牛「应用中心」里搜 PassNAT，装上打开，点「创建隧道」。名字随便起，本地地址留默认，本地端口填 8080，建好点开隧道按下电源，详情里就有公网地址。

拿到公网地址之后，手机浏览器直接打开就能看板、拖卡片，跟在家里一样。不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**数据在哪。** SQLite 库、附件和项目配置都在 /var/www/app/data，对应 kanboard\_data 卷；插件在 /var/www/app/plugins，对应 kanboard\_plugins 卷。备份这两个卷就够了。

**默认不公开。** 新建的项目默认只有项目成员能看。需要给外人只读浏览时，才去项目的「公开访问」页开一个链接；不想让人看了随时可以关掉。

**管理入口别直接放公网。** 8080 是登录入口，需要远程管理时临时开穿透，用完关掉；只给家里人看进度的话，尽量只开看板需要的那部分权限。

**升级。** 镜像标签是 latest，升级前先备份两个卷，再拉新镜像、重启项目。大版本升级偶尔会改表结构，容器启动时会自己迁移，别在迁移中途断电。

**它不是万能的项目管理。** Kanboard 只管看板和任务流转，不负责网盘、聊天或者日历。需要文档就去装文档工具，需要文件同步就用 NAS 自己的文件服务，各管一摊更省心。

---

原文：<https://www.passnat.com/posts/kanboard-nas-docker-family-task-board-intranet-penetration>


## 相关文章

- [Focalboard 看板：NAS Docker 部署 + 内网穿透](focalboard-nas-docker-kanban-intranet-penetration.md)
- [Cronicle Edge 定时任务：NAS Docker 部署 + 内网穿透](cronicle-edge-nas-docker-scheduled-tasks-web-ui-job-logs-intranet-penetration.md)
- [Leantime 项目管理：NAS Docker 部署 + 内网穿透](leantime-nas-docker-project-management-goals-timesheets-intranet-penetration.md)
- [开源自托管项目管理工具Focalboard部署指南，NAS内网穿透远程访问完整教程](focalboard-open-source-self-hosted-project-management-deployment-guide-nas-internal-penetration-remote-access-tutorial.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
