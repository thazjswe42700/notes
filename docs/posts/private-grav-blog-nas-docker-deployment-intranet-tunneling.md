---
title: "Grav 建站：NAS 上 Docker 部署文件型 CMS + 内网穿透"
date: 2026-10-20
description: "Grav 是一个不用数据库的建站工具：内容就是 Markdown 文件，整站放在 NAS 上，Docker Compose 一条命令跑起来，用帕斯内网穿透从外面打开。含首次建管理员、写页面、主题和备份步骤。"
canonical: https://www.passnat.com/posts/private-grav-blog-nas-docker-deployment-intranet-tunneling
---

## 项目介绍

Grav 是一个开源的建站工具，最大的特点是**不用数据库**。文章写成 Markdown 文件，配置用 YAML，一个页面就是 user/pages 下面的一个文件夹。没有数据库要装、要备份、要升级，整站拷走就能搬到另一台机器。

第一次打开会进一个建管理员页面，填用户名、邮箱、密码，点一下「Create administrator」就进后台。后台里能直接写页面、传图片、换主题，写好的内容前台立刻能看到。

项目地址：[点我跳转](https://github.com/getgrav/grav)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。

先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Grav 装起来。Grav 自己不挑地址，先装后穿也行；这里按先拿地址的顺序讲，后面填端口不容易乱。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 grav，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  grav:
    image: linuxserver/grav:latest
    container_name: grav
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      PUID: 1000
      PGID: 1000
      TZ: Asia/Shanghai
    volumes:
      - ./config:/config
```

逐行看一遍：

**image** 是 LinuxServer 维护的镜像，里面已经带好 nginx 和 PHP，不用另外装环境。

**ports** 的 8080:80，左边是 NAS 上的端口，要跟隧道里填的本地端口一致；右边 80 是容器内部的端口，不用改。

**PUID / PGID** 都写 1000，让容器用你的用户身份写文件，之后在「我的文件」里能直接打开内容文件夹。

**./config:/config** 把整个 Grav 放在当前文件夹的 config 目录里：页面、主题、插件、日志都在里面。

勾上「创建项目后立即启动」，点确认。第一次要拉镜像，等一两分钟。

### 三、第一次打开，建管理员

浏览器打开 http://NAS的局域网地址:8080，没有账号时首页会直接跳到 /admin/setup。填 Username、Full name（可选）、Email、Password、Confirm password，点「Create administrator」。

这一步不需要邮件验证，也不需要连数据库——它只是往 user/accounts 里写一个 YAML 文件。建好直接进后台，左边是 Dashboard、Pages、Themes、Plugins 几个入口。

### 四、写第一篇内容

左边点 Pages，右上角点 Add Page。填 Page Title（页面标题）、Folder Name（文件夹名，网址里用它）、Parent Page 留 <root>、Page Template 选 Default、Status 选 Published、Visible 选 Yes，点 Continue。

中间就是编辑器，直接写 Markdown：井号开头是标题，减号开头是列表，空一行就是新段落。写完点右上角 Save，前台打开 /你的文件夹名 就能看到。

想加图片，把图片拖到页面下方的 Page Media，正文里用感叹号加方括号引用图片文件名就行。

### 五、主题和插件

装好之后自带 Quark 2 主题，是官方维护的默认主题，浅色深色都支持，Pages 里写的内容直接就有排版。在 Themes 页能看到它，点 Configure 可以改配色和字体。

插件页有 10 个内置插件（Admin、API、Email、Form、Login 等），日常写文章够用，不用额外装。

### 六、备份和搬家

整站都在 config 文件夹里：config/www/user/pages 是文章，config/www/user/themes 是主题，config/www/user/config 是站点设置。备份就是把 config 整个拷走；换一台机器，把 config 放回去，编排一贴就恢复了。

整个目录里没有任何数据库文件，这也是 Grav 最省心的地方。

### 几点说明

**端口被占用。** 8080 被别的容器占了，把编排里 "8080:80" 左边改成别的（比如 8081），隧道里的本地端口也跟着改。

**换了公网地址。** Grav 不把地址写进文章里，换隧道不用改内容，朋友用新地址打开就行。

**更新。** 镜像标签用 latest，升级之前先把 config 备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/private-grav-blog-nas-docker-deployment-intranet-tunneling>


## 相关文章

- [写公众号不用再手动排版：飞牛 NAS 用一个容器装 doocs/md，Markdown 实时出公众号样式，配内网穿透在公司也能写](professional-wechat-markdown-editor-nas-simple-deployment-doocs-md-ai-intelligent-layout-beautiful-graphic-content.md)
- [自建备忘录 Memos 飞牛 NAS 极简部署，Docker 一把梭随手记，内网穿透在外面照样翻](memos-self-hosted-notes-nas-docker-deployment-intranet-penetration-remote-access.md)
- [DailyTxT 加密日记：飞牛 NAS Docker 部署 + 内网穿透外网访问](private-diary-encryption-nas-local-deployment-dailytxt-intranet-penetration-remote-access.md)
- [轻量级Markdown笔记应用NoteFlow，本地部署无需数据库，支持内网穿透远程访问笔记管理系统](noteflow-lightweight-markdown-note-app-local-deployment-no-database-intranet-penetration-remote-access.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
