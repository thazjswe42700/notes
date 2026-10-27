---
title: "Part-DB 电子元件库存：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源电子元件库存 Part-DB：类别、封装、存储位置逐条记，库存按抽屉记数量，搜索框打型号就知道东西在哪、还剩几个；用帕斯内网穿透拿公网地址，出门在电子城也能先查一遍再买。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/partdb-nas-docker-electronic-parts-inventory-intranet-penetration
---

## 项目介绍

Part-DB 是一个开源的电子元件库存管理系统，面向的是抽屉里堆着几百颗电阻电容的人：手头有什么、放在哪一格、还剩几个，都在一个表里。每颗元件一条记录，可以归到类别（电阻、电容、半导体、连接器）、填封装和制造商、写描述。

库存不是简单填一个数字，而是按「存储位置」分批记：同一颗料可以放两个抽屉，每个抽屉各记各的数量，加起来就是总数。想找东西的时候，顶上搜索框打型号或者阻值，列表就只剩相关几条，存储位置和数量都在行里。

界面有简体中文，账号分权限：不登录只能看，登录才能改。

项目地址：[点我跳转](https://github.com/Part-DB/Part-DB-server)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Part-DB 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址登录，以后在电子城挑元件之前，手机打开就能查家里还有多少。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3532（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 partdb，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  partdb:
    image: jbtronics/part-db1:latest
    container_name: partdb
    restart: unless-stopped
    ports:
      - "3532:80"
    environment:
      - APP_ENV=docker
      - APP_SECRET=换成一长串自己的随机字符
      - INITIAL_ADMIN_PW=换成自己的初始密码
      - DEFAULT_LANG=zh_CN
      - DEFAULT_TIMEZONE=Asia/Shanghai
      - BASE_CURRENCY=CNY
      - INSTANCE_NAME=零件库
      - DB_AUTOMIGRATE=true
    volumes:
      - partdb_uploads:/var/www/html/uploads
      - partdb_media:/var/www/html/public/media

volumes:
  partdb_uploads:
  partdb_media:
```

整段编排里只有两处要改：

**APP\_SECRET**：换成你自己的随机字符串，越长越好，字母数字混着来。它用来签名登录 Cookie，装好之后别再改，改了大家都得重新登录。

**INITIAL\_ADMIN\_PW**：换成你自己的初始密码。注意它不是「永久密码」——第一次登录进去，Part-DB 会强制你改一次密码，改完这个值就不再起作用了。

端口 3532 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了，首次启动它会自动建库、跑一遍数据库迁移，十几秒就好。

数据库和附件都在两个具名卷里（partdb\_uploads、partdb\_media），备份这两个卷就够了。

**关于 TRUSTED\_HOSTS：** 管理员登录后，首页顶部会提示「TRUSTED\_HOSTS not configured」。这是 Part-DB 提醒你可以限制访问的主机名。这里故意不设：一旦设成固定的主机名，公网隧道分配的地址一变，页面就会被它直接挡掉（返回 400）。这个提示只有管理员看得到，不影响普通访问，先按上面的编排用就行。

### 三、第一次打开：改密码、选中文

用公网地址打开，用户名 admin，密码就是编排里 INITIAL\_ADMIN\_PW 填的那串。登录后会被推到用户设置页，先改一次密码（旧密码填初始密码，新密码填两遍）。改完在同一页的「配置」里把语言选成中文，时区和货币按自己习惯设。

以后打开同一个地址，用新密码登录。默认的匿名访客属于只读组：不登录也能翻库存，但改不了任何东西，把地址发给朋友看清单是安全的。

### 四、把元件记起来

**先建类别和存储位置。** 左侧「类别 → 新建类别」，按自己的分法建几层，比如电阻、电容、半导体、连接器。「编辑 → 存储位置」里按实物建：元件柜 A 下面分抽屉 01、02、03，名字跟抽屉上贴的标签一致，以后找起来才对得上。

**再建物料。** 点「新建物料」，填名称（比如「金属膜电阻 10kΩ 1/4W」）和描述，类别选电阻，最小库存填个提醒线，保存。

**最后记库存。** 打开这颗物料的「库存」标签，点「增加库存」，选存储位置（元件柜 A · 抽屉 01），填数量（比如 87），保存。同一颗料放在两个抽屉，就再加一条库存，两条各记各的数量，详情页里自动合计。

用量变了不用去改那个数字：详情页的库存行右边有一排按钮，减号一下少一个，加号一下多一个，旁边那个双向箭头是整批调整。

### 五、找东西：搜索 + 存储位置

以后要找料，直接在顶上搜索框打型号、阻值或者封装，比如 10k，列表只剩相关的几条，「存储位置」和「数量」两列就在同一行，哪一格、还剩几个一眼看到。

在电子城或者元件店，手机打开同一个公网地址，搜一下型号就知道家里有没有、够不够，不用凭印象再买一包回来。这也是这套东西真正省事的地方：不是在电脑前整理得好看，而是买东西之前能查一眼。

### 几点说明

**数据在哪。** 数据库和附件都在 partdb\_uploads 卷里，物料图片、附件在 partdb\_media 卷里，两个卷一起备份就行。想搬到别的机器，把这两个卷导出来带走。

**升级。** 镜像标签用的是 latest，升级前先把两个卷备份一份，再到 Docker 里重新拉取镜像、重启项目；编排里带着 DB\_AUTOMIGRATE=true，启动时会自己把数据库结构升到新版本。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Part-DB 本身不需要 HTTPS 才能用；但它记的是自己家的东西，公网地址别到处发，用完了可以在插件里把隧道关掉。

**条码和导入。** Part-DB 里还有条码扫描、CSV 导入这些入口，需要摄像头或者按它的模板整理文件，这篇只写了手动录入这条最稳的路，其他功能等熟了自己再摸。

---

原文：<https://www.passnat.com/posts/partdb-nas-docker-electronic-parts-inventory-intranet-penetration>


## 相关文章

- [Dolibarr 小店管理：NAS Docker 部署 + 内网穿透](dolibarr-nas-docker-crm-quotes-inventory-intranet-penetration.md)
- [Note Mark 笔记：NAS Docker 部署 + 内网穿透](note-mark-nas-docker-markdown-notes-search-intranet-penetration.md)
- [copyparty 自建文件服务器：飞牛 NAS Docker 部署 + 内网穿透外网访问](copyparty-self-hosted-file-server-nas-docker-deployment-intranet-penetration.md)
- [Seafile 私有文件库：NAS Docker 部署 + 内网穿透](seafile-nas-docker-private-file-library-intranet-penetration.md)
- [Kanboard：NAS Docker 任务看板 + 内网穿透](kanboard-nas-docker-family-task-board-intranet-penetration.md)
