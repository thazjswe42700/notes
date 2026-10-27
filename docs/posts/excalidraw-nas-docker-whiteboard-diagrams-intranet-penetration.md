---
title: "Excalidraw 白板：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源白板 Excalidraw：方框、箭头、文字、手绘都能画，导出 PNG/SVG，保存成文件换设备接着看；用帕斯内网穿透拿公网地址，手机打开同一地址就能用。含完整编排、画图步骤和数据存放说明。"
canonical: https://www.passnat.com/posts/excalidraw-nas-docker-whiteboard-diagrams-intranet-penetration
---

## 项目介绍

Excalidraw 是一个开源的虚拟白板，打开网页就能画。方框、菱形、圆、箭头、线条、文字、自由手绘都有，线条带一点手绘的抖动感，画流程图、家里的网络、随手草图都很顺手。

它最省事的地方是不用建文档：打开就是一块空白画布，按 R 拖一个方框，双击方框直接打字，按 A 从这边拉到那边就是一条箭头。画完可以导出 PNG 或 SVG，也能把整张图保存成一个文件，以后接着改。

需要先说明一点：这个官方镜像是纯前端，没有服务端数据库。图纸保存在你自己浏览器的本地存储里，换浏览器、换设备、清缓存就看不到；想换设备接着看，要用「保存到本地」导出文件，在另一台设备上再打开。它也不是多人实时协作服务，账号和云端同步都不在这个镜像里。

项目地址：[点我跳转](https://github.com/excalidraw/excalidraw)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先在飞牛应用中心装 [帕斯内网穿透](https://passnat.com)，拿到公网地址，再用 Docker Compose 把 Excalidraw 装起来。白板本身不登录也能用，所以装完直接把地址发给手机就能打开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3608（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 excalidraw，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  excalidraw:
    image: excalidraw/excalidraw:latest
    container_name: excalidraw
    restart: unless-stopped
    ports:
      - "3608:80"
```

整段编排只有四行，照抄就能跑：镜像是官方静态前端，容器名自己认得就行，自动重启保证 NAS 重启后白板还在，端口映射的冒号左边是 NAS 上的端口，被占用就换一个，隧道里的本地端口也跟着改。

它不需要挂载卷，也没有数据库要初始化：镜像里就是一套网页，几秒钟就能起来。勾上「创建项目后立即启动」，等容器变成运行中，用浏览器打开「NAS 地址:3608」就能看到白板。

### 三、画第一张图

**画框。** 按 R，或者点顶上工具栏的方框，在画布上按住拖出一个矩形。双击矩形中间，直接输入文字，比如「路由器」，按 Esc 结束。再画两个框，分别写「NAS」和「电视」。

**连箭头。** 按 A，从一个框的边缘拖到另一个框，松手就是一条带箭头的线。箭头两端可以继续拖动，指到哪就连到哪。

**改和删。** 按 V 回到选择工具，点一下选中，拖动改位置，四周的把手改大小。选错了按 Ctrl+Z 撤销，右上角的撤销、重做按钮也在。

**导出。** 点左上角菜单，选「导出图片」，缩放选 2×，点 PNG，就下载一张白底图片；要矢量图就点 SVG。导出的图片不含工具栏和界面，直接发群、贴文档都能用。

### 四、换设备和备份

**保存成文件。** 菜单里的「保存到...」→「保存到本地」，会给一个 .excalidraw 文件，文件名自己填。这个文件就是整张图的完整数据，换电脑、换手机、重装浏览器都靠它。

**在另一台设备打开。** 打开同一个公网地址，点欢迎页的「打开」，选刚才那个文件，整张图就回来了。手机浏览器不支持桌面那套文件选择接口时，会自动走普通的文件选择框，效果一样。

**平时存在哪。** 没保存成文件之前，图纸在浏览器本地存储里，刷新页面不会丢，但清缓存、换浏览器就没了。换设备前记得先保存文件；重要的图建议顺手导出一张 PNG，看图不依赖任何工具。

### 几点说明

**没有登录和权限。** 这个镜像打开就是画布，没有账号体系。公网地址谁能打开，谁就能画；别把带密码、带内部地址的图放上去，用完可以在插件里把隧道关掉。

**不是多人实时协作。** 主菜单里的实时协作、分享链接依赖 Excalidraw 的在线服务，这个自托管镜像里没有配套后端，这篇也不涉及。要几个人一起看，导出 PNG 或文件发过去更稳。

**升级。** 镜像标签是 latest，重新拉取镜像、重启项目就行。升级不影响浏览器里已经存着的图；但升级前把重要图纸保存成文件，总归更稳妥。

**手机上画。** 手机打开是同一块画布，工具栏会变成底部一排图标，单指画、双指缩放。屏幕小，复杂图更适合在电脑上画，手机用来看和改两笔。

---

原文：<https://www.passnat.com/posts/excalidraw-nas-docker-whiteboard-diagrams-intranet-penetration>


## 相关文章

- [VTracer开源栅格转矢量工具Docker部署指南，无公网内网穿透远程访问高分辨率图像转SVG](vtracer-docker-deployment-guide-intranet-access-high-resolution-image-to-svg.md)
- [飞牛 NAS 部署 drawio 流程图工具：浏览器画图、保存图稿并导出 PNG](drawio-nas-flowchart-docker-guide.md)
- [RawTherapee开源原始照片处理软件，专业级RAW格式编辑工具，支持600+相机型号](rawtherapee-raw-photo-editor-open-source.md)
- [在线图像格式转换工具Webp2jpg，支持PSD转JPG、PNG压缩、视频字幕拼接，Docker一键部署NAS](webp2jpg-online-converter-psd-png-compress-video-subtitle-docker-nas.md)
- [Slidev开发者演示文稿工具，Markdown轻松创建专业幻灯片，代码高亮实时演示编辑](slidev-markdown-presentation-tool-code-highlight.md)
