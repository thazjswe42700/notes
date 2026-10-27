---
title: "自建 AI 画流程图：飞牛 NAS Docker 部署 next-ai-draw-io，一句中文生成 draw.io 图表并远程访问"
date: 2026-09-07
description: "在飞牛 NAS 上用 Docker 部署 next-ai-draw-io，用一句中文生成可编辑的 draw.io 流程图，并通过内网穿透在外网访问。"
canonical: https://www.passnat.com/posts/ai-flowchart-next-ai-draw-io-nas-docker-deployment-intranet-penetration
---

## 项目介绍

next-ai-draw-io 是一个把 AI 和 draw.io 画布接在一起的开源项目：你用一句中文描述想要的图，它直接在画布上把框、箭头和说明文字画出来。

它跟「AI 生成一张图片」那类工具的根本差别在于产物 —— 出来的不是一张定死的图片，而是 draw.io 的画布。框能拖、字能改、线能重新连，还能导出成 .drawio 或者图片带走，下次接着编。画得不满意也不用重新描述一遍，直接上手改就行。

作者在 GitHub 上开源（Apache-2.0），并且提供了打好的官方镜像，NAS 上拉下来就能跑，不用自己构建。

项目地址：[点我跳转](https://github.com/DayuanJiang/next-ai-draw-io)

## 部署安装

装在自己的 NAS 上之后，只能在家里的网络访问。想在外面也能打开，家里又没有公网 IP，可以用 [帕斯内网穿透](https://passnat.com) 把它开放到公网 —— 飞牛的应用中心里就能直接装到插件，不用另外找工具。下面这套流程在飞牛 NAS 上从头到尾走一遍。

### 第一步：准备编排文件

下面这份编排可以直接用，三处需要你按自己的情况改：宿主端口、模型的 API Key，以及访问码。

```yaml
services:
  next-ai-draw-io:
    image: ghcr.io/dayuanjiang/next-ai-draw-io:latest
    restart: always
    ports:
      - "3020:3000"
    environment:
      AI_PROVIDER: minimax
      MINIMAX_BASE_URL: https://api.minimaxi.com/v1
      MINIMAX_API_KEY: 换成你自己的
      AI_MODEL: MiniMax-M3
      ACCESS_CODE_LIST: 换成你自己设的访问码
      DAILY_REQUEST_LIMIT: 300
      DAILY_TOKEN_LIMIT: 2000000
```

端口：冒号左边的 3020 是待会儿在浏览器里输的宿主端口，右边的 3000 是容器内部的端口，不要动。3020 如果和你机器上别的服务撞了，换一个没被占用的即可。

镜像标签用的是上游文档里那个 latest，跟着作者更新走，不用自己钉版本号 —— 钉死了以后项目更新，你这份编排还停在老版本。（顺带一提，这个项目的版本号标签不带 v 前缀，真要钉的话是 0.4.15 这种写法，写成 v0.4.15 是拉不到的。）

AI 后端：**这里用 MiniMax 只是做个演示，不是只能用它。**这个项目自己支持十几家 —— 上游文档的 Multi-Provider 一节里列了 OpenAI、Anthropic、Google、DeepSeek、SiliconFlow、OpenRouter，还有跑在自己机器上的 Ollama。

换成别家就改 AI\_PROVIDER 这一行（写 openai / deepseek / ollama 这些），再把对应的 KEY 和 AI\_MODEL 填上就行。举两个例子：接兼容 OpenAI 协议的接口就写 AI\_PROVIDER: openai，加 OPENAI\_API\_KEY，自定义地址用 OPENAI\_BASE\_URL；家里已经跑着 Ollama 就写 AI\_PROVIDER: ollama、AI\_MODEL: llama3.2，地址用 OLLAMA\_BASE\_URL 指过去，模型就跑在你自己的机器上。本文为了省事，用的是云端 API。

ACCESS\_CODE\_LIST 是访问码，这一行最好不要留空。这个应用的 AI 调用走的是你自己的 Key，一旦开放到公网，任何知道地址的人都能用它画图、消耗你的额度。设了访问码之后，别人打开你的地址还得先在设置里填这一串才用得了。下面两行是每天的请求数和 Token 上限，是第二道闸，按自己的情况调。

### 第二步：在飞牛里建 Compose 项目

打开飞牛桌面上的 Docker，切到上面的「Compose」，点右上角的「新增项目」。项目名称填 aidrawio。

路径这一栏点开，在「我的文件」下面新建一个空文件夹并选中它。这个文件夹就是这个项目以后放编排文件的地方。

来源选「创建docker-compose.yml」，把上面那段编排整段贴进去，勾上「创建项目后立即启动」，点确认。镜像拉完项目就会自己跑起来，回到列表能看到它是运行中。

### 第三步：用 PassNAT 插件开放到公网

在飞牛的应用中心里搜 PassNAT，装上，用账号登一次。打开插件切到「隧道」，点「创建隧道」。

隧道名称随便起（注意不能超过 12 个字符），接入节点和传输协议保持默认。本地 IP 这一栏不用动，留空默认就是 127.0.0.1 —— 插件本来就跑在 NAS 上，回环地址天然指向本机。本地端口填 3020，也就是上面编排里冒号左边那个。

创建完回到隧道列表，点开这条隧道的详情，按标题行最左边那个电源图标启动它，状态转成「运行中」。详情里「公网连接」那一列的地址，就是你在外面要访问的地址。

### 第四步：让它画一张图

用公网地址打开，界面会按浏览器语言自动切到中文，左边是 draw.io 画布，右边是对话框。第一次用先点右上角的设置，把编排里那个访问码填进去。

然后在右边描述你要的图，比如「画一个家里 NAS 的备份流程图：手机和电脑的照片先传到 NAS，NAS 再定时备份到移动硬盘」，点发送。等十几二十秒，左边画布上就会出现画好的流程图，框、箭头和箭头上的说明文字都在。

画完之后就是一张普通的 draw.io 图：拖框、改字、重连线都可以，左上角还能导出成文件保存下来。

## 一些说明

这个项目的界面按浏览器语言自动切换，中文是完整支持的，包括图里的中文标签。

模型的选择对效果影响很大。这个任务要求模型按格式把画布元素吐出来（也就是去调用画图那个工具），模型没走这一步的时候，它就只把方案写成一段文字回给你，画布还是空的。碰上了再发一次，或者换一个更强的模型，一般就画出来了。

生成快慢也跟模型有关。用推理型的模型，它会先在内部把布局想一遍再动手，所以从点发送到图落到画布上，十几二十秒是正常的 —— 别以为是卡住了。

---

原文：<https://www.passnat.com/posts/ai-flowchart-next-ai-draw-io-nas-docker-deployment-intranet-penetration>
