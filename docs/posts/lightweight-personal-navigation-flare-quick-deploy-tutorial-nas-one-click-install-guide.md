---
title: "轻量级个人导航页面 Flare 快速部署教程，飞牛 NAS Docker 部署 + 内网穿透远程访问"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker 自建导航页 Flare：常用网站和 NAS 服务收进一个入口，顶上直接搜；没有数据库，配置就是三个 yaml。再用帕斯内网穿透把它开放到公网，人在外面掏出手机也是同一个页面，还能直接加书签。"
canonical: https://www.passnat.com/posts/lightweight-personal-navigation-flare-quick-deploy-tutorial-nas-one-click-install-guide
---

## 项目介绍

Flare 是一个自己搭的导航页。把常用的网站和 NAS 上跑的服务登记进去，之后开浏览器只要开这一个页面：上面一排是常用应用，下面按分类摆书签，顶上还能直接搜。书签攒多了以后，搜比翻快得多 —— 顶上敲两个字回车，下面就只剩对得上的那几条。

它没有数据库，配置就是数据目录里的三个 yaml 文件 —— apps.yml 放应用、bookmarks.yml 放书签、config.yml 放主题和界面设置，三个加起来三千多字节。想备份就直接把这个目录拷走，想改也可以在网页里改。

镜像压缩后 9.4 MB，跑起来实测占三十兆出头内存，闲着的时候 CPU 基本不动，放在 NAS 上长期开着没什么负担。

项目地址：[点我跳转](https://github.com/soulteary/flare)

## 部署安装

导航页这东西，人在外面的时候才最想打开 —— 想起某个服务的地址，摸出手机就能查。装在内网出了门就够不着，那它一多半用处就没了。我家是移动宽带，没有公网 IP，所以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件，不用有公网 IP，也不用折腾路由器。

### 第一步：准备编排文件

一共就九行，整段复制走。

```yaml
services:
  flare:
    image: soulteary/flare:latest
    container_name: flare
    restart: unless-stopped
    ports:
      - "2225:5005"
    volumes:
      - ./data:/app
```

要认的就三处。第一行是镜像，用作者自己发布的那个。端口那行左边的 2225 是宿主机端口，也就是你等下在浏览器里敲的那个号，被别的服务占了就换一个；右边的 5005 是容器里的端口，不要改。最后一行把容器里的数据目录挂到项目文件夹下面，三个 yaml 配置就落在那儿，以后备份直接拷这个目录。

### 第二步：在飞牛上建 Compose 项目

先开飞牛的 Docker，进 Compose，点新增项目，名字就叫 flare。

路径这儿点一下，找个地方新建一个空文件夹，建好再选中它。编排和配置以后都放这儿 —— 一个项目一个文件夹。

来源选「创建 docker-compose.yml」，把上面那段编排贴进去，勾上「创建项目后立即启动」，确认。镜像才九兆多，十来秒就跑起来了。记住 2225 这个端口，下一步建隧道要填它。

### 第三步：用内网穿透开放到公网

容器跑起来之后，下一步就是把它开放到公网。去飞牛的应用中心，搜 PassNAT，装上，账号登一次就行。

打开插件，直接在里面创建隧道：名字随便起；传输协议选 TCP；本地 IP 这一栏留默认的 127.0.0.1，不用去查自己 NAS 的局域网地址；本地端口填刚才编排里那个宿主机端口 2225。

建好之后按一下电源让它上线，详情里会给出一个公网地址 —— 记下这个地址，在外面用它就能打开同一个页面。

## 配置：三个 yaml

数据目录下面会自动生成 apps.yml、bookmarks.yml 和 config.yml。前两个的写法是一样的：categories 是分类，links 是条目，每条填名字、地址、图标，应用还可以多写一句描述。像这样：

```
categories:
    - id: "1"
      title: 常用
links:
    - name: 相册
      link: https://photos.example.com
      icon: imageMultiple
      desc: 手机拍的自动传回来
      category: "1"
```

图标名用的是 Material Design Icons 那一套的驼峰写法，比如文件夹是 folderOutline、路由器是 routerWireless，名字写错了那一条就没图标。

不想手写文件也行 —— 页面左下角有个齿轮进设置，旁边还有个在线编辑器，在网页里像填表格一样改完保存，它会自己把 yaml 写回去。这一点在外面的时候特别顺手：想起一个网站，掏出手机在公网地址上直接加一条，填个名字和地址，保存完刷新一下，新书签就在了 —— 不用回家。

## 一些使用上的细节

三个 yaml 都在项目文件夹的数据目录里，是普通文本文件。想搬到另一台机器，把整个文件夹拷过去、用同样的编排起一个容器，打开还是原来那些书签。

不用的时候，回 PassNAT 插件里把电源关掉，公网入口就没了，需要的时候再打开，地址还是原来那个，随时可控。

---

原文：<https://www.passnat.com/posts/lightweight-personal-navigation-flare-quick-deploy-tutorial-nas-one-click-install-guide>


## 相关文章

- [Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问](homepage-nas-service-dashboard-docker-deployment-intranet-penetration.md)
- [ReDoc 接口文档：NAS Docker 部署 + 内网穿透](redoc-api-docs-nas-docker-openapi-intranet-penetration.md)
- [极简网页仪表盘SimpleDash，本地快速部署，内网穿透远程访问，YAML配置自定义链接管理](simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config.md)
- [Swagger Editor：NAS Docker 部署 + 内网穿透](swagger-editor-nas-docker-openapi-api-docs-intranet-penetration.md)
- [自托管服务门户Easy Gate，NAS一键部署，内网穿透远程访问自建应用聚合平台](easy-gate-nas-deployment-intranet-penetration-remote-access.md)
