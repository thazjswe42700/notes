---
title: "自建开源协作编辑平台Etherpad，NAS极简部署，数据完全掌控，内网穿透远程访问"
date: 2025-10-16
description: "在飞牛 NAS 部署 Etherpad，通过 PassNAT 公网协作文档，附 SQLite 编排、口令设置、历史回放和导出步骤。"
canonical: https://www.passnat.com/posts/etherpad-nas-deployment-guide
---

## 这次搭一个什么

Etherpad 是实时协作编辑器。周末出游清单、聚会安排和共同草稿，可以让两个人同时写，新增的文字会同步出现，作者颜色便于分辨。文档保存在自己的 NAS 上。

## 在飞牛上部署

[帕斯内网穿透](https://passnat.com)把 NAS 上的协作文档开放到公网，家人用浏览器也能一起写。

先在飞牛的文件管理中，到我的文件下准备一个空文件夹。打开 Docker 的 Compose 页面，点新增项目，项目名填 etherpad，路径选刚才的文件夹，来源选创建 docker-compose.yml，把下面编排整段粘贴进去。

```yaml
services:
  etherpad:
    image: etherpad/etherpad:latest
    user: "0:0"
    network_mode: bridge
    ports:
      - "3497:9001"
    environment:
      DB_TYPE: sqlite
      DB_FILENAME: /opt/etherpad-lite/var/etherpad.sq3
      TITLE: 家庭协作文档
      DEFAULT_PAD_TEXT: 开始一起写吧
      PAD_OPTIONS_LANG: zh-hans
      REQUIRE_AUTHENTICATION: "true"
      USER_PASSWORD: 换成你自己的文档口令
      TRUST_PROXY: "false"
    volumes:
      - ./data:/opt/etherpad-lite/var
    restart: unless-stopped
```

3497 是 NAS 的访问端口，9001 是容器内端口。DB\_TYPE 设置为 sqlite，数据库文件保存在 data 目录。USER\_PASSWORD 这一行必须换成你自己的文档口令，之后访问文档时使用用户名 user 和这份口令。请保留 REQUIRE\_AUTHENTICATION 为 true，TRUST\_PROXY 为 false。

./data 是相对项目目录的数据文件夹。保留这个目录，就保留了文档数据库。勾选创建项目后立即启动，点确认，等待镜像拉取并启动。

## 通过飞牛插件开放到公网

接着进入飞牛自带的应用中心，搜索 PassNAT 内网穿透插件，安装并打开。点创建隧道，名字可以填 etherpad，接入节点与协议留默认，本地地址保留 127.0.0.1，本地端口填 3497，提交。

在本期隧道这一行点左边的电源键，等绿点亮起。用映射出来的公网地址访问 Etherpad，浏览器会先询问文档口令；用户名填 user，口令填编排里你自己设置的那份。

## 建文档和家人一起写

首页输入新的记事本名称，例如周末出游，再点按名称打开记事本。进入编辑器后写下清单。首次创建时会出现一次文档删除令牌，请按页面提示自行保存在私密位置，不要贴在文档里。

把当前这份文档的地址和访问口令私下给家人。对方打开同一个文档后，各自写一行：例如我带相机，家人带雨伞。双方不用刷新就能看到对方写的内容。演示使用两个独立浏览器会话，验证过实际同步。

## 回看历史与带走结果

编辑器右上方的时间轴按钮会打开历史回放。拖动时间滑块，可回看旧版本；再返回编辑模式继续写。打开导入/导出面板，选纯文本，就能下载当前文档。

本教程使用官方 etherpad/etherpad:latest 镜像，不固定主程序版本；镜像、目录和端口与本期视频一致。默认数据采用 SQLite，不需要再部署单独的数据库服务。

---

原文：<https://www.passnat.com/posts/etherpad-nas-deployment-guide>
