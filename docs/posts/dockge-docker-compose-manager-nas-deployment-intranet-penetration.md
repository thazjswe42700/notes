---
title: "自建 Dockge 管理面板：飞牛 NAS Docker 部署，容器开关、日志、编排一页全管，内网穿透出门也能看"
date: 2026-09-08
description: "Dockge 是开源的 Docker Compose 管理面板，装在飞牛 NAS 上，容器的开关、实时日志、编排文件都收在一个页面里，每个 stack 还是普通的 docker-compose.yml。本文从零部署，并用内网穿透开放到公网，出门也能看一眼哪个服务挂了。"
canonical: https://www.passnat.com/posts/dockge-docker-compose-manager-nas-deployment-intranet-penetration
---

## 项目介绍

Dockge 是一个开源的 Docker Compose 管理面板，装在自己的 NAS 上。机器上跑着哪些服务、哪个在跑哪个停了、日志在刷什么、编排文件长什么样，都收在同一个页面里，不用一个个点进去看。

它最舒服的一点是不锁死你的文件：每个 stack 就是一个普通的 docker-compose.yml，放在你指定的目录里，哪天不想用这个面板了，文件照样能自己拿去跑。面板里改编排有语法高亮，改完点部署就生效；还能把一条 docker run 命令直接转成 compose 格式。

项目地址：[点我跳转](https://github.com/louislam/dockge)

## 部署安装

装在内网之后，出了门就看不到了。想在外面也能看一眼哪个服务挂了、顺手重启一下，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

我家是移动宽带，没有公网 IP，拿公网 IP 也没那么简单，所以这一步用穿透最省事。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 dockge；路径挑一个空文件夹。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  dockge:
    image: louislam/dockge:1
    container_name: dockge
    ports:
      # 左边 5001 是 NAS 上对外的端口，浏览器敲它；右边 5001 是容器里的
      - 5001:5001
    volumes:
      # 这一行是关键：把 Docker 的接口交给它，它才管得到 NAS 上的容器
      - /var/run/docker.sock:/var/run/docker.sock
      # Dockge 自己的账号和设置存这儿
      - ./data:/app/data
      # 编排文件放这儿。左右两边必须写成同一个路径，写错了文件会跑到别的地方
      - /opt/stacks:/opt/stacks
    environment:
      # 告诉 Dockge 上面那个目录在哪，跟上面那行右边保持一致
      - DOCKGE_STACKS_DIR=/opt/stacks
    restart: unless-stopped
```

从上往下几处值得说一句。镜像 louislam/dockge:1 是上游 README 和官方 compose.yaml 的原文写法，后面那个 1 是作者维护的大版本线，照抄就行，不用自己去翻版本号。端口 5001，一会儿浏览器里敲的就是它；要是这个端口在你机器上被占了，改左边那个数字。

volumes 里第一行 /var/run/docker.sock 是关键：把 Docker 的接口交给它，它才管得到这台机器上的容器。第二行 ./data 是面板自己的账号和设置。

第三行 /opt/stacks 是编排文件放哪儿 —— 上游在这里专门写了警告：左右两边必须写成同一个绝对路径，而且下面那个环境变量 DOCKGE\_STACKS\_DIR 要跟它一致。写岔了的话，你在面板里建的 stack 会落到你没想到的地方。

勾上「创建项目后立即启动」，点确认。镜像不大，拉完容器起来就能访问了。

### 二、用内网穿透开放到公网

容器跑起来之后，直接把它开放到公网，后面建账号、建 stack 都在公网地址上做。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个，接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址指的就是这台机器。本地端口填 5001，跟编排里左边那个数字保持一致。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。

### 三、在公网地址上建管理员、建第一个 stack

浏览器打开刚才那个公网地址，第一次进来是「创建你的管理员账号」，填用户名和口令，点创建。

这一步别拖。Dockge 手里握着这台机器的 Docker 接口，而首次打开时它是没有主人的 —— 谁先访问谁就成了管理员。所以隧道一开，第一件事就是把管理员建好，口令用长的随机串。

进去之后左边是这台机器上的 stack 列表，上面有搜索框，服务多了可以直接过滤。点「+ Compose」新建一个：起个名字，把编排贴进右边的编辑器，点部署，它就在 /opt/stacks 下建好目录和 docker-compose.yml 并拉起来。

跑起来之后点进这个 stack：上面是每个容器的状态，中间是实时日志，右边是停止和重启。人在外面想看一眼哪个服务挂了、顺手重启一下，手机上打开这个公网地址就行。

## 几个提醒

一、面板里能看到的是这台机器上所有的 Compose 项目，不只是你在面板里建的那些 —— 别人建的、或者你在飞牛自带的 Compose 页里建的，它也会列出来。别手滑点到不该动的那个。

二、公网上开着的是一个能管你全机容器的面板，管理员口令一定用长的随机密码，别用生日、手机号这种。平时不用的时候，也可以在 PassNAT 插件里把这条隧道的电源关掉，要用再打开。

三、编排里 /opt/stacks 那两处路径改了的话，环境变量 DOCKGE\_STACKS\_DIR 要跟着一起改，两边始终一致。

---

原文：<https://www.passnat.com/posts/dockge-docker-compose-manager-nas-deployment-intranet-penetration>


## 相关文章

- [Chibisafe 文件上传站：NAS Docker 部署 + 内网穿透](chibisafe-nas-docker-file-upload-share-link-intranet-penetration.md)
- [自建 Dockhand 容器面板：飞牛 NAS Docker 部署 + 内网穿透](dockhand-docker-management-panel-nas-deployment-intranet-penetration.md)
- [Portainer容器管理面板部署指南，飞牛NAS用Docker搭建可视化面板，配内网穿透在外面也能管容器](portainer-nas-docker-container-management-panel-deployment-guide.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [DweebUI容器管理面板轻松上手，Docker Podman全能卫士一键部署运维](dweebui-container-management-panel-docker-podman-deployment.md)
