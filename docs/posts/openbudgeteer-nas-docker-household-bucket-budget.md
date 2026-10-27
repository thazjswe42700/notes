---
title: "OpenBudgeteer 自建家庭预算：NAS Docker 部署 + 桶预算 + 报表"
date: 2026-10-18
description: "开源的桶预算记账工具：Compose 三容器怎么起、账户和桶怎么建、工资怎么分配进桶、每笔支出怎么扣、报表怎么看。"
canonical: https://www.passnat.com/posts/openbudgeteer-nas-docker-household-bucket-budget
---

## 项目介绍

OpenBudgeteer 是一个开源的「桶预算」记账工具。做法是先把每个月的收入分进不同的桶——房租、吃饭、油费、长期储蓄、旅游基金，每花一笔钱就从对应的桶里扣。打开首页，每个桶还剩多少、这个月还能花多少，一眼就能看到。

它跟记流水账的思路不一样：不是月底了再回头分析钱花到哪去了，而是发工资那天先把每类钱的额度划好，花的时候心里就有数。

后端是 .NET，前端跑在浏览器里，官方用 Compose 起的是一组三件套：应用本体、MariaDB 数据库、Redis 缓存。

项目地址：[点我跳转](https://github.com/TheAxelander/OpenBudgeteer)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。三个容器，一份 Compose 编排就够了。

### 一、想在外面也能用，先把 8080 端口放出去

只在局域网里用，这一步可以跳过，直接看第二步。要让手机在外面也能打开，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地 IP 留默认的 127.0.0.1，本地端口填 **8080**，点开、按一下电源，状态变成运行中，详情里那个公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 openbudgeteer，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  openbudgeteer:
    image: axelander/openbudgeteer:latest
    container_name: openbudgeteer
    ports:
      - "8080:8080"
    environment:
      - CONNECTION_PROVIDER=mariadb
      - CONNECTION_SERVER=openbudgeteer-db
      - CONNECTION_PORT=3306
      - CONNECTION_DATABASE=openbudgeteer
      - CONNECTION_USER=openbudgeteer
      - CONNECTION_PASSWORD=换成你自己的口令
      - CONNECTION_ROOT_PASSWORD=换成你自己的口令
      - CONNECTION_REDIS_SERVER=openbudgeteer-redis
      - APPSETTINGS_CULTURE=zh-CN
      - APPSETTINGS_DEMO_DATA=false
    depends_on:
      - openbudgeteer-db
      - openbudgeteer-redis
  openbudgeteer-db:
    image: mariadb:11
    environment:
      MYSQL_ROOT_PASSWORD: 换成你自己的口令
    volumes:
      - db-data:/var/lib/mysql
  openbudgeteer-redis:
    image: redis:7-alpine
    volumes:
      - redis-data:/data
volumes:
  db-data:
  redis-data:
```

应用容器固定监听 8080，冒号前面那个数字是 NAS 上的端口，被别的项目占了就改它，后面的不用动。数据库口令出现在三个地方：应用的两个 **CONNECTION\_** 变量和数据库的 **MYSQL\_ROOT\_PASSWORD**，把它们换成同一个你自己的口令，三处必须一致。

**APPSETTINGS\_CULTURE** 决定金额和日期的格式，填 zh-CN 金额就显示成人民币符号；这个项目没有中文界面，界面文案还是英文。

**APPSETTINGS\_DEMO\_DATA** 打开会自带一套示例账本（两个账户、八笔收支、几个桶），第一次想先看看界面长什么样，可以临时改成 true，熟悉完再改回 false、清掉数据库卷重新起。

勾上「创建项目后立即启动」，点确认。三个镜像下载加上数据库初始化，第一次会等一会儿，容器状态都变成「运行中」就可以打开网页了。

### 三、第一次打开：建账户、建桶、分预算

浏览器打开 **http://NAS的局域网地址:8080**，没有登录页，直接就是账本首页。这个项目本身不带账号体系，打开就是全部权限。

第一步去左侧的 **Account** 建账户，比如一个「日常卡」、一个「储蓄卡」，把余额填进去。

第二步去 **Bucket** 建桶。建议先建两个组：一个是「日常开销」，里面放房租、吃饭、油费；另一个是「长期储蓄」，里面放备用金、旅游基金。每个桶设一个目标额度（Want），比如吃饭每月 3000。

第三步是桶预算的关键动作：发工资那天点 **Distribute Budget**，把收入分配进各个桶。分完之后，每个桶的余额就是「这一类还能花多少」。

平时花钱就在 **Transaction** 里记一笔：选账户、填收付方和金额、勾上这笔钱属于哪个桶。记完回桶页看，对应的桶余额就减下去了。

左侧的 **Report** 是报表：月度收支柱状图、账户余额曲线，还有按桶统计的花费。想看看家里钱的大致走向，看这一页就够了。

### 几点说明

**没有内置登录。** 这是部署前最该注意的一条：应用打开就是全部权限，挂到公网前一定在隧道那一层加上网页访问口令。

**数据在 MariaDB 的数据卷里。** 备份就是备份 db-data 这个卷；换机器时把它一起搬过去。

**界面是英文的。** 金额符号、日期格式跟着 APPSETTINGS\_CULTURE 走，填 zh-CN 就会显示人民币和中文日期。

**三个容器。** 比单容器应用多占一点内存和磁盘，好处是数据库独立，备份和迁移都清楚。

**更新。** 拉新镜像重建容器即可，数据库结构会在启动时自动迁移，动手前备份 db-data 更稳。

---

原文：<https://www.passnat.com/posts/openbudgeteer-nas-docker-household-bucket-budget>


## 相关文章

- [OpenGist 自建代码片段库：NAS Docker 部署 + 修订差异怎么看](opengist-nas-docker-snippet-share-revision-history.md)
- [Lute 自建外语阅读工具：NAS Docker 部署 + 生词状态怎么用](lute-nas-docker-foreign-language-reading-vocabulary.md)
- [个人财务管理系统BudgetBee，Docker一键部署，NAS极简配置，多账户记账工具](budget-bee-docker-deployment-nas-multi-account-accounting.md)
- [Donetick 自建家务清单：NAS Docker 部署 + 全家分工 + 积分](donetick-nas-docker-family-chore-list-points.md)
- [DocuSeal 自建电子签署：NAS Docker 部署 + 签署链接怎么发](docuseal-nas-docker-e-signature-contract.md)
