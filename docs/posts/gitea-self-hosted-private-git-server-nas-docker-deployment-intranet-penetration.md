---
title: "自建 Gitea 私有 Git 仓库：飞牛 NAS Docker 部署自己家的 GitHub，内网穿透在外面也能开"
date: 2026-09-07
description: "代码放在别人的平台上总有顾虑。Gitea 是开源的自建 Git 服务，装在飞牛 NAS 上就是自己家的 GitHub。本文用 Docker Compose 单容器部署 Gitea（内置 SQLite），再用内网穿透开放到公网，在外面也能 clone 和 push。"
canonical: https://www.passnat.com/posts/gitea-self-hosted-private-git-server-nas-docker-deployment-intranet-penetration
---

## 项目介绍

自己写的脚本、家里 NAS 的配置文件、一些不想公开的小项目，放在公共代码托管平台上总有点顾虑：要么担心隐私，要么免费额度和私有仓库数量受限。

Gitea 是一个开源、轻量的自建 Git 服务，用 Go 写的，一个二进制就能跑起来。它把 GitHub 那套核心功能都搬到了自己的机器上：代码仓库、提交历史、分支、工单（Issue）、合并请求、Wiki、发行版，还支持组织和团队。界面自带中文，装在 NAS 上一年到头也占不了多少资源。

项目地址：[点我跳转](https://github.com/go-gitea/gitea)

## 部署安装

Gitea 装在内网之后，出了门就 clone 不到、push 不上去了。想在外面也能用，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网，不需要公网 IP，也不用改路由器。

### 一、用 Docker Compose 部署

飞牛 NAS 打开 Docker 应用，切到 Compose 标签，点「新增项目」。项目名称填 gitea；路径选一个空文件夹，没有就在选择器里当场新建一个。

「来源」选「创建 docker-compose.yml」，把下面这段贴进去：

```yaml
services:
  gitea:
    image: gitea/gitea:1.27
    environment:
      - GITEA__database__DB_TYPE=sqlite3
    ports:
      - "3300:3000"
    volumes:
      - ./data:/data
    restart: always
```

从上往下要认四处：image 用的是官方镜像，带版本号 1.27，不用 latest，这样以后重建也是同一个版本；environment 里那一行告诉 Gitea 用内置的 SQLite 数据库，单容器就够了，不用再起一个数据库；ports 左边的 3300 是你浏览器要敲的端口，右边的 3000 是容器内部端口，别改；volumes 把仓库和数据库都放在项目目录下的 data 文件夹里，备份的时候拷这个文件夹就行。

勾上「创建项目后立即启动」，点确认。镜像压缩后七十多兆，拉起来很快。

### 二、用内网穿透开放到公网

容器跑起来之后，先不要急着在内网打开它——直接把它开放到公网，后面的初始化都在公网地址上做。

飞牛的应用中心里直接能搜到 PassNAT 插件，装上，用账号登录一次。

然后在插件里点「创建隧道」：隧道名称随便起一个（比如 gitea），接入节点和传输协议都留默认；「本地IP」这一栏留空不用填，默认就是 127.0.0.1，插件跑在 NAS 上，回环地址天然指向本机；本地端口填 3300。

建好之后回到隧道列表，点开这条隧道的详情，按一下最左边的电源图标，隧道就连上了，面板里会显示分配到的公网地址。

### 三、在公网地址上完成初始化

浏览器打开刚才那个公网地址，第一次进来是 Gitea 的安装向导。数据库类型已经是 SQLite3（编排里设好的），路径、仓库根目录、站点名称这些都保持默认就行。「Gitea 基础 URL」会自动填成你打开它用的公网地址——这个很关键，后面仓库页显示的克隆地址就是按它生成的。

往下拉，展开「管理员账号设置」，填一个用户名、邮箱和密码，点「安装 Gitea」。几秒钟后会自动登录，落到仪表盘。

提醒一句：隧道一开，安装向导就是对公网可见的，谁先打开谁就能建管理员。所以隧道启动之后马上把管理员建好，中间别放着。

### 四、建仓库、传文件

右上角的「+」，选「新建仓库」，起个名字，勾上「初始化仓库」（会自动生成一个 README），点「创建仓库」。

仓库页里点「添加文件 → 上传文件」，把文件拖进去，写一句提交说明，提交。仓库首页右上角的克隆地址就是公网的，在公司电脑上 git clone、git push 直接用这个地址。

## 几个提醒

一、公网上开着的是一个带登录的 Git 服务，建议装好之后进「站点管理 → 认证设置」把「允许注册」关掉，只留自己用。

二、如果以后重建隧道、公网端口变了，记得去 data/gitea/conf/app.ini 把 ROOT\_URL 改成新地址，否则克隆地址还是旧的。

三、这个编排没有映射 SSH 端口，日常用 HTTP 地址 clone / push 就够；需要 SSH 的话把容器的 22 端口再映射一个出来。

---

原文：<https://www.passnat.com/posts/gitea-self-hosted-private-git-server-nas-docker-deployment-intranet-penetration>


## 相关文章

- [轻量级Git容器镜像Alpine Docker，资源受限环境极简部署，无需安装Git直接运行](lightweight-git-alpine-docker-minimal-deployment-resource-constrained.md)
- [居家健身记录神器，GitHub风格可视化展示，Go语言轻量级应用，NAS一键部署Exercise Diary](exercise-diary-home-fitness-tracker-github-style-visualization-go-nas-deployment.md)
- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [自建在线表格 NocoDB：飞牛 NAS Docker 部署，Airtable 的开源替代，配内网穿透在外面也能打开自己的表](nocodb-one-click-deploy-airtable-open-source-alternative-nas-local-database-management.md)
- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
