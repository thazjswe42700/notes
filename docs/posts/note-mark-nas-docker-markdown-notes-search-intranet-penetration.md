---
title: "Note Mark 笔记：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源 Markdown 笔记 Note Mark：笔记本归类、编辑实时预览、自动保存，按名字搜索一键跳转；用帕斯内网穿透拿公网地址，在外面也能写、也能翻。含完整编排、注册关闭和账号管理步骤。"
canonical: https://www.passnat.com/posts/note-mark-nas-docker-markdown-notes-search-intranet-penetration
---

## 项目介绍

Note Mark 是一个开源的 Markdown 笔记应用，定位是「够快、够简单」：打开网页就能写，笔记按笔记本归类，数据以普通文件和 SQLite 数据库的形式存在磁盘上，随时能整个拷走。

写笔记的界面分四种视图：只读的渲染页、看源码的纯文本页、全屏编辑器，以及左边写、右边实时预览的分栏。正文是 GitHub 风格的 Markdown，标题、列表、引用、待办清单、表格、代码块都支持；编辑时自动保存，不用点存盘按钮。

找东西用搜索：输入笔记本或笔记的名字，点一下就跳过去。注意它搜的是名字，不是全文。界面有亮色和暗色两套主题，手机打开会按屏幕宽度自动收窄。项目地址：[点我跳转](https://github.com/enchant97/note-mark)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Note Mark 用 Docker Compose 装起来。它启动时要填一个访问地址，所以先穿透、后部署，装完直接用公网地址打开，以后在外面也能写笔记。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来，下一步要填进编排。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 note-mark，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  note-mark:
    image: ghcr.io/enchant97/note-mark-aio:latest
    container_name: note-mark
    restart: unless-stopped
    ports:
      # 左边是 NAS 上的端口，被占用就改成别的
      - "8080:8080"
    environment:
      # base64 编码的密钥，用 openssl rand -base64 32 生成后替换下面这串
      JWT_SECRET: Y2hhbmdlLW1lLXRoaXMtaXMtYS1kZW1vLXNlY3JldC0zMg==
      # 你访问这个应用的地址，结尾不要带斜杠
      PUBLIC_URL: http://nas.local:8080
      # 注册开关：第一个账号建好之后改成 "false"
      ENABLE_INTERNAL_SIGNUP: "true"
    volumes:
      - note_mark_data:/data

volumes:
  note_mark_data:
```

这段编排里要改三处：

**JWT\_SECRET**：登录令牌的签名密钥，必须是 base64 编码、解码后至少 32 字节。用 openssl rand -base64 32 生成一串（或者在任意支持 base64 的工具里生成 32 字节随机数），替换掉示例那串。换掉之后已经登录的会话会失效，重新登录一次就行。

**PUBLIC\_URL**：填上一步拿到的公网地址，比如 http://你的地址:端口。结尾不要带斜杠，否则启动时会报校验错误。这个值会用来生成笔记的分享链接，填错了点开链接会打不开。

**ENABLE\_INTERNAL\_SIGNUP**：注册开关，默认开着。第一次装先留着 true，注册完第一个账号之后改成 false（见下一节）。

端口 8080 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，等镜像拉完，状态变成运行中就可以打开了。数据都在 note\_mark\_data 这个具名卷里，里面是 SQLite 数据库和笔记文件，备份这一个卷就够了。

### 三、第一次打开：注册第一个账号，然后关掉注册

用公网地址打开，点登录页下面的「Need An Account?」进注册页，填用户名、全名和两遍密码，点「Create User」，再用刚建的账号登录，就进到自己的主页了。

这里有个小坑：注册页只能从登录页点进去，直接在地址栏里敲 /signup 再刷新会跳回登录页，别以为注册没开。

账号建好之后，回到 Docker 的编排里把 ENABLE\_INTERNAL\_SIGNUP 改成 "false"，保存并重新启动项目。再打开登录页，注册入口就没了，别人拿到地址也注册不了账号。已经登录的账号不受影响，笔记都在。

之后要加人，用命令行建账号：**docker exec -it note-mark ./note-mark user add -u 用户名 -p 密码**。Note Mark 没有管理员角色的概念，每个账号管自己的笔记本；想让别人看某本笔记，把笔记本设成公开，或者单独分享。

### 四、建笔记本、写笔记

**先建笔记本。** 主页点「New Book」，填标题（比如「家庭说明书」），下面那行地址会自动生成一串，可以改成好记的英文；要分享给别人的话勾上 Public。点「Create」。

**再建笔记。** 进笔记本，点右上角的新建笔记图标，填标题和地址，点「Create」。

**开始写。** 点工具栏的「编辑 + 预览」，左边写 Markdown，右边实时出效果。列表、引用、待办清单、表格都有对应的工具栏按钮；改一个字自动保存，页面上方会显示 Auto Save。想全屏写就切「编辑器」，想只看成品就切「渲染」，想看源码就切「纯文本」。

笔记本和笔记的地址都是好记的英文路径，复制浏览器地址发给别人就能直接打开；整篇笔记也能用工具栏的复制按钮一键拷走。

### 五、搜索

笔记多了就靠搜索：点左上角的「Search」，输入笔记本或笔记名字里的几个字，下面就会列出匹配的条目，点一下直接跳过去。再提醒一次，它按名字搜，正文里的词搜不到，所以起名字的时候尽量把关键词写进标题。

### 几点说明

**数据在哪。** note\_mark\_data 卷里是 /data 目录，SQLite 数据库和笔记文件都在里面，备份、搬家都是整个卷一起。

**升级。** 镜像标签用的是 latest，升级前先把卷备份一份，再到 Docker 里重新拉取镜像、重启项目。

**注册开关。** 默认开着，装完一定要关；关掉之后要加账号就用上面那条命令行。如果哪天忘了口令，也可以用同一条命令的 set-password 子命令重设。

**密钥名字。** 官方文档现在写的是 AUTH\_TOKEN\_\_SECRET，但线上这个 latest 镜像实际只认 JWT\_SECRET；按这篇编排写就行，两个都填上也不冲突。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Note Mark 本身不需要 HTTPS 才能用；但笔记里可能写着家里的事，公网地址别到处发，用完了可以在插件里把隧道关掉。

---

原文：<https://www.passnat.com/posts/note-mark-nas-docker-markdown-notes-search-intranet-penetration>
