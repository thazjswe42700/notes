---
title: "开源低代码工具ToolJet快速部署指南，NAS极简搭建内部应用开发平台"
date: 2025-10-16
description: "ToolJet 开源低代码后台：在 NAS 上用 Docker Compose 部署，连上数据源、拖表格和表单，就能把库存表变成内部小系统；自带示例应用含统计卡、搜索和新增表单，用帕斯内网穿透拿公网地址，手机在外也能查。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/open-source-lowcode-tooljet-quick-deployment-guide-nas-simple-setup-internal-app-development-platform
---

## 项目介绍

ToolJet 是一个开源的内部工具搭建平台：不用自己写前端页面和后端接口，连上数据库或者 API，再把表格、表单、按钮这些组件拖到画布上，一个能录数据、能查数据的小系统就出来了。适合设备报修、采购申请、库存清单这类只有自己人用的后台页面。

它自带一个示例应用：左边是查询列表，中间是应用画布，右边是组件库。示例应用本身是一个库存管理看板——四张统计卡（总量、有货、低库存、缺货）、一张商品表、搜索框和新增表单都在同一页，新增、修改、删除各对应一条查询。

本项目开源，可以部署在自己的 NAS 上，数据都在自己家里。

项目地址：[点我跳转](https://github.com/ToolJet/ToolJet)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。先把 ToolJet 用 Docker Compose 起起来，再用 [帕斯内网穿透](https://passnat.com) 给它一个公网地址，手机在外面也能打开。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 tooljet，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  tooljet:
    image: tooljet/try:latest
    container_name: tooljet
    restart: unless-stopped
    ports:
      - "8083:80"
    volumes:
      - tooljet_data:/var/lib/postgresql/13/main

volumes:
  tooljet_data:
```

这段编排只有一个服务，镜像、端口、数据卷三样就够：

**端口**：8083 是宿主端口，冒号右边 80 是容器里的端口，不要改。8083 被占用的话把冒号左边换一个。

**数据卷**：数据库文件挂在 tooljet\_data 卷里，容器删了重建，账号和应用都还在。备份这个卷就行。

**环境变量**：这个镜像不需要额外填任何环境变量，数据库和后台服务都在容器里自带。

勾上「创建项目后立即启动」。第一次启动要先初始化数据库，大约两三分钟，日志里出现「Ready to use at http://localhost」就装好了。这段时间页面打不开是正常的，等一会儿再刷新。

### 二、第一次打开：建管理员

浏览器打开 http://NAS地址:8083，会跳到 /setup。点「Set up ToolJet」，填名字、邮箱和密码，这个账号就是管理员——镜像里没有预置的默认账号，第一个注册的人就是管理员。

接着是四步引导：填公司名和准备拿 ToolJet 做什么、给工作区起名、第三步是一个功能推广页（选「No, thanks」跳过）、第四步会告诉你示例应用已经建好了。走完就进工作台。

如果安装时不想把引导走完，直接访问 /login 用刚才的邮箱密码登录，也会进到同一个工作区。

### 三、先看示例应用

工作台里已经有一个库存管理示例应用，点开就能用。它把一套内部小系统该有的东西都摆齐了：

**统计卡**：总量、有货、低库存、缺货四个数字，跟着表格里的查询结果实时变。

**表格**：每一行是一个商品，列有编号、名称、数量、价格、状态、分类、描述。表头带筛选，右上角有搜索框。

**搜索**：在搜索框里打一个词，表格只剩匹配的行，上面的统计卡也跟着重算。比如打「电阻」，总量和有货都变成 87，其余清零。

**新增表单**：点右上角的新增按钮弹出表单，填名称、数量、价格、状态、分类、描述，点确认，表格里立刻多一行。

**查询**：编辑器左边列着几条查询：查列表、新增、修改、删除，写法就是一段 SQL（也支持 JavaScript），表格和按钮绑的就是它们。

示例应用连的是官方自带的示例数据源，页面顶部也写着数据会随时间变化、每天重置，适合先熟悉操作，不适合当正式库。

### 四、自己搭一个

点「Create an app」新建一个空白应用，就进了编辑器：左边是查询列表，中间是画布，右边是组件库。把表格拖到画布上，新建一条查询（SQL 或者 JavaScript），在表格的数据属性里绑定这条查询的结果，数据就显示出来了。

要录数据，就再拖一个表单或者几个输入框和一个按钮，按钮绑定插入数据的查询；要筛选，表格自带搜索框和列筛选。做好的应用点右上角「Release」发布，之后在工作台点「Launch」就能像普通网页一样打开。

组件库里有表格、按钮、表单、文本输入、数字输入、日期选择、下拉框、开关、容器、弹窗、标签页这些，够拼出大多数内部页面。

### 五、穿透到公网：手机在外也能打开

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起，接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8083（就是编排里冒号左边的端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就是分配好的公网地址。手机用流量打开这个地址，登录同一个管理员账号，库存表、搜索、新增表单都能用。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**数据在哪。** 账号、应用、示例数据都在 tooljet\_data 卷里。升级或者搬家之前，先把这个卷备份一份。

**关于明文 HTTP。** 内网穿透给的是 http 地址，ToolJet 本身不要求 HTTPS 才能登录；但它是自己家的后台，公网地址别到处发，用完可以在插件里把隧道关掉。

**关于镜像版本。** 编排用的是官方 try 镜像，它把数据库和后台打包在一个容器里，适合自用和小团队；要拆成多容器、接外部数据库，按官方文档换成正式部署方式。

**首次启动慢。** 第一次要初始化数据库和示例应用，两三分钟属正常；以后重启一般几十秒。

---

原文：<https://www.passnat.com/posts/open-source-lowcode-tooljet-quick-deployment-guide-nas-simple-setup-internal-app-development-platform>


## 相关文章

- [开源低代码平台Budibase快速部署指南，Docker极简搭建应用，内网穿透远程访问](open-source-low-code-platform-budibase-quick-deployment-guide-docker-simple-setup-internal-network-penetration-remote-access.md)
- [开源无代码平台Joget，NAS极简部署，内网穿透远程访问，快速构建企业应用](open-source-no-code-platform-joget-nas-simple-deployment-intranet-penetration-remote-access-rapid-enterprise-applications.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [开源Dify平台极简部署，LLM应用开发工具链完整集成，支持内网穿透远程访问](dify-open-source-deployment-llm-application-development-tools.md)
- [自建开发者工具箱 it-tools：飞牛 NAS Docker 部署，86 个小工具一页全有，配内网穿透在外面也能用](it-tools-docker-intranet-penetration-remote-access.md)
