---
title: "自托管编程统计平台Wakapi快速部署指南，Docker Compose一键启动自己的数据私密服务"
date: 2025-10-16
description: "Wakapi 装在 NAS 上的完整步骤：单容器 Compose 怎么起、注册管理员、VS Code 插件怎么改上报地址、一周统计怎么看，以及用内网穿透在外面也能记。"
canonical: https://www.passnat.com/posts/wakapi-self-hosted-deployment-guide-docker-compose
---

## 项目介绍

Wakapi 是一个自托管的编程统计服务，兼容 WakaTime。你在编辑器里装一个插件，它会在你写代码的时候往后端发心跳：哪个项目、什么语言、哪个分支、什么时候保存了文件。Wakapi 把这些心跳汇总成每天、每周的图表——时间到底花在哪个项目上，一眼就能看出来。

跟用官方统计服务比，区别是 **数据存在自己家的 NAS 上**：不把数据交给第三方，也没有别人的服务端能看到你的项目名和编码时间。插件这边只需要把地址改成你自己的 Wakapi，用的还是同一套插件。

项目地址：[点我跳转](https://github.com/muety/wakapi)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。编排是一个容器 + 一个数据卷，默认用 SQLite，不用另外装数据库。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 wakapi，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  wakapi:
    image: ghcr.io/muety/wakapi:latest
    container_name: wakapi
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      WAKAPI_PASSWORD_SALT: 换成你自己的随机字符串
      WAKAPI_DB_TYPE: sqlite3
      WAKAPI_DB_NAME: /data/wakapi.db
      WAKAPI_ALLOW_SIGNUP: "true"
      TZ: Asia/Shanghai
    volumes:
      - wakapi_data:/data
volumes:
  wakapi_data:
```

几个地方照着改：**WAKAPI\_PASSWORD\_SALT** 换成一串你自己的随机字符，它用来保护登录信息，填好之后就不要再改。

端口一行写的是 **3000:3000**：冒号右边是容器里的端口，不用动；冒号左边是 NAS 上的端口，被占了就换一个数字，浏览器访问和后面建隧道都填这个数字。

数据存在名为 **wakapi\_data** 的具名卷里。这里不要改成绑定挂载到「我的文件」下的目录——实测容器写不进那个目录，启动日志会报打不开数据库文件，换回具名卷就正常。

第一次启动先让 **WAKAPI\_ALLOW\_SIGNUP** 保持 **"true"**，注册完自己的账号之后，可以把它改成 **"false"** 再重启一次，别人就不能再注册了。

### 二、第一次打开：注册管理员

浏览器打开 http://NAS的局域网地址:3000，首页点「Get Started」，进注册页。用户名、邮箱、密码填好——**第一个注册的用户自动是管理员**，后面的用户都是普通用户。

注册完用刚填的账号登录，会进入 Dashboard。刚装好还没有数据，页面会提示先接上编辑器。

### 三、让编辑器往这里发数据

以 VS Code 为例：在扩展商店装官方 WakaTime 插件，装好后它会要你填 API Key。回到 Wakapi 的「Settings → API Keys」标签，把这一页给你的 key 复制到插件里。

然后要改上报地址：在 VS Code 的设置里搜 wakatime，找到 API URL 一项，改成 **http://NAS的局域网地址:3000/api**。不装插件行不行？也可以用命令行工具 wakatime-cli 发心跳，原理一样，填的都是这个地址和这把 key。

接好之后正常写代码、保存文件，过几分钟回到 Dashboard 刷新，统计就出来了。心跳是插件真实产生的，不用手动录入。

### 四、看统计

Dashboard 默认显示今天。右上角可以选日期范围，切成一周，下面几张图就都按这一周算：

**顶部六块数字**：总时长、心跳数、花时间最多的项目、最常用的语言、编辑器、AI 编码比例；

**Projects**：横向条形图，看每个项目分到多少时间；

**Languages**：饼图，看 Python、Go、Shell 各占多少；

**Timeline**：按天的堆叠柱状图，看这一周哪天写得多；

**Hourly Breakdown**：按小时的热力格，看你习惯在什么时间写代码。

上面的筛选器可以只看某一个项目或者某一种语言。点进「Projects」页，还能按项目单独看趋势。

### 五、出门也能记：把上报地址放出去

上面的地址是局域网地址，只在家里连 WiFi 时能上报。带着笔记本出门，要让心跳也能发回来，就把它放出去：飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」——名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 3000，跟编排里冒号左边一致。

建好点开隧道、按一下电源，状态变成「运行中」，详情里那一串就是公网地址。

回到编辑器，把 WakaTime 插件的 API URL 改成 **http://公网地址:端口/api**，在外面写代码的心跳就也能记进来了。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**用数据卷，不要用绑定挂载。** 这是实测踩出来的：绑定挂载到 NAS 的普通目录时，容器启动会报「unable to open database file」，哪怕目录权限是全开也不行；具名卷从零起就正常。

**盐值定好别改。** WAKAPI\_PASSWORD\_SALT 是保护登录信息用的，改了之后已有账号和 key 会对不上，需要重新处理。

**注册开关。** 单人用就把 WAKAPI\_ALLOW\_SIGNUP 改成 false；要给家里几个人一起用就开着，第一个账号是管理员。

**备份数据卷。** 所有心跳、项目、语言统计都在 wakapi\_data 这个卷里，升级镜像前先备份它。

**界面是英文的。** 目前没有中文语言包，菜单就是 Dashboard、Projects、Settings 这几项，用两次就熟了。

**AI 编码比例。** 这一项要编辑器那边上报了 AI 相关的数据才有数，没接就是 0%，不影响其他统计。

---

原文：<https://www.passnat.com/posts/wakapi-self-hosted-deployment-guide-docker-compose>


## 相关文章

- [PHP动态二维码生成系统部署指南，Docker一键启动内网穿透远程访问管理平台](php-dynamic-qr-code-generator-docker-deployment-guide.md)
- [code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码](codeserver.md)
- [开源无代码数据库SeaTable自托管部署指南，NAS极简配置Docker Compose一键启动](seatable-self-hosted-nas-docker-compose-deployment-guide.md)
- [开源自托管订阅管理系统Wallos 内网穿透远程访问 NAS一键部署完整指南](open-source-self-hosted-subscription-management-system-wallos-internal-penetration-remote-access-nas-one-click-deployment-complete-guide.md)
- [自建私人API开发环境，Yaade开源协作平台Docker快速部署指南](yaade-open-source-api-development-docker-deployment-guide.md)
