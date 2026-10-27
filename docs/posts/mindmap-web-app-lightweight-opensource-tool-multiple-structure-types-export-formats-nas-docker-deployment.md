---
title: "思绪思维导图：NAS Docker 部署 + 内网穿透"
date: 2025-10-16
description: "在 NAS 上用 Docker Compose 部署开源的思绪思维导图（SimpleMindMap 网页版）：单容器、不用数据库，浏览器打开就画，Tab 加下级、Enter 加同级、关联线强调关系，画完导出图片、PDF、Markdown、XMind；用帕斯内网穿透拿公网地址，出门用手机也能打开接着画。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/mindmap-web-app-lightweight-opensource-tool-multiple-structure-types-export-formats-nas-docker-deployment
---

## 项目介绍

思绪思维导图（SimpleMindMap 网页版）是一个开源的在线脑图工具，中文界面，浏览器打开就能画。它把「整理思路」这件事做得足够直接：中间写一个主题，往四周长出分支，节点之间自动连线，适合理清项目结构、拆任务、把一件事的来龙去脉摊在一张图上。

常用操作就三个键：选中一个节点，按 Tab 加一个下级，按 Enter 加一个同级，按 F2 改文字。节点多了点一下「一键整理」，布局自己排好；想强调两个分支之间的关系，用「关联线」把两个节点连起来。节点里还能放图片、图标、超链接、备注、标签、概要，右侧栏可以换主题、换结构（逻辑结构图、思维导图、组织结构图、时间轴、鱼骨图等）。

画完点「导出」，能存成图片、SVG、PDF、Markdown、XMind、TXT、JSON，也能把导出的文件再导入回来继续改。项目地址：[点我跳转](https://github.com/wanglin2/mind-map)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。它只有一个容器，不用数据库、不用配卷，用 Docker Compose 贴两行就起来；想在外面用手机也能打开，再用 [帕斯内网穿透](https://passnat.com) 给它一个公网地址。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 mindmap，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  mind-map:
    image: shuiche/mind-map:latest
    ports:
      - "8080:8080"
```

整段编排就两行：

**image**：镜像地址，直接拉 shuiche/mind-map 这个打包好的网页版。

**ports**：端口映射。右边 8080 是容器里的端口，别动；左边 8080 是你在 NAS 上访问用的端口，被占用的话换成别的数字，隧道里的本地端口也跟着改。

勾上「创建项目后立即启动」，镜像下完就起来了。这个应用没有数据库、没有环境变量、不用挂卷，拉完镜像直接就是一个能用的网页。

### 三、第一次打开

用公网地址（或者局域网地址加端口）打开，看到的就是编辑界面：中间一张默认模板导图，顶上一排工具，右边是样式栏。不用注册、不用登录，打开就能画。

点「新建」清空成一张空白的图，把中间的根节点改成你的主题，然后按 Tab 往下长分支。想换个样子，右边「主题」里挑一套配色，「结构」里换布局，节点和连线会自己重排。

### 四、画一张自己的导图

**长节点。** 选中一个节点，按 Tab 加下级、按 Enter 加同级；也可以点顶上的「子节点」「同级节点」按钮。改文字按 F2，或者直接双击节点。

**强调关系。** 点顶上「关联线」，再分别点两个节点，就会拉出一条虚线把它们的关联标出来——它不影响树的结构，只是给你自己看的旁注。

**整理。** 节点多了按 Ctrl+L（或顶上的「一键整理」）重新排布；不想看的子树可以点节点边上的小圆点收起。

**换个结构。** 同一份内容可以在「逻辑结构图 / 思维导图 / 组织结构图 / 时间轴 / 鱼骨图」之间切换，做项目拆解用逻辑结构图，做时间安排用时间轴，复盘归因用鱼骨图。

### 五、导出和带走

画完点「导出」，左边选格式：

**图片 / SVG / PDF**：直接给别人看，或者贴进文档、打印出来。

**Markdown / XMind**：想接着用别的工具改，导出成通用格式。

**思绪文件（.smm）/ JSON**：这是它自己的格式，导回去还能原样继续编辑。

填一个导出文件名，点「导出」就下载到本地。

### 几点说明

**数据存在哪。** 这个网页版把导图存在浏览器的本地存储里（localStorage），刷新、关掉再开都还在，服务端不保存你的图。所以换一台设备（或者换一个浏览器）打开，看到的是一张新的空白图——要把图带过去，用「导出」存成 .smm 或 JSON，在新设备上「导入」回来就行。

**只有一个容器。** 没有数据库、没有账号体系，NAS 上占的资源很少，重启容器不影响你已经导出的文件。

**公网访问的安全。** 内网穿透给的是 http 地址，这个工具本身没有登录，谁拿到地址都能打开画图（但看不到你浏览器里的图）。自家用够了，不想让别人打开时，在插件里把隧道电源关掉即可。

**升级。** 镜像标签是 latest，想升级就在 Docker 里重新拉取镜像、重启项目，导出的文件不受影响。

---

原文：<https://www.passnat.com/posts/mindmap-web-app-lightweight-opensource-tool-multiple-structure-types-export-formats-nas-docker-deployment>


## 相关文章

- [开箱即用 Markdown 笔记应用，多库协作实时编辑，NAS 极简部署 Many Notes 内网穿透远程访问](many-notes-markdown-notebook-collaborative-editing-nas-deployment.md)
- [私人知识库无限制 Logseq 开源笔记 NAS 部署 内网穿透远程访问管理](private-knowledge-base-unlimited-logseq-opensource-notes-nas-deployment-intranet-penetration-remote-access-management.md)
- [Slidev开发者演示文稿工具，Markdown轻松创建专业幻灯片，代码高亮实时演示编辑](slidev-markdown-presentation-tool-code-highlight.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
- [PDFMathTranslate 整篇翻译：NAS Docker 部署 + 内网穿透](pdfmathtranslate-nas-docker-pdf-translate-formula-layout-intranet-penetration.md)
