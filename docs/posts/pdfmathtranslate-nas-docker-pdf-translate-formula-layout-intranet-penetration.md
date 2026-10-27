---
title: "PDFMathTranslate 整篇翻译：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker 部署开源 PDF 翻译工具 PDFMathTranslate：上传英文 PDF，选好引擎和语言，整篇翻成中文，公式、表格、段落位置都按原样保留；翻完给纯译文和中英对照两份。用帕斯内网穿透拿公网地址，在外面也能传文件、看译文。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/pdfmathtranslate-nas-docker-pdf-translate-formula-layout-intranet-penetration
---

## 项目介绍

PDFMathTranslate 是一个开源的 PDF 翻译工具：把英文 PDF 传上去，选好翻译引擎和目标语言，它把整篇文档翻成中文，同时保留原来的排版——公式还是原来的公式，表格还是原来的表格，段落和图片的位置也不动。它不是在文字层简单替换，而是把版面识别出来，按原来的位置放译文，所以翻完看起来还是那篇文档。

翻完会给你两份文件：一份 Mono，只有译文；一份 Dual，原文和译文对照排版。论文、说明书、英文资料这类整篇的文档，不用一页页复制粘贴，扔进去就行。

项目地址：[点我跳转](https://github.com/Byaidu/PDFMathTranslate)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。PDFMathTranslate 是单容器，不需要数据库，也不需要预先准备任何东西，装完就能用。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 pdf2zh，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  pdf2zh:
    image: byaidu/pdf2zh:latest
    container_name: pdf2zh
    restart: unless-stopped
    ports:
      - "7860:7860"
```

整段编排只有两个地方按自己的情况改：

**端口**：冒号左边是宿主机端口，默认 7860。被别的服务占了就换一个，冒号右边 7860 是容器里的端口，不要动。

**容器名**：保持 pdf2zh 就行，后面看日志、重启都按这个名字找。

勾上「创建项目后立即启动」，镜像拉完就起来了。它没有初始化向导，也没有登录页，打开地址直接就是翻译界面。

### 二、第一次打开：上传、选引擎、翻译、下载

浏览器打开 http://NAS地址:7860，左边是文件区，把 PDF 拖进去，或者点上传。下面一排选项：Service 选翻译引擎，Translate from 选原文语言，Translate to 选目标语言，Pages 选翻译全部还是只翻前几页。

引擎选 Bing 就能直接翻，不用填任何密钥。源语言选 English，目标语言选 Simplified Chinese，页码选 All，点 Translate。右边会实时显示进度条，一页文档通常一两分钟翻完；页数多就按比例变长，可以先去干别的。

翻完下方出现 Translated 区，两份下载：

**Download Translation (Mono)。** 只有译文，适合直接读。

**Download Translation (Dual)。** 原文和译文对照，适合核对自己有没有理解错。

右边 Document Preview 可以直接翻页看效果，公式、表格、段落位置都按原文保留。

### 三、用帕斯内网穿透，在外面也能翻

装好之后默认只能在家里局域网打开。想在手机上、或者在外面也能用，用 [帕斯内网穿透](https://passnat.com) 给它配一个公网地址：飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。

隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 7860（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。以后在外面用这个地址打开，功能和局域网里完全一样。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 四、实际用下来的注意点

**引擎选 Bing。** 界面默认是 Google，但 Google 的公共接口很容易被限流，连续翻几次就可能报 429。换成 Bing 稳定得多，同样不用填密钥。

**内置引擎会把文字发到对应服务的服务器。** Bing、Google 这类在线引擎，翻译时是把文档里的文字发给它们的服务器处理，再拿回译文。自己的普通资料没问题；如果文档涉密或者不能外传，就换成自建引擎（界面里的 Ollama、Argos 这些本地选项），或者干脆别用在线翻译。

**扫描版 PDF 翻不了。** 它处理的是文字层。整页扫描出来的图片，先做 OCR 把文字识别出来，再拿来翻。

**公式和排版能保留，但不是百分百。** 实测普通论文的公式、标题层级、段落位置都保得住；特别复杂的双栏排版或者手写批注，可能需要人工再看一眼。

**翻完可以对照 Dual 核对。** 专业术语拿不准的时候，打开 Dual 对照着看原文，比只看 Mono 放心。

### 几点说明

**升级。** 镜像标签用的是 latest，到 Docker 里重新拉取镜像、重启项目就行。

**资源占用。** 单容器，空闲时内存很小；翻译时会跑版面识别，那一刻会多占一些内存和 CPU，翻完就释放。

**关于页数。** 很长的文档可以先用 Pages 选「First 5 pages」试翻前几页，确认效果再整篇翻，省时间。

---

原文：<https://www.passnat.com/posts/pdfmathtranslate-nas-docker-pdf-translate-formula-layout-intranet-penetration>
