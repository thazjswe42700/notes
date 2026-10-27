---
title: "帕斯内网穿透全平台上手攻略，PassNAT如何使用，飞牛NAS | 群晖NAS | Windows 系统 内网穿透"
date: 2025-10-16
description: "PassNAT内网穿透完整使用教程，详细讲解飞牛NAS、群晖NAS（7.1及以上/以下版本）和\n  Windows平台的配置步骤。从订阅购买、隧道创建到frpc客户端部署，手把手教你实现公\n  网访问。涵盖Docker镜像配置、配置文件导入、端口映射等关键操作，适合NAS用户和远\n  程访问需求者。"
canonical: https://www.passnat.com/posts/p
---

近期有不少小伙伴在询问PassNAT在不同平台如何使用，那么本文章将具体讲述PassNAT在不同平台具体是如何使用的。

在具体的教程开始之前，有一个前置，就是有一个PassNAT的订阅套餐，如果没有，可前往官网购买。[点我前往官网](https://dashboard.passnat.com/)。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2F001.png%3F2025-10-16T11%3A45%3A12.052Z&w=3840&q=80)

购买订阅套餐完成后，我们需要前往隧道列表中新建一个隧道。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_19-59-03.png%3F2025-10-16T11%3A59%3A51.239Z&w=3840&q=80)

点击新建隧道。节点优先选择人数少的节点。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_20-03-28.png%3F2025-10-16T12%3A03%3A48.417Z&w=3840&q=80)

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2Fimage-5.png%3F2025-09-12T08%3A50%3A58.773Z&w=3840&q=80)

隧道名称可以随便填写，看你心情来就好，没有什么要求的。小编个人建议填写与自己项目相关的名称。**本地IP**默认即可，不需要修改。**本地端口**填需要使用的端口，**传输协议**根据项目需求来。我这里以3000举例。**远程端口**可以留空默认。如果需要多个端口，可点击**添加新端口**，步骤同上。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2Fimage-6.png%3F2025-09-12T09%3A07%3A43.370Z&w=3840&q=80)

## 飞牛NAS如何使用PassNAT具体步骤：

创建好隧道后进入隧道。点击此处,**获取配置文件信息**，并点击**复制到粘贴板**。复制内容后续需要使用的。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2Fimage-7.png%3F2025-09-12T09%3A08%3A37.002Z&w=3840&q=80)

回到飞牛NAS中，点击应用商店。搜索frpc。下载**frpc客户端**。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_20-21-39.png%3F2025-10-16T12%3A22%3A02.718Z&w=3840&q=80)

下载完成后打开**frpc客户端**，将他提供的默认内容删除，把刚刚复制的**配置文件信息**粘贴在这里，并点击**提交**。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_20-49-26.png%3F2025-10-16T12%3A51%3A01.781Z&w=3840&q=80)

提交后，回到PassNAT中，使用隧道中提供的**公网IP+端口**。就可以正常访问了。

这样，在飞牛NAS中使用frpc的详细教程就到此为止了。

## 群晖NAS如何使用PassNAT具体步骤：

##### 对于群晖版本＜7.1的可以参考这里：

进入群晖NAS的管理界面，点击套件中心，搜索**Container Manager**，在所有套件中安装该套件。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_21-11-55.png%3F2025-10-16T13%3A14%3A10.806Z&w=3840&q=80)

打开该套件，点击**镜像仓库**—**设置**，我们先添加一个镜像源。我这里使用的是[毫秒镜像](https://1ms.run/)。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_21-33-17.png%3F2025-10-16T13%3A33%3A39.760Z&w=3840&q=80)

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_21-38-55.png%3F2025-10-16T13%3A41%3A52.325Z&w=3840&q=80)

应用并且手动选择该镜像源后，我们搜索frpc。下载这个docker镜像。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_21-44-13.png%3F2025-10-16T13%3A45%3A44.645Z&w=3840&q=80)

