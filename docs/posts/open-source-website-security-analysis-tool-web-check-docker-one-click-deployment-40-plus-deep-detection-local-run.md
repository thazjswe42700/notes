---
title: "Web-Check 网站体检：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker 部署开源网站体检工具 Web-Check：填一个网址，四十多项检查一次跑完，DNS、SSL 证书、响应头、开放端口、TLS 配置、服务器状态分级汇总，缺什么一眼看到；用帕斯内网穿透拿公网地址，在外面也能查自己的站。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/open-source-website-security-analysis-tool-web-check-docker-one-click-deployment-40-plus-deep-detection-local-run
---

## 项目介绍

Web-Check 是一个开源的网站体检工具：在输入框里填一个网址，点一下分析，它会从外面把这个站查一遍——DNS 记录、SSL 证书、响应头、HTTP 安全头、开放端口、TLS 配置、服务器状态、第三方追踪、社交标签、碳足迹，四十多项一次跑完。

结果页最上面是分级汇总：哪些是必须处理的（比如证书无效），哪些是建议补的（比如缺安全响应头），哪些是正常的。往下每一项一张卡，具体值都摊开写，不用自己去一条条查。

它适合的场景很直接：自己搭了个站，上线前想知道有没有漏配的东西；或者接手一个站，想快速摸清它的底细。所有检查都从服务端发起，本机浏览器只负责显示结果。

项目地址：[点我跳转](https://github.com/Lissy93/web-check)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。Web-Check 是单容器，不需要数据库，也不需要预先准备任何密钥，装完就能用。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 webcheck，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  web-check:
    image: lissy93/web-check:latest
    container_name: web-check
    restart: unless-stopped
    ports:
      - "3000:3000"
```

整段编排只有两个地方按自己的情况改：

**端口**：冒号左边是宿主机端口，默认 3000。被别的服务占了就换一个，冒号右边 3000 是容器里的端口，不要动。

**容器名**：保持 web-check 就行，后面看日志、重启都按这个名字找。

勾上「创建项目后立即启动」，镜像拉完就起来了。它没有初始化向导，也没有登录页，打开地址直接就是首页。

### 二、第一次打开：填网址、看结果

浏览器打开 http://NAS地址:3000，页面中间是一个输入框，下面列着它能查的所有项目。填一个不带端口的域名（比如你自己的站），点 Analyze，等几秒，结果页就出来了。

结果页从上到下是这么几块：

**顶部分级。** Critical、Issues、Warnings、Informational、Passes 五档，每档后面是条数。第一眼先看 Critical 和 Issues 有没有东西，这两档是要动手的。

**SSL 证书卡。** 证书主体、是否受信任、公钥算法和位数、签发日期、到期日期、有效期天数、序列号都在一张卡里。证书过期或者用了自签名，这一档会直接标红。

**服务器状态卡。** 站点是否在线、HTTP 状态码、响应时间、DNS 查询耗时、TCP 连接耗时、TLS 握手耗时分开列，慢在哪一段一眼看出来。

**开放端口卡。** 从外面能连上的端口和对应的服务名列出来，连不上的端口也一并列出，方便对照。

**HTTP 安全头卡。** 缺哪个安全响应头、缺了会怎样，逐条写清楚，照着补就行。

往下还有 DNS 记录、TLS 连接、DNSSEC、CAA、HSTS、恶意与钓鱼、数据泄露、第三方追踪、社交标签、碳足迹等等，每一项一张卡，哪张卡需要细看就展开。

### 三、用帕斯内网穿透，在外面也能查

装好之后默认只能在家里局域网打开。想在手机上、或者在外面也能用，用 [帕斯内网穿透](https://passnat.com) 给它配一个公网地址：飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。

隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3000（就是编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。以后在外面用这个地址打开，功能和局域网里完全一样。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 四、几个实际用下来的注意点

**网址不要带端口。** 实测在域名后面加 :443 这种带端口的写法，大部分检查会被跳过，只跑出三四项。填不带端口的域名，四十多项才会完整跑一遍。

**有些检查需要外部密钥。** 比如 Google Safe Browsing、Shodan、Cloudmersive 这几项，要自己在环境变量里填对应的 API key 才会出结果，不填的话对应卡片显示跳过，不影响其他检查。这部分不是必需的，先用默认配置就够。

**界面是英文。** 目前没有中文语言包，术语不多，常用的就是证书、响应头、端口这几类。

**只查自己的站。** 别拿它去扫别人的站——一来结果不一定准，二来高频扫描对别人的服务器是负担。自己搭的站、自己负责的站，随便查。

**容器没有持久化数据。** 它不存任何扫描结果，重启容器也不影响使用，不需要挂卷。想留一份报告，结果页底部可以导出 JSON。

### 几点说明

**升级。** 镜像标签用的是 latest，到 Docker 里重新拉取镜像、重启项目就行。

**资源占用。** 单容器，空闲时内存很小；扫描时会临时起一个无头浏览器去截页面，那一刻会多占一点内存，扫完就释放。

**关于那些「跳过」。** 结果页顶部会写多少项成功、多少项跳过、多少项失败。跳过通常是因为缺密钥，或者目标本身不适用（比如纯 IP 没有域名信息）；失败一般是目标站点拒绝连接，把鼠标放到对应卡片上能看到原因。

---

原文：<https://www.passnat.com/posts/open-source-website-security-analysis-tool-web-check-docker-one-click-deployment-40-plus-deep-detection-local-run>


## 相关文章

- [Nginx Proxy Manager Docker部署指南，内网穿透反向代理网关搭建教程配置](nginx-proxy-manager-docker-deployment-guide-intranet-penetration-reverse-proxy-gateway-setup-tutorial-configuration.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自托管即时通讯平台Stoat完整部署指南，内网穿透远程访问无公网限制](self-hosted-stoat-instant-messaging-deployment-guide-intranet-penetration-remote-access-no-public-ip.md)
- [DPanel Docker可视化管理面板一键部署，内网穿透远程访问容器，极简操作无需命令行](dpanel-docker-visual-management-panel-one-click-deployment-intranet-penetration-remote-access-containers-simple-operation-no-command-line.md)
- [免费在线虚拟钢琴演奏平台 AutoPiano NAS 极简部署 内网穿透远程弹奏](free-online-virtual-piano-autopiano-nas-simple-deployment-intranet-penetration-remote-playing.md)
