---
title: "Spliit：NAS 上的 AA 分账工具，Docker 部署 + 内网穿透"
date: 2026-10-18
description: "群组地址发给朋友，各自把花的钱记上，谁欠谁多少自动算好；附编排、数据库口令和备份说明。"
canonical: https://www.passnat.com/posts/spliit-nas-docker-shared-expenses-split-bills
---

## 项目介绍

Spliit 是一个开源的 AA 分账工具：出去玩之前建一个群组，谁付了钱就记一笔，最后它会算出每个人应该收多少、付多少，还给出「谁转给谁」的最少转账方案。

跟记账软件不一样的地方在于 **朋友不用注册**：群组建好之后，右上角分享按钮把群组地址复制下来，发到群里，大家用手机浏览器打开就能直接记，不用下载 App，也不用注册账号。

账本放在自己家的 NAS 上，没有广告，也不绑定任何支付平台。

项目地址：[点我跳转](https://github.com/spliit-app/spliit)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 Docker Compose 把服务装起来，再用 [帕斯内网穿透](https://passnat.com) 把群组地址放出去，朋友在外面也能打开。

### 一、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 spliit，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  app:
    image: ghcr.io/spliit-app/spliit:latest
    user: "1000:1000"
    ports:
      - "3000:3000"
    environment:
      POSTGRES_PRISMA_URL: postgresql://spliit:change-me-spliit@database:5432/spliit
      POSTGRES_URL_NON_POOLING: postgresql://spliit:change-me-spliit@database:5432/spliit
      DEFAULT_CURRENCY_CODE: CNY
    volumes:
      - ./app/cache:/usr/app/.next/cache
    depends_on:
      database:
        condition: service_healthy
    restart: unless-stopped

  database:
    image: postgres:17.3
    user: "1000:1000"
    environment:
      POSTGRES_DB: spliit
      POSTGRES_USER: spliit
      POSTGRES_PASSWORD: change-me-spliit
    volumes:
      - ./database/data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U spliit"]
      interval: 5s
      timeout: 3s
      retries: 10
    restart: unless-stopped
```

几个地方照着改：**POSTGRES\_PASSWORD** 改成你自己的数据库口令，上面两行连接地址里的口令也要一起改，**三处必须一致**，不然应用连不上数据库。

端口一行写的是 **3000:3000**：冒号右边是容器里的端口，不用改；冒号左边是 NAS 上的端口，如果 3000 已经被别的服务占了，换成别的数字，后面隧道也填同一个数字。

**./database/data** 存的是全部账本数据，**./app/cache** 是网页的构建缓存。这两个目录都要留好，迁移或者重建容器时把 database 整个备份下来就行。

数据库有健康检查，应用会等数据库准备好了再启动，所以第一次点「启动」直接等着就行，不用手动重试。镜像下载大约几百 MB。

### 二、第一次打开：建一个群组

浏览器打开 http://NAS的局域网地址:3000，首页点「前往群组」，再点「创建」。

群组名随便起，比如「周末露营」；币种选人民币；下面把参与的人名填上。点「创建」之后，群里每个人都会拿到自己的名字，之后记账就是选「谁付的、为谁付的」。

第一次进群会问「你是哪位？」，选中你自己保存一下，之后在这台设备上打开就会默认按你的身份显示。这一步只是本地设定，填错了可以在「设定」里改。

点右上角的绿色加号记第一笔：标题、日期、金额，然后选「支付自」（谁掏的钱）和「支付给」（这笔钱算谁头上，默认全体平分）。金额和参与人填完，点创建就记上了。

### 三、把群组地址发给朋友

群组建好后，页面右上角有一个分享按钮，点一下会把群组地址复制到剪贴板——此时地址还是局域网地址，只有连在家里 WiFi 的设备能打开。要让朋友在外面也能用，得先把它放出去。

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **3000**（跟编排里冒号左边的数字一致）。

建好点开隧道、按一下电源，状态变成「运行中」，详情里那一串就是公网地址，复制下来。

用这个公网地址打开群组页，再点一次分享按钮，这次复制到的就是公网地址了，发到群里谁都能打开。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 四、算账

玩完回到群里切到「余额」，每个人一行：正数是该收的，负数是该付的，下面的「建议报销」直接告诉你谁转给谁，转账次数已经压到最少。点一笔消费还能改金额、换付款人或者删掉。

「统计」页能看总花费、平均每笔、最大的一笔，还有按月汇总，回来复盘很方便。

### 几点说明

**三处口令要一致。** 数据库的 POSTGRES\_PASSWORD、上面两行连接地址里的口令，必须完全相同；改了一处忘了另外两处，应用会一直连不上数据库。

**先等数据库。** 编排里已经给数据库加了健康检查，应用会等它就绪再启动；如果你把 healthcheck 删掉，第一次启动时应用可能先退出一次，再启动一次就好了。

**备份 database 目录。** 所有群组、成员、消费记录都在这里，升级镜像之前先复制一份。

**群组地址就是钥匙。** 拿到地址的人都能看和改这个群组的账，别把地址发到公开的地方；不想让人进了，把群组删掉或者把服务停掉。

**免注册换来的方便，也意味着没有账号体系。** 这套工具适合朋友之间算清楚账，不适合放敏感的账目。

---

原文：<https://www.passnat.com/posts/spliit-nas-docker-shared-expenses-split-bills>


## 相关文章

- [SQLite Web数据库管理工具极简部署指南 NAS内网穿透在线查询编辑](sqlite-web-database-management-tool-minimal-deployment-guide-nas-intranet-penetration-online-query-editing.md)
