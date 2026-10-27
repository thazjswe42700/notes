---
title: "自建卡片笔记 Blinko：飞牛 NAS Docker 部署，随手记 + 标签 + 全文搜，出门也能用"
date: 2025-10-16
description: "在飞牛 NAS 上用 Docker 部署开源卡片笔记 Blinko：随手记、标签、全文搜，数据落在自己的目录里，再用内网穿透在外网访问。"
canonical: https://www.passnat.com/posts/ai-powered-card-note-blinko-self-hosted-deployment-guide-docker-one-click-launch-private-intelligent-note-taking-system
---

## 项目介绍

Blinko 是一个开源的卡片式笔记应用，专门给「想到什么先扔进去，回头再找得到」这件事用的。它把记下来的东西分成两类：闪念是随手一句就发、不用起标题不用挑分类；笔记是整理过、会回头翻的那种。两者在同一面卡片墙上，左边的标签树跟着正文里的井号自己长出来。

它自托管，数据都在你自己的机器上。装在 NAS 上之后，手机、电脑打开同一个地址就是同一份笔记，不用把想法交给别人的服务器保管。项目在 GitHub 上开源（GPL-3.0），并且提供了打好的官方镜像，NAS 上拉下来就能跑，不用自己构建。

项目地址：[blinko-space/blinko](https://github.com/blinko-space/blinko)，官方文档：[docs.blinko.space](https://docs.blinko.space/en/install)。

## 部署安装

装在自己的 NAS 上之后，只能在家里的网络访问。想在外面也能打开，家里又没有公网 IP，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 飞牛的应用中心里就能直接装到插件，不用另外找工具。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

下面这份编排可以直接用，三处需要你按自己的情况改：宿主端口、签名用的密钥，以及数据放在哪个目录。

```yaml
services:
  blinko:
    image: blinkospace/blinko:latest
    container_name: blinko
    environment:
      NODE_ENV: production
      TZ: Asia/Shanghai
      # 这串是给登录凭证签名用的，换成你自己随手打的一串，别照抄我的
      NEXTAUTH_SECRET: 换成你自己的一串随机字符
      # 连下面那个 postgres，用户名密码库名跟 postgres 那三行对上就行
      DATABASE_URL: postgresql://postgres:mysecretpassword@postgres:5432/postgres
    ports:
      # 左边 3111 是 NAS 上对外的端口，浏览器敲它；右边 1111 是容器里的
      - "3111:1111"
    volumes:
      # 笔记里的图片、附件落在 NAS 的这个目录里，备份直接把文件夹拷走
      - /vol1/1000/blinko/files:/app/.blinko
    depends_on:
      postgres:
        condition: service_healthy
    restart: always
  postgres:
    image: postgres:14
    container_name: blinko-postgres
    environment:
      POSTGRES_DB: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: mysecretpassword
      TZ: Asia/Shanghai
    volumes:
      # 笔记正文在数据库里，也落在同一个目录下
      - /vol1/1000/blinko/db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres", "-d", "postgres"]
      interval: 5s
      timeout: 10s
      retries: 5
    restart: always
```

端口：冒号左边的 3111 是待会儿在浏览器里输的宿主端口，右边的 1111 是容器内部的端口，不要动。3111 被别的服务占了就换一个，右边那个别跟着改。

NEXTAUTH\_SECRET：给登录凭证签名用的，随手敲一串没规律的字符就行，别照抄示例里的占位符。上游文档也专门提醒过这一条：一直用默认值是有风险的。

数据目录：两行挂载分别是笔记里的图片附件和数据库本体。/vol1/1000 就是飞牛「存储空间1 / 我的文件」的实际路径，所以上面这份编排会在「我的文件」下面建一个 blinko 文件夹，笔记全落在里面 —— 想备份直接把这个文件夹拷走就行。换成你自己的目录名也可以，两行一起改。

数据库这一段没有把 5432 发布到宿主机上，它只在这个项目内部可达，不会因为装了个笔记应用就在 NAS 上多开一个对外的数据库端口。

### 第二步：在飞牛的 Docker 里建项目

打开飞牛的 Docker，切到 Compose，点右上角的新增项目，项目名称填 blinko。路径那一栏点开，在「我的文件」下面新建一个空文件夹选中，这个文件夹以后就放这个项目的编排文件。

来源选「创建 docker-compose.yml」，把上面那段编排整段贴进去。勾上「创建项目后立即启动」，点创建。镜像拉完两个容器就跑起来了。

镜像标签用的是官方文档给的那个，不建议自己钉一个具体版本号 —— 钉死之后你就停在那一版了，上游修的东西也跟不上。

### 第三步：用内网穿透开放到公网

飞牛的应用中心里直接搜得到 PassNAT 插件，装上，账号登一次。打开插件点「创建隧道」：名字随便起，本地地址不用动（留空默认就是 127.0.0.1，插件本来就跑在 NAS 上），端口填刚才那个 3111。建好之后点开详情按一下电源，状态转成运行中，上面那个公网地址就是在外面要打开的。

### 第四步：第一次打开，先把管理员建好

用公网地址打开，第一次进来是登录页，下面有一行「需要创建账户？注册」。这里有一件事必须说清楚：Blinko 没有默认账号，也没有预置口令 —— 第一个注册的人就是这台实例的管理员。

所以隧道一开就赶紧把管理员注册掉，别开着放一晚上。管理员建好之后，注册入口会自动关上，别人再打开这个地址只会看到登录页。要不要放开别人注册，在「设置 → 基本信息 → 允许注册」里，默认是关着的。

## 用起来

主界面最上面就是输入框，打一句话，末尾加个井号跟上标签，按住 Ctrl 敲回车就发出去了，卡片当场出现在下面。标签不用提前建，写进正文里就有了；写成 #家里/冰箱 这样还能带层级，左边的标签树会自动分出子标签。

点左边的标签，卡片墙就只剩这个标签底下的内容。想找以前写过的哪句话，按 Ctrl + K 打开搜索，输一个词，命中的笔记会直接列出来。

左边还有「笔记」和「待办」两个视图：写成待办类型的条目会单独归到待办里，「统计」页能看到写了多少条、多少字、坚持了多少天，以及一年的记录热力图。

## 想接 AI 的话

Blinko 自带 AI 的位置，在「设置 → AI」里。按上游文档，模型提供方可以选 OpenAI、AzureOpenAI、Anthropic、DeepSeek、Gemini、Grok、Ollama、OpenRouter；另外还有一个可填的 API Endpoint 字段，用来接代理或者你自己部署的服务，也就是说兼容 OpenAI 协议的接口基本都能填进去。

想让模型也跑在自己机器上，上游文档有一篇专门讲怎么接 Ollama。不接 AI 也完全不影响用 —— 随手记、标签、全文搜这些都不需要模型。

## 项目总结

Blinko 适合的场景很具体：脑子里冒出来的东西先扔进去，不用当场想清楚放哪儿，靠标签和全文搜在需要的时候把它捞回来。装在自己的 NAS 上，再配上内网穿透，在外面用手机随手记一句，回家打开电脑就在那儿。

---

原文：<https://www.passnat.com/posts/ai-powered-card-note-blinko-self-hosted-deployment-guide-docker-one-click-launch-private-intelligent-note-taking-system>


## 相关文章

- [自建书签管理器Linkding极简部署指南，NAS内网穿透远程访问书签收藏库](linkding-bookmark-manager-nas-remote-access-guide.md)
- [Shlink 自建短链接：飞牛 NAS Docker 部署 + 内网穿透，公网建短链看点击统计](shlink-nas-docker-short-url-deployment-guide.md)
- [LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤](linkstack-nas-docker-personal-link-page.md)
- [自建私密书签导航站，NeonLink 轻量部署 Docker 一键启动，内网穿透外网访问](self-hosted-private-bookmark-navigation-neonlink-lightweight-docker-one-click-deployment-intranet-penetration-external-access.md)