下载完成后点击这里的映像，选择刚刚下载好的镜像，点击运行—下一步。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_21-47-00.png%3F2025-10-16T13%3A47%3A27.669Z&w=3840&q=80)

点击这里的FileStation，创建一个文件夹。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-14-40.png%3F2025-10-16T14%3A15%3A19.076Z&w=3840&q=80)

回到PassNAT,创建好隧道后进入隧道。点击此处,获取配置文件信息，并点击**下载配置文件**备用。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2Fimage-7.png%3F2025-09-12T09%3A08%3A37.002Z&w=3840&q=80)

将刚刚下载好的配置文件导入到该目录中。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-18-33.png%3F2025-10-16T14%3A19%3A21.839Z&w=3840&q=80)

点击这里的**添加文件**，找到刚刚上传的配置文件，点击使用。添加后，在这里添加以下内容。具体信息可[点我](https://doc.passnat.com/)查看。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-20-00.png%3F2025-10-16T14%3A20%3A27.993Z&w=3840&q=80)

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-24-52.png%3F2025-10-16T14%3A26%3A38.488Z&w=3840&q=80)

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-26-57.png%3F2025-10-16T14%3A27%3A11.298Z&w=3840&q=80)

操作完成后，下拉找到网络，将原先的**bridge**修改成host，修改完成后点击下一步。点击完成就可以了。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-21-50.png%3F2025-10-16T14%3A22%3A25.556Z&w=3840&q=80)

这样，就可以使用PassNAT中的公网**ip+端口**正常访问了。

#### 对于群晖版本≥7.1的小伙伴，可以使用以下方法：

点击套件中心—设置—套件来源—新增，这里的具体信息我也放在文档里了。[点我查看](https://doc.passnat.com/)。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-43-39.png%3F2025-10-16T14%3A44%3A09.758Z&w=3840&q=80)

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-46-18.png%3F2025-10-16T14%3A48%3A01.520Z&w=3840&q=80)

操作完成后，搜索frpc，下载frpc的客户端。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_22-51-36.png%3F2025-10-16T14%3A56%3A36.472Z&w=3840&q=80)

回到PassNAT，进入隧道。点击此处,获取配置文件信息，并点击复制到粘贴板。复制内容后续需要使用的。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2Fimage-7.png%3F2025-09-12T09%3A08%3A37.002Z&w=3840&q=80)

打开frpc客户端，将自带的配置文件信息删除。粘贴刚刚复制的PassNAT的配置文件。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_23-07-06.png%3F2025-10-16T15%3A09%3A25.811Z&w=3840&q=80)

回到PassNAT，我们可以看到隧道已经在线了，使用提供的公网IP+端口就可以正常访问了。

## windows环境如何使用PassNAT具体步骤：

windows环境中是最简单的。我们先前往官网下载PassNAT的客户端。下载完成后如果有提示病毒等信息，大家不必惊慌，这不是怪兽入侵地球，具体信息可以参考以下信息，[点我直达](https://doc.passnat.com/)。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_23-18-15.png%3F2025-10-16T15%3A20%3A39.483Z&w=3840&q=80)

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_23-24-40.png%3F2025-10-16T15%3A24%3A58.968Z&w=3840&q=80)

下载客户端完成后，启动客户端，点击这里的隧道—创建新隧道。创建隧道的步骤与最开始在网页端步骤类似，我就不过多赘述了。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_23-26-15.png%3F2025-10-16T15%3A26%3A59.140Z&w=3840&q=80)

创建完成后点击这里的启动就可以了。

![](https://www.passnat.com/_next/image?url=https%3A%2F%2Fwww.passnat.com%2Fapi%2Fmedia%2Ffile%2FPixPin_2025-10-16_23-30-23.png%3F2025-10-16T15%3A31%3A15.867Z&w=3840&q=80)

启动成功后，可以点击映射端口，使用提供的公网IP+端口就可以正常链接了。

那么本教程到这里就结束了。

---

原文：<https://www.passnat.com/posts/p>
