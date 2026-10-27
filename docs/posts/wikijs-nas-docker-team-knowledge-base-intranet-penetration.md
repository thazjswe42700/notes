---
title: "Wiki.js 知识库：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源 wiki 系统 Wiki.js：配 PostgreSQL，第一次打开在向导里建管理员，页面按路径分目录，顶栏搜索打两个字就跳过去；用帕斯内网穿透拿公网地址，家里人手机上也能查。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/wikijs-nas-docker-team-knowledge-base-intranet-penetration
---

## 项目介绍

Wiki.js 是一个开源的 wiki 系统，用来把「大家都知道、但没人写下来」的东西整理成一个站：路由器怎么重启、净水器滤芯什么时候换、保修电话是多少、某个设备的设置步骤。页面用 Markdown 写，左边是目录树，顶上有一个搜索框，打几个字就能跳到对应的页面。

它跟记事本的区别在于「组织」：页面按路径分目录，比如 network/router-setup 会显示成 network 文件夹下的「路由器设置与重启」；同一个站可以开多级目录，谁都能按自己家的习惯分。界面有简体中文，账号分权限，访客默认只能看。

数据存在 PostgreSQL 里，页面、目录、历史版本都在数据库卷里，备份一个卷就够。

项目地址：[点我跳转](https://github.com/requarks/wiki)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Wiki.js 用 Docker Compose 装起来。Wiki.js 第一次打开时要在向导里填「站点地址」，这个地址决定页面里的链接怎么生成，所以先穿透、后部署，装的时候直接填公网地址。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3000（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来，下一步要填进安装向导。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 wikijs，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: postgres:15-alpine
    container_name: wikijs-db
    restart: unless-stopped
    environment:
      - POSTGRES_DB=wiki
      - POSTGRES_USER=wikijs
      - POSTGRES_PASSWORD=换成自己的数据库口令
    volumes:
      - wikijs_db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U wikijs -d wiki"]
      interval: 5s
      timeout: 5s
      retries: 20
  wiki:
    image: ghcr.io/requarks/wiki:2
    container_name: wikijs
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - DB_TYPE=postgres
      - DB_HOST=db
      - DB_PORT=5432
      - DB_USER=wikijs
      - DB_PASS=换成自己的数据库口令
      - DB_NAME=wiki
    depends_on:
      db:
        condition: service_healthy
volumes:
  wikijs_db:
```

整段编排里只有一处要改：两处数据库口令（db 里的 POSTGRES\_PASSWORD 和 wiki 里的 DB\_PASS）换成同一串自己的密码，越长越好。端口 3000 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。

勾上「创建项目后立即启动」。PostgreSQL 先起来，健康检查通过之后 Wiki.js 才开始连库，日志里出现「Database Connection Successful」就说明连上了。第一次启动会提示「DB Configuration is empty or incomplete. Switching to Setup mode...」，这是正常的，它在等你去装。

数据库放在具名卷 wikijs\_db 里，页面和目录都在这个卷里，备份它一个就够。

### 三、第一次打开：安装向导

用公网地址打开，看到的是 Wiki.js 的安装页。按顺序填：

**管理员邮箱**：以后登录用的账号。

**密码和确认密码**：至少 8 位。

**站点地址**：填刚才拿到的公网地址，结尾不要带斜杠。这一栏装完就写死了，决定页面链接怎么生成，填错要进后台改。

**遥测**：看自己愿不愿意，不影响使用。

点 INSTALL，等它建表、写初始数据，几秒到十几秒就好。装完会停在欢迎页，点「CREATE HOME PAGE」开始建首页——这一步需要登录，用刚才的邮箱和密码登录即可。

### 四、建首页和目录

点「CREATE HOME PAGE」之后先选编辑器，选 Markdown。然后在弹出的页面属性里填标题、简短说明和路径（首页路径默认 home），点 OK。左边写 Markdown，右边实时预览，写的就是你平时记笔记那套：标题、列表、引用、表格都支持。写完整页点右上角的 CREATE。

**目录靠路径分。** 再建页面时，路径写成 network/router-setup，它就会出现在左侧的 network 文件夹里；路径的第一段就是文件夹名，可以按「网络与设备 / 家居维护 / 生活常识」这样分几层。文件夹本身没有独立页面，点进去看到的是这一层的页面列表。

**页面标题和路径可以不一样。** 路径是给目录和链接用的，标题是页面上显示的大字，改标题不影响路径。

左侧点「Browse」能看到整个目录树：顶层是几个文件夹加首页，点进文件夹就是这一层的页面。

### 五、找东西：顶栏搜索

顶上的搜索框输入关键词，比如「滤芯」，下面立刻列出命中的页面：标题、简短说明、所在路径都在一起，点一下直接跳过去。回车会打开第一条结果。搜索索引是随页面保存自动更新的，新建或改完页面马上就能搜到。

这就是知识站比聊天记录好用的地方：不用翻半年前的对话，打两个字就出来。家里人问「滤芯什么时候换」，把公网地址发过去，自己搜就行。

### 几点说明

**数据在哪。** 页面、目录、历史版本都在 PostgreSQL 里，也就是 wikijs\_db 这个具名卷。备份这一个卷就够了；以后如果用到上传附件，再给 /wiki/assets 加一个卷。

**升级。** 镜像标签用的是 2，跟着 2.x 的小版本走。升级前先备份 wikijs\_db 卷，再到 Docker 里重新拉取镜像、重启项目，数据库结构会自己升级。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Wiki.js 本身不需要 HTTPS 就能用；但这是给自己家或团队看的站，公网地址别到处发，不用的时候可以在插件里把隧道关掉。

**账号和权限。** 安装时建的账号是管理员。以后可以在后台「用户」里加人，给只读或编辑权限；不登录的访客默认只能看，适合把地址发给家里人查资料。

---

原文：<https://www.passnat.com/posts/wikijs-nas-docker-team-knowledge-base-intranet-penetration>


## 相关文章

- [无数据库Wiki知识库系统，NAS极简部署DokuWiki，内网穿透远程访问个人文档平台](dokuwiki-nas-intranet-penetration-remote-access.md)
- [Mattermost 团队聊天：NAS Docker 部署 + 内网穿透](mattermost-team-chat-nas-docker-intranet-penetration.md)
- [SyncTV：NAS Docker 同步影院 + 内网穿透](synctv-nas-docker-synchronized-movie-theater-intranet-penetration.md)
- [Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透](mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration.md)
- [开源评论引擎Comentario一键部署指南，NAS轻松搭建私人讨论系统，Docker快速启动](open-source-comment-engine-comentario-deployment-guide-nas-private-discussion-system-docker-quick-start.md)
