---
title: "LibrisLog 读书清单：NAS Docker 部署 + 内网穿透"
date: 2026-10-18
description: "在 NAS 上用 Docker Compose 部署开源读书清单 LibrisLog：想读/在读/已读/弃读四个书架，记进度、评分和书评，仪表盘和统计页看阅读轨迹；用帕斯内网穿透拿公网地址，出门在手机也能查自己的书单。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/librislog-nas-docker-reading-list-progress-reviews-intranet-penetration
---

## 项目介绍

LibrisLog 是一个开源的读书清单工具：想读、在读、已读、弃读四个书架，每本书可以记评分、写几行书评、填 ISBN 和页数，读到第几页也能随手记一笔。它管的是「读到哪里、读完了什么、当时怎么想的」，不是电子书文件库。

仪表盘上一眼看到书库总数、已读多少、今年读完几本；统计页把语言、媒介、评分和每月的完成量做成图表。书按 Open Library 的条目搜，封面和页数自动带进来，也可以手动填。

项目地址：[点我跳转](https://github.com/codebude/librislog)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 LibrisLog 用 Docker Compose 装起来。它没有必须预填的公网地址字段，先装再建隧道也可以；本文按「先穿透、后部署」的顺序写，出门在书店里也能直接打开自己的书单。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8001（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 librislog，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，只需要改一处：

```yaml
services:
  backend:
    image: ghcr.io/codebude/librislog/librislog-api:latest
    restart: unless-stopped
    environment:
      DATABASE_URL: sqlite:///./data/librislog.db
      COVERS_DIR: ./data/covers
      API_KEY_ENCRYPTION_KEY: "换成你自己的一长串随机字符（32 位以上）"
      AUTH_COOKIE_NAME: librislog_session
      AUTH_COOKIE_SECURE: "false"
      AUTH_COOKIE_SAMESITE: lax
      DASHBOARD_QUOTE_ENABLED: "false"
      THALIA_COVER_SEARCH_ENABLED: "false"
      TELEMETRY_DISABLED: "true"
      FORWARDED_ALLOW_IPS: "*"
    volumes:
      - ./data:/app/data

  frontend:
    image: ghcr.io/codebude/librislog/librislog:latest
    restart: unless-stopped
    ports:
      - "8001:80"
    depends_on:
      - backend
```

**API\_KEY\_ENCRYPTION\_KEY**：换成你自己的随机字符串，32 位以上，字母数字混着来。它用来加密保存接口密钥，第一次启动前就填好；已经在用了再换，之前存的密钥会解不开。

端口 8001 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，两个镜像下完就起来了。数据库和封面都在 ./data 下面，备份整个目录就行。

后端第一次启动要跑数据库迁移，大约一分钟；这期间打开页面是正常的，但接口会短暂报 502，等一会儿刷新就好。

### 三、第一次打开：建管理员账号

用公网地址打开，第一次会跳到创建管理员页面。右上角语言下拉里可以选中文，填名字、邮箱和密码（至少 8 位，要有大写、小写、数字和符号），点「创建管理员」。

建完直接进仪表盘。以后打开同一个地址，用这个邮箱和密码登录。

### 四、把书架建起来

打开「书库」，点「添加图书」。两种加书方式：

**搜索并导入**：输入书名或 ISBN，点搜索，结果里带封面、作者、出版社和页数，找到对的点「添加」。中文书名也能搜，同一个书名会有很多版本，挑封面和页数对得上的那个。

**手动添加**：自己填标题、作者、页数，ISBN 和封面可以留空。封面留空的话，卡片上显示一个书本图标。

加进来之后可以点开每本书改状态：想读、在读、已读、弃读；填评分和笔记；在读的书把进度滑到读到的页数。这些都在右侧的详情面板里，改完即时保存。

### 五、仪表盘、统计和时间线

仪表盘上方四张卡是书库总数、已读、待读、正在阅读；下面列出在读的书和进度百分比，再往下是连续阅读天数和想读清单。

「统计」页按语言、状态、所有权、媒介分别画分布条，还有平均每月本数、最活跃月份、平均页数和封面墙。「时间线」按完成月份倒序列出读过的书和当时的评分，适合翻回去看某一年的阅读轨迹。

在书店或者图书馆，用手机打开同一个公网地址，搜索书名就能确认家里有没有这本、读没读过，不用凭印象买重复的书。

### 几点说明

**没有配置发信邮箱。** 不配 SMTP 也能正常用；只是「忘记密码」的找回邮件发不出去。管理员可以在「管理 → 用户」里给其他家庭成员建账号，每个账号的书架互相独立。

**搜书和封面依赖 Open Library。** 这是外部服务，纯内网、断外网的环境里搜索和自动封面用不了，手动添加不受影响。个别 ISBN 对应的封面可能是错版，搜到不对就换一个版本，或者手动加书。

**明文 HTTP 访问。** 编排里 AUTH\_COOKIE\_SECURE 设成 false，是为了让 HTTP 地址也能登录；这会牺牲一点安全性。以后换成 HTTPS 地址，把它改成 true 更稳妥。页面里的条码扫描在 HTTP 下不可用，属于浏览器的安全限制，不影响其他功能。

**更新。** 镜像标签用的是 latest，升级之前把 data 目录备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/librislog-nas-docker-reading-list-progress-reviews-intranet-penetration>
