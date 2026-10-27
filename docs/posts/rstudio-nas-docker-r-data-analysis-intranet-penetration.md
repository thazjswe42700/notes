---
title: "RStudio 数据分析：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署 RStudio Server：浏览器打开就是完整的 R 开发环境，编辑器、Console、数据框、图表一个页面全有。用内置 iris 数据演示 head、summary、View 和散点图；用帕斯内网穿透拿公网地址，手机流量也能接着跑。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/rstudio-nas-docker-r-data-analysis-intranet-penetration
---

## 项目介绍

RStudio 是 R 语言最常用的开发环境，写脚本、跑命令、看变量、画图都在一个窗口里。RStudio Server 把这一整套界面搬进浏览器：服务跑在一台机器上，你用另一台机器的浏览器打开地址就能用，装到 NAS 上之后，代码和数据都留在自己家里。

界面分四块：左上角是编辑器，写 R 脚本；左下角是 Console，命令一行行跑；右上角 Environment 列出当前的数据框和变量；右下角 Plots 直接出图。数据框点开就是表格，改一行代码重新跑，图跟着重画。

这次用的镜像 rocker/rstudio 是 R 社区维护的预装镜像，R 和 RStudio Server 都装好了，不用自己配环境。内置的 iris、mtcars 这些示例数据集也在，不联网就能拿来练手。

项目地址：[点我跳转](https://github.com/rocker-org/rocker)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 RStudio 装起来。先穿透、后部署，装完用公网地址打开，以后在别的电脑或者手机上也能接着写。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8787（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 rstudio，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  rstudio:
    image: rocker/rstudio:latest
    container_name: rstudio
    restart: unless-stopped
    ports:
      - "8787:8787"
    environment:
      - DISABLE_AUTH=true
      - TZ=Asia/Shanghai
    volumes:
      - rstudio_data:/home/rstudio

volumes:
  rstudio_data:
```

整段编排里只有两处可能需要改：

**端口**：8787 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。冒号右边是容器里的 8787，不要动。

**DISABLE\_AUTH**：这里设成 true，打开地址不用登录，直接进 R 控制台。自己长期用建议改成登录模式（见下面「几点说明」）。

家目录挂在一个具名卷 rstudio\_data 上，脚本、上传的数据、装的包都在里面；容器重建不会丢，备份这个卷就够了。勾上「创建项目后立即启动」，镜像下完就起来了，首次启动大约半分钟。

镜像比较大，第一次拉取要等一会儿，进度在 Docker 的日志里能看到。

### 三、第一次打开

用公网地址打开，没有登录页，直接就是 R Console，上面会打印 R 的版本和欢迎信息。这是因为编排里设了 DISABLE\_AUTH=true，适合自己家里演示或者内网用。

**一句提醒：** 免登录等于拿到地址的人都能进 R 控制台。地址别到处发，用完可以在插件里把隧道关掉；要长期开放，建议去掉 DISABLE\_AUTH，按 rocker/rstudio 镜像文档用 PASSWORD 环境变量设一个登录密码。

### 四、跑第一段 R：内置数据

在 Console 里输入 data(iris)，再输入 head(iris)，就能看到这张内置表的前六行：150 行、5 列，分别是花萼长度、花萼宽度、花瓣长度、花瓣宽度和品种。

输入 summary(iris)，每一列的最小值、四分位、均值、最大值一次列出来。右上角 Environment 里会多出一个 iris，点右边的表格图标，或者输入 View(iris)，整张表就摊开成可滚动的表格，每一列旁边还有分布小图。

换自己的数据也一样：read.csv 读进来，就是一个数据框，后面的操作都相同。

### 五、画一张图

在 Console 里跑下面三行，右下角 Plots 就会出一张散点图，横轴花瓣长度、纵轴花瓣宽度，三个品种用三种颜色分开：

```
plot(iris$Petal.Length, iris$Petal.Width,
     col = c("#4C78A8", "#F58518", "#54A24B")[iris$Species],
     pch = 19, xlab = "Petal length", ylab = "Petal width")
```

想按品种算平均花瓣长度，用 aggregate：aggregate(Petal.Length ~ Species, data = iris, FUN = mean)，Console 里直接打印三行结果。把这几行存成 demo.R，下次打开 source("demo.R") 就能一次跑完。

改一行代码、按一下回车，图就重画。数据框、统计结果、图表都在同一个页面里，不用在几个软件之间倒来倒去。

### 几点说明

**数据在哪。** 脚本和数据都在 rstudio\_data 卷挂载的 /home/rstudio 下，容器删了重建也还在；备份这一个卷就行。想搬到别的机器，把卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先备份卷，再到 Docker 里重新拉取镜像、重启项目。R 包如果装在系统目录里，重建容器后要重装；装在家目录里的包会跟着卷保留。

**手机打开。** 手机浏览器也能打开同一个地址，界面是桌面布局压缩，字比较小，适合临时看一眼数据或者跑一小段；长时间写代码还是电脑上舒服。

**中文界面。** RStudio 默认英文，想换中文在 Tools → Global Options 里改语言；R 本身的中文支持取决于系统 locale，这篇演示用的英文界面。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，RStudio 本身不需要 HTTPS 才能用；但免登录模式下等于把控制台开放出去，地址别随便发，不用时把隧道关掉。

---

原文：<https://www.passnat.com/posts/rstudio-nas-docker-r-data-analysis-intranet-penetration>
