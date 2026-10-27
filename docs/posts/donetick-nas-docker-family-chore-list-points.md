---
title: "Donetick 自建家务清单：NAS Docker 部署 + 全家分工 + 积分"
date: 2026-10-18
description: "单容器、SQLite 的开源家务清单：配置文件怎么放、Compose 怎么起、家人怎么加入同一个 Circle、每天每周重复和积分怎么设。"
canonical: https://www.passnat.com/posts/donetick-nas-docker-family-chore-list-points
---

## 项目介绍

Donetick 是一个开源的家务与任务清单：把洗碗、遛狗、倒垃圾这些事建成任务，指定给家里的人，设好每天或每周重复；谁做完了就在手机上点一下勾，完成了会记积分。它把「谁该做什么」摊在一张清单上，省掉了每天在群里问「今天谁洗碗」。

一个 Go 写的服务加一个 SQLite 数据库，一个容器就能跑，不用另外装数据库，也不用配邮件服务器。界面是英文的，但日期会跟着浏览器语言走，中文系统下日历显示中文。

项目地址：[点我跳转](https://github.com/Donetick/donetick)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。单容器加两个目录，一份 Compose 编排就够了。

### 一、想在外面也能用，先把 2021 端口放出去

只在局域网里用，这一步可以跳过，直接看第二步。要让手机在外面也能打开，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **2021**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、先放好配置文件

这个项目启动时会读 **/config/selfhosted.yaml**，没有这个文件会起不来。在 Compose 项目目录下建一个 **config** 文件夹，把下面这段存成 **config/selfhosted.yaml**：

```
name: "selfhosted"
is_done_tick_dot_com: false
is_user_creation_disabled: false
database:
  type: "sqlite"
  migration: true
server:
  port: 2021
  serve_frontend: true
  serve_swagger: false
  public_host: ""
jwt:
  secret: "换成你自己的随机字符串至少32位"
  session_time: 168h
  max_refresh: 1440h
logging:
  level: "info"
realtime:
  enabled: true
  sse_enabled: true
```

里面最要紧的是 **jwt.secret**，换成你自己的随机字符串，至少 32 位；其他照着填就行，数据库用的 SQLite 会自动建在 data 目录里。

### 三、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 donetick，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  donetick:
    image: donetick/donetick:latest
    container_name: donetick
    restart: unless-stopped
    ports:
      - "2021:2021"
    volumes:
      - ./data:/donetick-data
      - ./config:/config
    environment:
      - DT_ENV=selfhosted
      - DT_SQLITE_PATH=/donetick-data/donetick.db
      - TZ=Asia/Shanghai
    healthcheck:
      test: wget --no-verbose --tries=1 --spider http://localhost:2021/api/v1/health || exit 1
      start_period: 1m
      timeout: 5s
      interval: 1m
      retries: 3
```

容器里固定监听 2021，冒号前面那个数字是 NAS 上的端口，被别的项目占了就改它，后面的不用动。两个目录：**./data** 存数据库，**./config** 放刚才那个配置文件。

勾上「创建项目后立即启动」，点确认。镜像不大，一会儿就下完，容器状态变成「运行中」就可以打开网页了。

### 四、第一次打开：建账号、建一个 Circle

浏览器打开 **http://NAS的局域网地址:2021**，第一次会让你注册：显示名、用户名（小写字母和数字，至少 4 位）、邮箱、密码。注册完有两个开关是「要不要匿名上报崩溃和用量」，默认都是关的，直接继续。

接着是建立 Circle —— 就是一个共享圈子，家里所有人加进来，看到的家务清单是同一份。圈子的设置页里有一个邀请码，家人各自注册后，在同一个页面输入邀请码发请求，管理员点一下同意就进来了。

这个邀请码等于家门钥匙，别发到公开的地方。

### 五、把家务建成清单

点右上角或者左下角的加号新建任务。上面填名字，下面三块是按需展开的：

**Schedule**：选 Repeating task，再挑 Daily 或者 Weekly；Weekly 会在今天这个星期几重复，想要别的日子可以在 Custom 里选。下一次到期时间默认就是今天，也可以改。

**Assignment**：勾上谁可以接这件事。只勾一个人就是固定给他；勾「Anyone」就是谁有空谁做。

**On completion**：点 Add action，选 Award points，填个分数，比如洗碗 10 分、遛狗 15 分、倒垃圾 5 分。

建好之后，清单会按「今天 / 明天 / 未来 7 天」分组；每个人打开首页只看到自己那几件，做完点任务后面的勾，或者进详情点 Mark as done。

### 六、积分和记录

左侧菜单的 Points 是积分榜：按人排名，上面还有一条 7 天趋势图；Activities 里是完成记录，每条写着谁做的、拿了多少分。想让孩子愿意干，可以拿积分换点小奖励，比如 100 分换一次选电影。

### 几点说明

**必须带配置文件。** 这是这个项目跟大多数单容器应用不一样的地方：没有 config/selfhosted.yaml 起不来。升级重建容器时别把 config 目录丢了。

**数据都在 data 目录。** 数据库是 data/donetick.db，备份就是备份这个目录；换机器把 data 和 config 一起拷过去就行。

**界面是英文的。** 目前没有中文界面，日期会跟随浏览器语言。照着上面的位置点，不影响使用。

**公网暴露要谨慎。** 它带账号密码，但毕竟是家里所有人都在用的清单。挂到公网时建议在隧道那一层再加一道网页访问口令。

**端口冲突。** 宿主 2021 被占了就改编排里冒号前面那个数字，容器里的 2021 不用动。

**更新。** 拉新镜像重建容器即可；数据库迁移会在启动时自动跑，先备份 data 目录更稳。

---

原文：<https://www.passnat.com/posts/donetick-nas-docker-family-chore-list-points>


## 相关文章

- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
- [Isso 评论区 NAS Docker 部署 + 内网穿透](isso-nas-docker-self-hosted-comments-intranet-penetration.md)
- [Rustpad 协同编辑器：NAS Docker 部署 + 内网穿透](rustpad-nas-docker-collaborative-code-editor-intranet-penetration.md)
- [OpenGist 自建代码片段库：NAS Docker 部署 + 修订差异怎么看](opengist-nas-docker-snippet-share-revision-history.md)
