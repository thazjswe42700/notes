---
title: "青龙定时任务面板：飞牛 NAS Docker 部署，自己写的小脚本每天准点跑，在外面也能看日志"
date: 2026-09-23
description: "在飞牛 NAS 上用 Docker Compose 部署青龙定时任务面板，自己写的小脚本（每天查一遍家里服务通不通）交给它准点跑，日志都留着；配上内网穿透，人在外面也能打开面板看今天跑没跑成功、手动再跑一次。"
canonical: https://www.passnat.com/posts/qinglong-scheduled-tasks-panel-nas-docker-intranet-penetration
---

## 项目介绍

青龙是一个定时任务管理面板，Python、JavaScript、Shell、TypeScript 脚本都能放进去跑。在网页里写好脚本、设好时间，它就按点自己执行，每一次运行的日志都给你留着。

适合拿来跑那些「每天、每小时要做一遍」的小事：查一下家里的服务还通不通、定时备份一个目录、定时抓一个网页看有没有变化。脚本是你自己写的，放在自己的 NAS 上跑，不用一直开着电脑。

项目地址：[whyour/qinglong](https://github.com/whyour/qinglong)

## 部署安装

装在 NAS 上之后，面板默认只有家里的网络能打开。可「脚本昨晚跑成功没有」这种事，往往是人在外面才想起来看。家里没有公网 IP 的话，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，飞牛的应用中心里就能直接装到插件。具体的操作步骤可以参考 [这份全平台上手指南](https://www.passnat.com/posts/p)。下面这套流程在飞牛 NAS 上从头到尾走一遍。

打开飞牛的 Docker 应用，切到上面的 Compose 标签，点新增项目，项目名填 qinglong。

路径这一栏点右边的文件夹图标，在自己的文件夹里挑一个空目录选中。

来源选「创建 docker-compose 文件」，把下面这段整个贴进去：

```yaml
services:
  qinglong:
    image: whyour/qinglong:latest
    container_name: qinglong
    restart: unless-stopped
    ports:
      - "5700:5700"
    volumes:
      - ./data:/ql/data
    environment:
      TZ: Asia/Shanghai
```

这份编排要认三处：

第一，ports 左边的 5700 是 NAS 上对外的端口，浏览器打开和建隧道都填它；右边是容器里的，不要改。

第二，volumes 的 ./data 是数据目录，脚本、定时任务、运行日志全在里面。这一行千万别省，省了删一次容器就全没了；备份就是把这个 data 文件夹整个拷走。

第三，环境变量里的时区设成 Asia/Shanghai，定时任务才会按北京时间跑。不设的话，你写的「每天早上 8 点」可能是另一个时区的 8 点。

镜像用的是官方的 whyour/qinglong，跟着上游的 latest 走。最后勾上「创建项目后立即启动」，点确认，镜像拉完它自己就跑起来了。

### 配上内网穿透：建好先别通电

容器跑起来之后紧接着把隧道建上。在飞牛的应用中心里搜 PassNAT 装上，打开插件，账号登一次。点创建隧道：名字随便起，上面几项留默认，本地地址那一栏留它默认的不要动，端口填 5700。

🔴 建好之后\*\*先别按电源\*\*。青龙第一次打开会进初始化，\*\*谁先打开谁就是管理员\*\*。隧道一通电，这个地址全世界都能访问到 —— 所以要么先在家里把管理员建好再通电，要么通电之后马上把管理员建好，别让这个空档留着。

公网地址在建好隧道的那一刻就已经分好了（点开隧道详情就能看到），先记下来。

### 第一次打开：初始化

用局域网地址（NAS 的 IP 加 :5700）打开，它会直接进初始化：

第一步「欢迎使用」，点开始安装。

第二步「通知设置」，想让任务失败时推到手机可以在这里选一种通知方式；不急的话先点跳过，以后在系统设置里再配。

第三步「账户设置」，填用户名和密码。密码不能是 admin，\*\*越长越好\*\* —— 这个面板能在你的 NAS 上跑任意脚本，后台被别人拿到就等于 NAS 被别人拿到。

提交之后管理员就建好了。这时候再回插件里按一下电源，状态转成运行中，那一行就是你的公网地址。

### 写一个脚本，让它每天准点跑

拿公网地址打开、登进去。下面用一个最实用的例子：每天查一遍家里几个服务还通不通。

左边菜单点「脚本管理」，新建一个脚本，名字叫 check\_home.sh，把下面这段贴进去保存：

```
#!/usr/bin/env bash
# 每天查一遍家里几个服务还通不通
NAS=你的NAS局域网IP   # 换成你 NAS 的局域网 IP

check() {
  code=$(curl -s -o /dev/null -m 5 -w "%{http_code}" "$2")
  if [ "$code" = "000" ]; then
    echo "❌ $1 不通"
  else
    echo "✅ $1 正常（$code）"
  fi
}

check "飞牛后台" "http://$NAS:5666"
check "青龙面板" "http://$NAS:5700"
check "博客"     "http://$NAS:3406"
```

NAS= 那一行换成你自己 NAS 的局域网 IP，下面三行 check 换成你自己想盯的服务（名字随便起，后面是地址和端口）。脚本跑在 NAS 上，所以查的是 NAS 自己看出去通不通，跟你手机在哪儿没关系。

然后到「定时任务」，点创建任务：名称随便写，命令填 task check\_home.sh，定时规则填 0 8 \* \* \*，意思是每天早上 8 点跑一次。保存之后它就会按点执行。

不想等到第二天，可以在任务那一行点运行，马上跑一次；再点日志，哪个服务正常、哪个不通，一行一行都写在里面。人在外面的时候，用公网地址打开面板，看的就是这一页。

### 两个要说清楚的地方

第一，青龙能跑任意脚本，这是它好用的地方，也是要小心的地方：只跑你自己看得懂的脚本，别把来路不明的脚本仓库整个拉进来。

第二，数据全在 data 目录里：脚本、任务、日志、数据库都在。换机器或者重装，把这个目录拷过去，编排一样，任务就都回来了。

本篇教程结束。

---

原文：<https://www.passnat.com/posts/qinglong-scheduled-tasks-panel-nas-docker-intranet-penetration>


## 相关文章

- [Cronicle Edge 定时任务：NAS Docker 部署 + 内网穿透](cronicle-edge-nas-docker-scheduled-tasks-web-ui-job-logs-intranet-penetration.md)
- [Portainer容器管理面板部署指南，飞牛NAS用Docker搭建可视化面板，配内网穿透在外面也能管容器](portainer-nas-docker-container-management-panel-deployment-guide.md)
- [自建推送通知 Gotify：飞牛 NAS Docker 部署，家里的脚本有事就发一条，配内网穿透在外面也能收](gotify-self-hosted-push-notification-deployment-guide.md)
