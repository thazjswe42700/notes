---
title: "MicroBin 临时分享：NAS Docker 部署 + 内网穿透"
date: 2025-10-16
description: "把一段文字或临时口令发给别人，设好有效期和查看次数，对方看完自动销毁：在 NAS 上用 Docker Compose 部署开源临时分享服务 MicroBin，用帕斯内网穿透拿公网地址，含数据卷、管理员口令和明文 http 下的加密开关设置。"
canonical: https://www.passnat.com/posts/microbin-lightweight-self-hosted-clipboard-docker-deployment-intranet-penetration
---

## 项目介绍

MicroBin 是一个可以自己搭的临时分享服务。一段文字、一个小文件，或者一条要转出去的网址，都可以存成一条「面条」，给它设一个有效期、一个可以查看的次数，再把地址发给别人。对方看完了，这条东西就自动消失，不用留在聊天记录里。

最常用的场景是临时口令：朋友来家里问 WiFi 密码、物业发的门禁临时码、给同事的一段配置片段。发完就焚，事后不用再改密码，也不用去聊天记录里翻它。

它跑在你自己的 NAS 上，内容存在你自己的硬盘里；服务端加密默认是开着的。除了文字，也能传附件、做短链接，管理页在 /admin。

项目地址：[点我跳转](https://github.com/szabodanika/microbin)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 MicroBin 装上。顺序不能反——MicroBin 复制出来的分享地址，是按编排里填的那个公网地址拼的，填成家里的局域网地址，发给别人就打不开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 microbin，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，再改两处：

```yaml
services:
  microbin:
    image: danielszabo99/microbin:latest
    container_name: microbin
    restart: unless-stopped
    ports:
      - "8080:8080"
    environment:
      - MICROBIN_ADMIN_USERNAME=admin
      - MICROBIN_ADMIN_PASSWORD=换成你自己的管理员口令
      - MICROBIN_PUBLIC_PATH=http://你的公网地址:端口
      - MICROBIN_ENCRYPTION_CLIENT_SIDE=false
      - MICROBIN_HIDE_FOOTER=true
      - MICROBIN_DISABLE_UPDATE_CHECKING=true
      - MICROBIN_DISABLE_TELEMETRY=true
      - MICROBIN_DEFAULT_EXPIRY=24hour
    volumes:
      - microbin_data:/app/microbin_data
volumes:
  microbin_data:
```

**MICROBIN\_PUBLIC\_PATH**：填第一步拿到的公网地址，前面带上 http://。这一行不填的话，页面上的复制地址和二维码功能是禁用的（管理页会显示一条 WARNING）。

**MICROBIN\_ADMIN\_PASSWORD**：换成你自己的管理员口令。管理页在 /admin，里面能看到所有面条的查看次数，也能手动删。

**MICROBIN\_ENCRYPTION\_CLIENT\_SIDE=false**：这一行别删。镜像默认开着浏览器端加密，但那要用浏览器的加密接口，明文 http 下浏览器不给用，留 true 的话新建页会报错。关掉它，服务端加密（MICROBIN\_ENCRYPTION\_SERVER\_SIDE）仍然是开着的，数据在硬盘上还是加密的。

数据卷用的是具名卷 microbin\_data，挂在容器的 /app/microbin\_data。镜像里的进程不是 root，具名卷会自动把权限设对；想改成绑定挂载的话，要先把 NAS 上的目录属主改成 65532：65532。勾上「创建项目后立即启动」，点确认，一个容器起来就能用。

### 三、发第一条

用公网地址打开，就是新建页。上面四行选项从左到右：Expiration 是有效期（一分钟到一周），Burn After 是可以查看的次数（No Limit、First Read、10th Read……），Syntax 是代码高亮，Privacy 是公开还是隐藏，最右边 Password 可以再加一道口令。

界面是英文的，选项就这几个。内容写进 Content 框，点 Save，就得到一条新面条。地址的尾巴是三五个英文单词，像 mouse-spider-bear 这样，记不住也没关系，页面上一键复制。

想让人看完就销毁，Burn After 选 First Read；只想发给某个人、不想出现在公开列表里，Privacy 选 Unlisted。

### 四、对方看到什么

对方打开地址，只会看到这段内容，下面一行小字写着已经读了几次。看完再刷新，页面就变成 404，内容已经销毁了。如果设了 Password，他会先看到一个输入框，输对口令才看得到内容。

分享文件的路径是 /file/，纯文本是 /raw/，做短链接也可以；这些在首页的 Guide 里有写。

### 几点说明

**复制出来的地址不对。** 多半是 MICROBIN\_PUBLIC\_PATH 没填，或者填的还是旧的隧道地址。改完编排，在 Compose 里重新启动项目就生效。

**新建时报加密相关的错。** 检查 MICROBIN\_ENCRYPTION\_CLIENT\_SIDE 是不是 false；用 http 访问时，这一项必须是 false。

**更新。** 镜像标签用的是官方写法的 latest；升级之前把 microbin\_data 这个卷备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/microbin-lightweight-self-hosted-clipboard-docker-deployment-intranet-penetration>


## 相关文章

- [私有ntfy推送服务NAS极简部署，内网穿透外网实时通知接收方案](private-ntfy-push-service-nas-minimal-deployment-intranet-penetration-external-network-real-time-notification-solution.md)
- [PairDrop 自建隔空投送：飞牛 NAS Docker 部署 + 内网穿透跨设备传文件](pairdrop-cross-device-file-transfer-nas-docker-deployment-intranet-penetration.md)
- [ExpenseOwl 轻量记账：NAS Docker 部署 + 内网穿透](expenseowl-lightweight-expense-tracker-nas-docker-intranet-penetration.md)
- [HivisionIDPhotos 手机拍一张就出证件照：飞牛 NAS Docker 部署 + 内网穿透](hivision-id-photos-professional-certificate-photo-generator.md)
- [离线OCR识别工具TrWebOCR，NAS一键部署，内网穿透远程访问，支持中文识别](tr-web-ocr-offline-recognition-nas-deployment-intranet-penetration-chinese-support.md)
