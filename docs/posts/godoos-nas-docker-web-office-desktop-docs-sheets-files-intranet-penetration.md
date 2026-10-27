---
title: "GodoOS 网页办公桌面：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker 部署开源网页办公桌面 GodoOS：一个容器一个端口，打开就是桌面，文档、表格、文件管理在同一套系统里，文档存自己的盘；用帕斯内网穿透拿公网地址，出门用手机也能打开这个桌面。含完整编排、端口说明和首次访问设置。"
canonical: https://www.passnat.com/posts/godoos-nas-docker-web-office-desktop-docs-sheets-files-intranet-penetration
---

## 项目介绍

GodoOS 是一个开源项目，把办公常用的一套东西做成了一个网页桌面：打开浏览器，左边一列图标，文档、表格、演示文稿、文件管理都在里面，点开就是窗口，用起来跟本机桌面差不多。

它跟单独的在线文档不一样的地方在于「桌面」这一层：文档和表格各自有编辑器，文件管理里能看到 C、D、E 三个盘符，刚保存的 docx、xlsx 就躺在对应目录里，双击又能打开。文档、表格、文件是同一套系统里的，不用在几个网页之间来回切。

整个东西是一个容器、一个端口，不需要数据库，也没有登录页：装好打开就是桌面。

项目地址：[点我跳转](https://github.com/godoos/godoos)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 GodoOS 用 Docker Compose 装起来。GodoOS 的编排很短，装好打开就是桌面，出门在外也能用手机打开。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 godoos，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  godoos:
    image: godoos/godoos:latest
    container_name: godoos
    ports:
      - "56780:56780"
    volumes:
      - ./godoos:/.godoos
    restart: unless-stopped
```

三处说明：

**端口 56780**：GodoOS 的前端把服务地址默认写成了「当前主机名 + 56780」，所以宿主端口要和容器端口一致，也就是冒号两边都写 56780。换成别的宿主端口，打开会一直停在开机画面。

**./godoos**：数据目录。桌面配置、系统库和你的文档都在容器的 /.godoos 里，挂到宿主的一个文件夹，以后重建容器、升级镜像，文档都还在。

**restart: unless-stopped**：NAS 重启后自动把桌面拉起来。

勾上「创建项目后立即启动」，镜像下完（约 225MB）就起来了，几秒钟后打开地址就是桌面。

### 二、第一次打开

在浏览器里打开 http://NAS的局域网地址:56780，等几秒开机动画走完，就是桌面。不需要注册、不需要登录，左边一列图标，底部是任务栏和开始按钮。

第一次打开会看到「此电脑、应用商店、内网聊天、文档、表格、markdown、思维导图、演示文稿、文件编辑器、看板、白板、图片编辑、甘特图、浏览器、AI助手、模型管理」这些图标。这篇只讲文档、表格和文件这三样，应用商店和 AI 相关的入口先不动。

### 三、用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 56780。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 四、让公网地址真正生效

这一步是 GodoOS 特有的，不做的话，用公网地址打开会卡在开机画面：前端默认去连「主机名:56780」，而从公网域名进来时，那个地址是错的。

用公网地址打开桌面，点左下角开始按钮 →「设置」→「存储配置」，在「存储地址」里填你刚才拿到的公网地址（原样带上 http:// 或 https://），点「确认」，然后刷新页面。桌面就能正常加载了，之后手机、外面的电脑都用这个地址打开。

**只在局域网用的话，这一步可以不做**：直接用局域网地址:56780 打开就行。反过来，把「存储地址」填成公网地址以后，局域网里也走公网地址，隧道关掉就打不开了；想切回来，把这一栏清空、确认即可。

### 五、写文档

双击桌面的「文档」图标，开出一个跟常见办公软件很像的窗口：开始、字体、插入、页面四个标签，下面是加粗、斜体、下划线、上下标这些按钮。在编辑区点一下就能打字，中文直接输入。

写完点左上角那个软盘图标保存，会弹出目录选择框，选好文件夹、填文件名、点保存，提示「文件写入成功」。默认目录是 C 盘用户目录下的 Documents/Word。

保存之后，打开「此电脑」→ 左侧「我的收藏 → 文档」，就能看到刚存的 docx，双击可以再打开继续改。

### 六、做表格

双击「表格」图标，是一个电子表格：行列号、公式栏、常用格式按钮、底部 Sheet1/2/3 三个工作表。点单元格直接输入，Tab 往右、Enter 往下，跟平时用的表格习惯一致。

填完点右上角「保存」，同样选目录、填文件名。xlsx 会存到 Documents/Excel 下，在「此电脑 → 我的收藏 → 表格」里能看到。表格还带导入、导出按钮，需要跟别人交换文件时用得上。

### 七、文件都在哪

双击「此电脑」，看到的是 GodoOS 自己的文件盘：文档、办公、回收站、系统几个目录，左边「我的收藏」里按类型列了文档、表格、演示文稿、markdown、思维导图、白板、看板、图片库、音乐、视频。

所有东西都落在容器的 /.godoos/os 下面，也就是你挂载的那个 ./godoos 文件夹。备份这一个文件夹就够了；换一台机器，把这个文件夹拷过去、用同样的编排起容器，桌面和文档原样回来。

### 几点说明

**数据备份。** 只备份 ./godoos 一个目录即可，桌面配置（desktop.json）、系统库（data/db/system.db）和文档都在里面。

**升级。** 镜像标签是 latest，升级前先备份 ./godoos，再到 Docker 里重新拉取镜像、重启项目。

**明文 HTTP。** 内网穿透给的是 http 地址，GodoOS 在 http 下能正常用。它记的是自己家的文档，公网地址别到处发，用完了可以在插件里把隧道关掉。

**没有登录页。** 打开地址就是桌面，等于谁拿到地址谁就能看文档。公网地址要当成口令一样收好；多人共用一台的话，可以在「设置 → 用户角色」里再研究权限。

**这篇没碰的部分。** 应用商店、AI 助手、模型管理、内网聊天都需要额外的服务或者外部模型，这篇只演示文档、表格、文件这条最稳的路。

---

原文：<https://www.passnat.com/posts/godoos-nas-docker-web-office-desktop-docs-sheets-files-intranet-penetration>


## 相关文章

- [Docmost 共享文档库：NAS Docker 部署 + 内网穿透](docmost-nas-docker-family-shared-wiki-intranet-penetration.md)
- [Moodist 环境音效：NAS Docker 部署 + 内网穿透](self-hosted-ambient-sound-platform-moodist-75-curated-sounds-meditation-privacy-first-open-source-free.md)
- [ReDoc 接口文档：NAS Docker 部署 + 内网穿透](redoc-api-docs-nas-docker-openapi-intranet-penetration.md)
- [Webtop：NAS 上的网页桌面，Docker 部署 + 内网穿透](webtop-nas-browser-linux-desktop-docker.md)
- [思绪思维导图：NAS Docker 部署 + 内网穿透](mindmap-web-app-lightweight-opensource-tool-multiple-structure-types-export-formats-nas-docker-deployment.md)
