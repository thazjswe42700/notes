---
title: "webtrees：NAS 上的家谱网站，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "单容器把家族树和老照片收进 NAS，网页里展开树图；附编排、GEDCOM 导入、账号协作和备份说明。"
canonical: https://www.passnat.com/posts/webtrees-nas-docker-family-tree-gedcom-remote-access
---

## 项目介绍

webtrees 是一个开源的在线家谱程序：把家族成员、父母子女关系、出生去世和照片录进去，网页上就能看到一棵可以展开的家族树，还能按人查事件、按年份查日历。

它用的是家谱行业通用的 GEDCOM 格式，别的家谱软件导出的文件能直接导进来，录好的数据也能随时导出，不会被锁在一个网站里。

数据全在自己的 NAS 上，家里的事不必放到别人的服务器；亲戚要看、要补自己那一支，开个账号就能上来改。

项目地址：[点我跳转](https://github.com/fisharebest/webtrees)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 Docker Compose 把服务装起来，再用 [帕斯内网穿透](https://passnat.com) 把它放出去，亲戚在自己家也能打开。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 webtrees，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  webtrees:
    image: nathanvaughn/webtrees:latest
    container_name: webtrees
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      - TZ=Asia/Shanghai
      - LANG=zh-Hans
      - BASE_URL=http://nas.local:8080
      - DB_TYPE=sqlite
      - DB_NAME=webtrees
      - WT_USER=admin
      - WT_NAME=管理员
      - WT_PASS=换成你自己的管理员密码
      - WT_EMAIL=admin@example.com
    volumes:
      - webtrees_data:/var/www/webtrees/data

volumes:
  webtrees_data:
```

这份编排用的是 **sqlite** 数据库：家谱数据就放在同一个数据卷里，一个容器跑起来就能用，不用另外配 MySQL。几百人的家谱完全够用；如果家族特别大，再换成镜像也支持的 MariaDB。

**BASE\_URL** 改成你实际要用的访问地址，比如局域网先访问就填 NAS 的地址和端口。它决定网页里生成的链接，装好以后再换地址，要到控制面板的网站配置里改「网站 URL」，或者直接改数据目录里的 config.ini.php。

**WT\_USER**、**WT\_PASS** 是第一次启动自动创建的管理员账号，密码改成你自己的；**WT\_EMAIL** 换成你的邮箱。

端口一行写的是 **8080:80**：冒号右边是容器里的端口，不要改；冒号左边是 NAS 上的端口，被占用的话换一个数字，后面隧道填同一个。

第一次启动会自动建库、建管理员，大约半分钟，看到登录页就是好了。

### 二、第一次打开：建家谱、录人、导照片

浏览器打开你填的地址，用管理员账号登录。第一次先点「新建一个家谱」：家谱标题填「某氏家谱」这类名字，URL 填一个英文短名，保存。

接着录人。点「添加未链接的记录」可以一个人一个人加；如果以前用过别家谱软件，直接到「控制面板 → 家谱 → 导入」，选 GEDCOM 文件，一次把全家导进来最省事。

**关系是自动排的：** 每个人填上父母、配偶和子女，家族树和世系图就按这些关系画出来，不用另外排版。照片放在多媒体文件里，人物页会自动显示头像和相册。

录完回到「家谱」首页，统计表会列出族人总数、男女比例、最早出生、最长寿这些数字，树图在「图表」里，后代图和世系图各看一种角度。

### 三、放出去：给亲戚开账号

到这一步家谱还只能在局域网里打开。飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **8080**。

建好点开隧道、按一下电源，状态变成「运行中」，详情里那一串就是公网地址。把这个地址发给亲戚，他们在自己家、用手机就能打开。

要让亲戚自己补分支，到「控制面板 → 用户」给每人建一个账号，编辑权限选上这个家谱。他们登录后就能补自己那一支，改动直接进你的 NAS，不用来回传文件。

**在世的人默认是隐私的。** webtrees 对访客默认隐藏在世的人，只有登录用户按权限能看到，把地址发出去之前可以到「隐私」里按自己的习惯调。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 几点说明

**备份数据卷。** 数据库和全部照片都在 webtrees\_data 这个卷里，升级镜像之前把它复制一份，换机器时一起搬过去就能恢复。

**家谱大了再换数据库。** SQLite 对家用够省事；族人上万、多人同时编辑频繁时，把编排改成镜像支持的 MariaDB 会更稳。

**GEDCOM 是通用格式。** 录好的数据随时能从「导出」拿到 .ged 文件，换软件、留档都不受限制。

---

原文：<https://www.passnat.com/posts/webtrees-nas-docker-family-tree-gedcom-remote-access>


## 相关文章

- [Webtop：NAS 上的网页桌面，Docker 部署 + 内网穿透](webtop-nas-browser-linux-desktop-docker.md)
- [深度网络研究工具本地部署指南，NAS一键启动Deep Research Web UI，支持多AI提供商](deep-network-research-tool-local-deployment-guide-nas-one-click-launch-deep-research-web-ui-multi-ai-providers.md)
