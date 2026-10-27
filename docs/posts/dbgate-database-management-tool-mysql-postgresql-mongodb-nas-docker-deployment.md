---
title: "翻数据库不用再开客户端：飞牛 NAS 用一个容器装 DbGate，浏览器里开表写 SQL，配内网穿透在外面也能连"
date: 2025-10-16
description: "在飞牛 NAS 上用一个容器部署 DbGate 网页版数据库管理台，浏览器里开表、改数据、写 SQL，配内网穿透在外面也能连。"
canonical: https://www.passnat.com/posts/dbgate-database-management-tool-mysql-postgresql-mongodb-nas-docker-deployment
---

## 项目介绍

DbGate 是一个跑在浏览器里的数据库管理台。开表看数据、改数据、写 SQL、导出，这些平时要装个客户端才能干的事，装在 NAS 上之后开个网页就能干。

它认的数据库种类很多，连接类型的下拉里有 MySQL、MariaDB、PostgreSQL、SQL Server、Oracle、SQLite、MongoDB、Redis、ClickHouse、DuckDB 这些，家用能碰到的基本都在里面。本文用 SQLite 做示例，因为它不需要另外再起一个数据库容器，把 .db 文件丢进一个文件夹就能连。

放在 NAS 上还有一个好处：数据库文件本来就在 NAS 上，管理台也在 NAS 上，两者之间是本机访问，不用把数据库端口暴露出来。

