---
title: "LinkStack 自建个人名片页：NAS Docker 部署 + 公网访问完整步骤"
date: 2026-10-18
description: "单容器、SQLite、免数据库：Compose 怎么起、安装向导怎么填、注册和页脚怎么关、入口怎么加、在外面怎么打开。"
canonical: https://www.passnat.com/posts/linkstack-nas-docker-personal-link-page
---

## 项目介绍

LinkStack 是一个开源的个人名片页：一个地址打开，最上面是头像和一句介绍，下面是一排按钮 —— 项目、视频、教程、联系方式，点哪个去哪个。适合做自媒体的人、接私活的、或者只是想给朋友一个「我的入口都在这」的页面。

它跟 NAS 的服务导航不是一回事：Homepage、Homarr 那种是给自己看的面板；LinkStack 是给外面的人看的公开页面，按钮怎么排、什么颜色、头像换哪张，都在后台点几下就改完。

程序是 PHP 写的，默认用 SQLite，一个容器就够了，不需要另外装数据库。

项目地址：[点我跳转](https://github.com/LinkStackOrg/LinkStack)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。单容器，一份 Compose 编排就够了。

### 一、想在外面打开，先把 8080 放出去

只在局域网里自己看，这一步可以跳过，直接看第二步。要让别人在外面也能打开你的页面，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起，本地 IP 留默认的 127.0.0.1，本地端口填 **8080**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 linkstack，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  linkstack:
    image: linkstackorg/linkstack:latest
    container_name: linkstack
    restart: unless-stopped
    environment:
      TZ: Asia/Shanghai
      SERVER_ADMIN: admin@example.com
      PHP_MEMORY_LIMIT: 256M
      UPLOAD_MAX_FILESIZE: 8M
    ports:
      - "8080:80"
    volumes:
      - linkstack_data:/htdocs

volumes:
  linkstack_data:
```

容器里固定监听 80，冒号前面那个数字是 NAS 上的端口，被别的项目占了就改它，后面的不用动。

数据全部存在名为 **linkstack\_data** 的卷里，包括数据库、头像、主题和配置。不用绑定挂载 —— 容器里的 Apache 用的是普通用户，直接挂宿主机目录会因为写不进去而反复重启。

勾上「创建项目后立即启动」，点确认。镜像不到一百兆，容器状态变成「运行中」就可以打开网页了。

### 三、第一次打开：安装向导

浏览器打开 **http://NAS的局域网地址:8080**，第一次进来是安装向导，四步：

**选语言**：有简体中文，选完点下一步。

**依赖检查**：容器里该有的 PHP 模块都装好了，直接下一步。

**选数据库**：用默认的 SQLite。除非你本来就有 MySQL，不然不用改。

**建管理员**：填邮箱、密码、用户名和显示名。**这里的用户名就是你的页面地址**：用户名填 xiaolin，页面就是 **/@xiaolin**。

最后一步是页面配置：注册、邮件验证、要不要把个人页设成首页、应用名称。先按默认走完，装好以后再回来调。

### 四、装完先做两件事

**关掉注册。** 安装时「启用注册」默认是开的，这个页面是给自己用的，不需要别人注册账号。进「管理 → 配置 → 应用程序」，把「启用注册」的开关关掉。

**把页脚和信用关掉。** 默认页面底部有一排「条款 / 隐私 / 联系」，还有一个「Powered by LinkStack」的徽标。同一个配置页往下拉，「页脚链接」里把「显示页脚」「在用户页面上显示信用」关掉，页面就干净了。

### 五、加第一个入口

左边导航点「添加链接」，中间是表单：

**选择块**：从预置的平台里挑一个，YouTube、GitHub、WordPress、Telegram 这些常用的都有，图标和按钮颜色会跟着走。

**自定义标题**：按钮上显示的字，比如「最新视频」。留空就用平台默认名字。

**网址**：点这个按钮去的地方。

点「保存」，页面就多一个按钮；点「保存并添加更多」，可以一口气把剩下的加完。想调顺序，去「个性化 → 链接」里调。

### 六、把页面发出去

右上角「查看页面」，看到的就是别人打开时的样子。页面右上角的分享按钮会把地址复制出来 —— 就是 **公网地址/@用户名** 这一串，发到群里、印在名片上都行。

对方打开不用登录、不用装 App，手机浏览器也一样：按钮会自动竖着排，头像和介绍缩放到合适的大小。

想再改内容，回后台加一条，刷新页面就有了，地址不会变，也不用重新部署。

### 几点说明

**头像自己传。** 「个性化 → 外观」里可以传头像、改显示名和页面描述。别用别人的照片或商标。

**页面地址跟着访问方式走。** 从局域网地址打开，后台显示的页面地址就是局域网地址；挂到隧道以后从公网地址打开，它自动变成公网那一串。不用手填。

**改名要谨慎。** 用户名就是页面路径，改掉之后旧地址会失效。

**端口冲突。** 宿主 8080 很热门，被占了就改编排里冒号前面那个数字。

**更新。** 拉新镜像、重建容器即可。数据都在 linkstack\_data 卷里，不会丢；重建之前先备份这个卷更稳妥。

**它是公开页面。** 放上去的内容等于对互联网公开，别把内部服务地址、手机号之类的东西放上去。

---

原文：<https://www.passnat.com/posts/linkstack-nas-docker-personal-link-page>


## 相关文章

- [极简命令行笔记本 Dnote 自建部署：Docker 快速上手，内网穿透远程访问笔记库](dnote-self-hosted-docker-deployment-internal-penetration-remote-access.md)
- [Rustpad 协同编辑器：NAS Docker 部署 + 内网穿透](rustpad-nas-docker-collaborative-code-editor-intranet-penetration.md)
- [Isso 评论区 NAS Docker 部署 + 内网穿透](isso-nas-docker-self-hosted-comments-intranet-penetration.md)
- [OpenGist 自建代码片段库：NAS Docker 部署 + 修订差异怎么看](opengist-nas-docker-snippet-share-revision-history.md)
- [Donetick 自建家务清单：NAS Docker 部署 + 全家分工 + 积分](donetick-nas-docker-family-chore-list-points.md)
