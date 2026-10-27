---
title: "Hoppscotch 接口测试台：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源接口测试工作台 Hoppscotch：选 GET 或 POST，填上自己服务的接口地址，点发送就能看到状态码、耗时和返回的 JSON；常用请求存进集合，下次点开就重发。用帕斯内网穿透拿公网地址，在外面用手机也能调接口。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/hoppscotch-nas-docker-api-testing-workbench-intranet-penetration
---

## 项目介绍

Hoppscotch 是一个开源的接口测试工作台。打开就是一个请求页：左边选 GET、POST 这些方法，中间填接口地址，点发送，下面立刻显示状态码、耗时、响应大小和返回内容；JSON 可以折叠展开，也能切到 Raw 和 Headers 看原始数据。

常用的请求可以起个名字、放进集合，下次点开直接重发。还可以建环境变量，把不同环境的地址和令牌分开存。整套东西是网页应用，装在 NAS 上以后，电脑和手机打开同一个地址就能用，不用每台机器都装客户端。

它是自托管的：请求从浏览器直接发到你自己的服务，界面和后端都跑在自己的机器上，不经过第三方的接口调试站点。

项目地址：[点我跳转](https://github.com/hoppscotch/hoppscotch)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Hoppscotch 用 Docker Compose 装起来。因为编排里有几处地址要填成公网地址，所以顺序是先建隧道、后部署。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3605（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 hoppscotch，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_USER: hoppscotch
      POSTGRES_PASSWORD: CHANGE-ME-DB-PASSWORD
      POSTGRES_DB: hoppscotch
    volumes:
      - pgdata:/var/lib/postgresql/data
  redis:
    image: redis:7-alpine
    restart: unless-stopped
  hoppscotch:
    image: hoppscotch/hoppscotch:latest
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "3605:80"
    environment:
      DATABASE_URL: postgresql://hoppscotch:CHANGE-ME-DB-PASSWORD@db:5432/hoppscotch?connect_timeout=300
      DATA_ENCRYPTION_KEY: "CHANGE-ME-32-CHAR-RANDOM-STRING-"
      WHITELISTED_ORIGINS: "http://YOUR-TUNNEL-ADDRESS"
      JWT_SECRET: CHANGE-ME-RANDOM-STRING
      TOKEN_SALT: CHANGE-ME-RANDOM-STRING
      ALLOW_SECURE_COOKIES: "false"
      ENABLE_SUBPATH_BASED_ACCESS: "true"
      HOPP_ALTERNATE_PORT: "80"
      VITE_BASE_URL: "http://YOUR-TUNNEL-ADDRESS"
      VITE_BACKEND_API_URL: "http://YOUR-TUNNEL-ADDRESS/backend/v1"
      VITE_ADMIN_URL: "http://YOUR-TUNNEL-ADDRESS/admin"
      VITE_BACKEND_GQL_URL: "http://YOUR-TUNNEL-ADDRESS/backend/graphql"
      VITE_BACKEND_WS_URL: "ws://YOUR-TUNNEL-ADDRESS/backend/graphql"
      REDIS_URL: redis://redis:6379

volumes:
  pgdata:
```

整段编排里有几处要改：

**数据库密码**：`POSTGRES\_PASSWORD` 和 `DATABASE\_URL` 里各有一处，两处要写成同一个值。

**DATA\_ENCRYPTION\_KEY**：换成刚好 32 位的随机字符串。长度不对（比如 31 位）后端会直接报 `ERR\_CRYPTO\_INVALID\_KEYLEN` 起不来。

**YOUR-TUNNEL-ADDRESS**：替换成第一步拿到的公网地址。出现的几处都要换：`WHITELISTED\_ORIGINS`、`VITE\_BASE\_URL`、`VITE\_BACKEND\_API\_URL`、`VITE\_ADMIN\_URL`、`VITE\_BACKEND\_GQL\_URL`，以及 `VITE\_BACKEND\_WS\_URL` 里 `ws://` 后面那一段。

端口 3605 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了；第一次启动会跑数据库迁移，等健康检查变成正常再打开。

**为什么要单端口：** 编排里开了 `ENABLE\_SUBPATH\_BASED\_ACCESS: true`，前端、后端 API 和后台都走同一个 80 端口，隧道只需要映射一个端口，不用为 API 再建一条。

### 三、第一次打开：直接就能发请求

用公网地址打开，看到的就是请求页，不需要先登录。左下角可以切 REST、GraphQL 等协议，第一次用保持 REST 就行。

右上角有「Login」和「Save My Workspace」两个入口，那是登录账号、把工作区同步到服务器的功能；不登录也能正常发请求、存集合，只是集合保存在当前浏览器里。

### 四、发第一个请求

**GET。** 方法选 GET，地址栏填一个自己的接口，比如你 NAS 上某个服务的健康检查地址，点 Send。下面会出现状态码、耗时、响应大小；JSON 标签页里可以逐层折叠，Headers 标签页里能看到完整的响应头。

**POST。** 方法切到 POST，地址换成要提交的接口，点 Body 标签，Content Type 选 application/json，在编辑框里贴一段 JSON，再点 Send。返回里能看到服务端收到的字段，调试参数对不对很直观。

**Query 参数。** 地址里的 `?key=value` 会自动拆到 Parameters 标签页，也可以在那里逐行加参数，地址栏会同步更新。

### 五、把常用请求存成集合

调好一个请求，点右上角的 Save，起个名字，选一个集合位置；还没有集合就先新建一个（比如「家庭设备接口」）。保存后右侧 Collections 里就能看到，下次点开这条请求，地址、方法、Body 都还在，点 Send 就重发一次。

多个相关请求放进同一个集合，换项目的时候整套切换，比在聊天记录里翻地址省事。

### 几点说明

**集合存在哪。** 不登录时，Personal Workspace 里的集合保存在当前浏览器的本地存储里，换一台设备或换一个浏览器就看不到；要跨设备同步需要登录账号，这篇不展开。调试请求本身是浏览器直接发出去的，不经过第三方站点。

**History 不可用。** 社区版默认把 History 关了，点历史会提示由管理员启用；本篇用「存集合」代替历史记录，够用。

**关于 VITE\_ 地址。** 这几项在启动时写进前端页面，用来决定 API、GraphQL 和后台的地址。公网地址变了（比如重建隧道换了地址），要把编排里的地址一起改掉再重启项目，否则页面能打开但后端请求会打到旧地址。

**数据在哪。** 数据库在 `pgdata` 卷里，备份这个卷就够了；Redis 只做进程间消息，不需要备份。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Hoppscotch 本身不需要 HTTPS 才能发请求；但公网地址等于把接口测试台开放到外面，别到处发，用完了可以在插件里把隧道关掉。

---

原文：<https://www.passnat.com/posts/hoppscotch-nas-docker-api-testing-workbench-intranet-penetration>
