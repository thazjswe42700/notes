---
title: "无数据库Wiki知识库系统，NAS极简部署DokuWiki，内网穿透远程访问个人文档平台"
date: 2025-10-16
description: "在 NAS 上用 Docker Compose 部署开源 Wiki 引擎 DokuWiki：不用数据库，页面就是文件；第一次打开设管理员，装完重启容器自动关掉安装入口；中文全文搜索开箱可用，家里说明书一台设备一页；用帕斯内网穿透拿公网地址，出门在手机也能查。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/dokuwiki-nas-intranet-penetration-remote-access
---

## 项目介绍

DokuWiki 是一个开源的 Wiki 引擎，用 PHP 写，不需要数据库：页面、历史版本、搜索索引全是普通文件，整个知识库就是一个目录，备份就是把它整个拷走。

它适合把家里散落的说明书整理成一个站：路由器、洗衣机、空调、门锁，一台设备一页，谁都能搜。页面里写标题、列表、链接，编辑器顶上有一排工具栏，不会语法也能排版。

界面有简体中文，中文全文搜索开箱就能用，打「滤网」「重启」都能直接落到对应的段落。

项目地址：[点我跳转](https://github.com/dokuwiki/dokuwiki)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 DokuWiki 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，家里人在外面也能查。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3714（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 dokuwiki，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  dokuwiki:
    image: linuxserver/dokuwiki:latest
    container_name: dokuwiki
    restart: unless-stopped
    ports:
      - "3714:80"
    environment:
      PUID: "1000"
      PGID: "1000"
      TZ: Asia/Shanghai
    volumes:
      - dokuwiki_config:/config

volumes:
  dokuwiki_config:
```

编排里只有两处按自己的情况改：端口 3714 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改；PUID 和 PGID 填 NAS 上你自己的用户 ID，一般留 1000 就能用。

勾上「创建项目后立即启动」，镜像下完就起来了。页面和配置都在 dokuwiki\_config 这个具名卷里，备份这一个卷就够了。

### 三、第一次打开：设置管理员

用公网地址打开，第一次会跳到安装工具，整页是中文的。维基名称填「家庭说明书」这类名字；超级用户、全名、邮箱、密码按自己的填，密码记好，以后改页面、装插件都用它。初始的 ACL 策略选「开放的维基」，家里谁都能读能写；想收成只读，装完在「管理 → ACL」里把匿名用户改成只读就行。

保存之后回到 Docker，把项目重启一次。LinuxServer 的镜像在重启时会自己把安装入口删掉，再访问安装页就是 404，不用自己进容器删文件。

装完在「管理 → 配置设置」里可以再改维基名字、默认语言（简体中文）、时区；想让搜索结果和面包屑显示页面标题而不是页名，把「使用标题」打开。

### 四、把说明书搬进去

**先搭首页。** 首页就是 start 页，用标题把设备分个组：网络、家电、其他；每条写成 [[network:router|路由器]] 这样的链接，竖线右边是显示出来的名字，点进去就是那一页。

**再建页面。** 在搜索框搜一个不存在的词，点「创建这个页面」，或者直接在地址后面打页名。页名用英文命名空间好管理，比如 network:router、appliance:ac；页面第一行写中文标题，打开以后显示的就是中文。

**写内容。** 编辑页就是 wiki 语法：等号包住标题、星号做列表、双中括号做链接，顶上工具栏也能点。保存后马上生效；改错了在「最近更改」里能看到每个历史版本，随时退回去。

### 五、搜索：中文直接搜

顶上搜索框打关键词就是全文搜索，中文不用装分词工具。实测搜「滤网」，首页的链接和空调页里的清洗步骤会一起出来，命中词在摘要里高亮；搜「重启」，直接落到路由器页的「怎么重启」。

家里人在手机上打开同一个公网地址，搜「洗衣机」就能看到日常洗涤程序，不用再翻抽屉找那本纸质说明书。

### 几点说明

**数据在哪。** 页面、历史版本、搜索索引都在 dokuwiki\_config 卷里，备份这一个卷就行；想搬到别的机器，把卷导出带走。

**升级。** 镜像标签用的是 latest，升级前先把卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**权限。** 安装时选的 ACL 决定谁能写。开放的维基谁都能改，适合家里人自己用；想收紧就到「管理 → ACL」里把匿名用户改成只读，只有管理员能编辑。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，DokuWiki 本身不需要 HTTPS 才能用；但里面记的是自己家的东西，公网地址别到处发，用完了可以在插件里把隧道关掉。

**页脚。** 默认模板的页脚会显示许可协议和 DokuWiki 的按钮，介意的话在「配置设置」里把许可协议改成「不要显示」。

---

原文：<https://www.passnat.com/posts/dokuwiki-nas-intranet-penetration-remote-access>


## 相关文章

- [Wiki.js 知识库：NAS Docker 部署 + 内网穿透](wikijs-nas-docker-team-knowledge-base-intranet-penetration.md)
- [个人知识库TiddlyWiki5 Docker极简部署NAS无公网内网穿透远程访问](personal-knowledge-base-tiddlywiki5-docker-deployment-nas-intranet-tunneling-remote-access.md)
- [Redmine开源项目管理系统Docker部署指南，NAS快速安装配置，内网穿透远程访问完整教程](redmine-docker-deployment-nas-installation-intranet-penetration-remote-access.md)
- [开源MediaWiki本地部署教程，Docker快速搭建个人维基百科系统，内网穿透远程访问](open-source-mediawiki-local-deployment-docker-personal-wiki-intranet-penetration.md)
- [开源私密知识库系统wikmd轻松部署 Docker容器化文件存储随处访问](open-source-private-knowledge-base-wikmd-docker-deployment.md)