项目地址：[dbgate/dbgate](https://github.com/dbgate/dbgate)

本文所有内容都是在 7.3.0 上实测的。

## 部署安装

装好之后它默认只有家里的网络能打开。可「出门在外想查一眼库里的数据」恰恰是这东西最有用的场景，只能在家用就少了一半意义。家里没有公网 IP 的话，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件。具体操作可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

下面这份编排可以直接用，有两处要按自己的情况改：冒号左边的宿主端口，以及最后那两行登录信息。

```yaml
services:
  dbgate:
    # ⚠ 不要用 :alpine —— 它的 SQLite 驱动在 musl 上加载不了
    image: dbgate/dbgate:latest
    container_name: dbgate
    restart: unless-stopped
    ports:
      # 左边 3411 是 NAS 上对外的端口，浏览器敲它；右边 3000 是容器里的
      - "3411:3000"
    volumes:
      # 想看的数据库文件放进这个文件夹，容器里看到的路径就是 /db
      - ./db:/db
      # DbGate 自己的东西（你存的连接就记在这儿），整个文件夹拷走就是一份完整备份
      - ./data:/root/.dbgate
    environment:
      # 不填的话它记的时间按 UTC 算，跟本地差八小时
      TZ: Asia/Shanghai
      # 🔴 这两行不填，谁打开这个地址都是一整个完整界面，连上的库随便翻
      LOGIN: nasdb
      PASSWORD: change-this-please
```

镜像：这里特意写的是 latest，不要换成 alpine 那个标签。alpine 版的 SQLite 驱动在 musl 上加载不了，容器日志里会有一行 Error relocating better\_sqlite3.node: fcntl64: symbol not found，表现是网页一直停在 Preparing plugins 不动。麻烦的地方在于这时候端口是通的、HTTP 返回 200、NAS 的容器列表里也写着「正在运行」，光看面板完全看不出问题，只有翻容器日志才知道。换成 latest 之后同一台机器六秒就出界面。

端口：冒号左边的 3411 是待会儿在浏览器里输的宿主端口，也是建隧道时要填的那个；右边的 3000 是容器内部的端口，不要动。3411 如果被别的容器占了，换一个没用过的即可。

数据库文件目录：./db 这个文件夹是放你自己的数据库文件的地方，容器里看到的路径就是 /db。比如你把一个 mydata.db 放进这个文件夹，待会儿在 DbGate 里新建连接时，路径就填 /db/mydata.db。刚开始这个文件夹是空的，这是正常的。

配置目录：./data 里存的是 DbGate 自己的东西 —— 你保存的每一条连接都记在这儿。整个文件夹拷走就是一份完整备份。这里特意用路径挂载而不是存储卷，因为飞牛的 Docker 界面里没有管理存储卷的地方，用存储卷的话以后想清掉重来会很麻烦。

时区：TZ 填 Asia/Shanghai，不填的话它记的时间按 UTC 算，跟本地差八小时。

### 🔴 最后那两行：登录信息，不要留空

这是整份编排里最要紧的两行，也是本文跟很多教程不一样的地方。

DbGate 默认是没有登录这一关的 —— 不填 LOGIN 和 PASSWORD 的话，任何人打开这个地址看到的都是一整个完整的管理界面：你保存的连接在那儿摆着，点两下就能翻库里的数据，SQL 编辑器也是随便用的。在家里的网络上这没什么问题，可一旦把它开放到公网，等于把一个数据库管理台裸挂在外面。

填上这两行之后，打开这个地址先是一个登录页，输对了才进得去。编排里那个 change-this-please 是占位用的，一定换成你自己的口令 —— 编排文件是明文的，别把它当秘密，也别用你在别处用过的口令。

### 第二步：在飞牛上建 Compose 项目

打开飞牛的 Docker 应用，切到 Compose，点右上角的新增项目。项目名称填 dbgate。

路径这一栏点开，在「我的文件」下面挑一个空文件夹选中 —— 这个文件夹以后就是这个项目的家。

来源选「创建 docker-compose.yml」，把上面那份编排整段贴进去。

下面「创建项目后立即启动」勾上，点创建。镜像五百多兆，拉完一会儿就起来了。

在家里的网络上打开 http://你的NAS地址:3411，看到登录页就说明装好了。

### 第三步：用内网穿透开放到公网

在飞牛的应用中心里搜 PassNAT，装上，账号登一次。打开插件点创建隧道：名字随便起；传输协议选 HTTP；本地 IP 留空就好，默认就是 127.0.0.1，不用去查 NAS 的局域网地址；本地端口填上面那个 3411。

选 HTTP 是因为选了它之后表单上会多出「子域名」和一个顶级域名的下拉 —— 填一个没人用过的子域名、在下拉里挑一个顶级域名，拿到的公网地址就是一个网址，而不是一串 IP 加随机端口。子域名是全平台唯一的，填一个太常见的词多半会提示已存在，换一个就行。

### 🔴 隧道那两格「网页访问用户名 / 密码」请留空

这一条很反直觉，但请照做：给 DbGate 建隧道的时候，不要再在隧道上加一层网页访问密码，那两格留空。

原因是两边用的是同一个请求头。浏览器过完隧道那层密码之后，会把 Authorization 这个头挂在之后每一个请求上；而 DbGate 自己的接口也用这个头传它自己的登录令牌，拿到隧道那层的值之后解不出来，所有接口一律返回 401。

表现出来就是：公网地址打开之后页面一直停在 Starting DbGate，怎么等都不动，而同一个实例在局域网里一切正常。很容易误判成「隧道坏了」或者「这个应用不支持内网穿透」，实际上两样都没坏。

把口令留给 DbGate 自己（也就是编排里那两行）就没这个问题，而且只需要输一次密码，不用输两道。

## 怎么用

### 新建一条连接

进去之后点左上角工具条上的新建连接图标（那个图标没有文字，在搜索框右边）。

连接类型选 SQLite，下面那一栏填数据库文件的路径。注意这里填的是容器里的路径：你放进 ./db 文件夹的 mydata.db，在这儿要写成 /db/mydata.db。这一步填错的话会连到一个别的文件上（SQLite 连一个不存在的路径会新建一个空库），表现是连上了但一张表都没有 —— 看着像库坏了，其实是路径写错了。

点 Save 保存。注意保存之后左边不会马上把表列出来，还要再点一下那条连接，它才真去连、才把表展开出来。

连上之后左边会列出这个库里的表。双击任意一张表，右边就把数据整页铺开，可以直接翻、直接改，跟在电脑上开客户端看是一样的。

### 写 SQL

在左边那条连接上点右键，菜单里选 New query，右边就开出一个 SQL 编辑器。写完按 F5 或者点运行，结果直接显示在下面，底部还会告诉你返回了多少行。

多表连接、分组统计这些都能跑，跟你平时在客户端里写 SQL 没有区别。想把结果导出来的话，工具条上有 Export。

### 几件用之前值得知道的事

连接信息存在 ./data 里，换机器的时候把这个文件夹拷过去，所有连接都还在。

界面右上角有一个升级入口，指向它的商业版本。社区版本身该有的功能都有，本文用到的每一步都在社区版上跑通的。

如果你连的是 MySQL 或者 PostgreSQL 这类需要单独跑一个容器的数据库，主机那一栏填容器名或者 NAS 的局域网地址都行，但请不要为了让 DbGate 连上就把数据库端口也开到公网 —— 数据库自己的端口留在局域网里，只把 DbGate 这一个口子开出去就够了。

最后提醒一句：这东西连上之后是能改数据、也能删表的。开到公网之前先确认那两行登录信息填好了，口令也换成了你自己的。

---

原文：<https://www.passnat.com/posts/dbgate-database-management-tool-mysql-postgresql-mongodb-nas-docker-deployment>


## 相关文章

- [浏览器免费数据库设计工具DrawDB，无需账户在线绘制ER图生成SQL脚本](browser-free-database-design-tool-drawdb-er-diagram-sql-script.md)
- [Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透](mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration.md)
- [自然语言SQL对话工具，AI驱动数据库客户端，支持多数据库NAS快速部署](natural-language-sql-chat-ai-database-client-multi-database-nas-deployment.md)
- [SQLite Web数据库管理工具极简部署指南 NAS内网穿透在线查询编辑](sqlite-web-database-management-tool-minimal-deployment-guide-nas-intranet-penetration-online-query-editing.md)
- [Directus 开源数据管理平台 Docker 快速部署，NAS 自建内容管理系统无公网穿透远程访问！](directus-docker-nas-cms-remote-access.md)
