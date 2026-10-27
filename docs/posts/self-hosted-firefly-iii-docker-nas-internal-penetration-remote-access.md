---
title: "Firefly III 自建家庭账本：NAS Docker 部署、日常记账和月底报表"
date: 2025-10-16
description: "Firefly III 家庭记账工具：两个容器一段 Compose 起在自己 NAS，记一笔支出、设每月预算、月底自动出分类报表；配内网穿透在外面用手机也能记。"
canonical: https://www.passnat.com/posts/self-hosted-firefly-iii-docker-nas-internal-penetration-remote-access
---

## 项目介绍

Firefly III 是一个跑在自己 NAS 上的记账工具：每花一笔钱记一句，月底自动按分类、按账户、按预算出报表。钱花在哪儿、这个月还剩多少预算，打开首页就能看见。

它按复式记账的思路记账：钱从哪个账户出去、进了哪个账户、属于什么分类、算在哪项预算里，都分开记。日常用的时候不用管这些术语，填金额、写一句描述、选个分类就行。

常用的东西它都有：多个账户和钱包、分类和标签、每月预算上限、按月的收支曲线、分类支出饼图、导入导出。数据全部存在自己 NAS 的数据库里，不经过任何第三方。

项目地址：[点我跳转](https://github.com/firefly-iii/firefly-iii)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示。它需要一个数据库，所以是「应用 + MariaDB」两个容器，一段 Compose 编排就够了。

### 一、想在外面记账，先把 3524 放出去

只在局域网里用，这一步可以跳过，直接看第二步。要让手机在外面用流量也能打开，就在飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」：名字随便起（不超过 12 个字符），本地地址留默认的 **127.0.0.1**，本地端口填 **3524**，点开、按一下电源，状态变成运行中，详情里那串公网地址就是你在外面要打开的网址。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 firefly，路径在「我的文件」下面挑一个空文件夹，来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  firefly:
    image: fireflyiii/core:latest
    container_name: firefly
    restart: unless-stopped
    ports:
      - "3524:8080"
    environment:
      APP_KEY: "changeme0123456789abcdef01234567"
      APP_URL: "http://nas.local:3524"
      APP_ENV: production
      DB_CONNECTION: mysql
      DB_HOST: db
      DB_PORT: 3306
      DB_DATABASE: firefly
      DB_USERNAME: firefly
      DB_PASSWORD: "换成你自己的口令"
      TRUSTED_PROXIES: "**"
      TZ: Asia/Shanghai
    volumes:
      - upload:/var/www/html/storage/upload
    depends_on:
      db:
        condition: service_healthy

  db:
    image: mariadb:11
    container_name: firefly-db
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD: "换成你自己的口令"
      MARIADB_DATABASE: firefly
      MARIADB_USER: firefly
      MARIADB_PASSWORD: "换成你自己的口令"
      TZ: Asia/Shanghai
    volumes:
      - db:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 5s
      retries: 20

volumes:
  upload:
  db:
```

套用之前改三处：

**① APP\_KEY** 必须是 32 个字符的随机字符串，示例里那串只是占位，换成自己的；

**② 两个 DB\_PASSWORD** 和 **MARIADB\_ROOT\_PASSWORD** 换成你自己的数据库口令（三处建议写成同一个），改完以后不要随便改；

**③ APP\_URL** 换成你实际打开的地址：只在局域网用就填 **http://NAS的局域网地址:3524**；如果第一步建了隧道、要在公网打开，就填隧道详情里那串公网地址。

端口映射那行 **"3524:8080"**，冒号前面是 NAS 上的端口，被别的项目占了就改它，后面的 8080 是容器内的端口，不用动。数据放在两个命名卷里：**db** 存全部账目，**upload** 存附件；备份就备份这两个卷。

勾上「创建项目后立即启动」，点确认。第一次启动要先建数据库表，在这台 NAS 上大约要三到四分钟，这期间网页打不开是正常的，等容器健康检查变成 healthy 再打开。

### 三、第一次打开：注册管理员，走完向导

浏览器打开 **http://NAS的局域网地址:3524**，会跳到登录页，点「注册」。

第一个注册的账号自动就是管理员。注册只要邮箱和密码，**不需要配 SMTP，也不用收验证码**。

登录后会进首次向导：填你常用的银行卡名字、现在的余额、储蓄余额，语言选「Chinese Simplified」。提交以后它会自动建好三个账户：这张主卡、一个储蓄账户、一个现金钱包。

想关掉注册，就到「设置 → 用户管理」把注册关掉，或者直接在编排里只留自己一个账号。

### 四、日常怎么记

左上角「交易 → 支出」，填四样东西：描述（比如「周末买菜」）、来源账户（家庭银行卡）、目标账户（菜市场、超市、加油站这类收款方，第一次填完以后会出现在下拉里）、金额和日期。

下面可以再选分类和预算：分类用来出报表（吃饭 / 交通 / 日用品 / 孩子 / 健康 / 娱乐），预算用来卡每月上限（比如「家庭生活」1800、「交通出行」800）。

点提交，这笔就进账了。收入走「交易 → 收入」，转账（比如从银行卡取现金到钱包）走「交易 → 转账」。

### 五、月底看报表

首页会自己长出几张图：本期收入与支出、净资产、账户余额曲线、预算用了多少、分类支出柱状图、每个支出账户花了多少。

要看更细的，点「报表」：默认财务报表给出余额曲线、收入 vs 支出、支出排名、预算进度和分类汇总；分类报表要先选资产账户和分类，再提交，出来的是分类支出饼图和每个分类的逐日柱状图。

这些图不用自己画，记的每一笔都带着日期、分类和预算，月底直接看。

### 六、把地址发给家里人

如果第一步建了隧道，在手机上用流量打开公网地址就能记账，不用连家里的 WiFi。

想让两个人一起记，就在「设置 → 用户管理」里再开一个账号，各记各的、看同一份账；不想开注册也可以先把注册打开、建完账号再关掉。

### 几点说明

**货币。** 镜像默认货币是欧元。要人民币，到「货币」页把 Chinese yuan 启用，再到个人偏好里把它设成默认；之后新建账目就是 ¥。

**启动慢是正常的。** 第一次启动要跑数据库迁移，三到四分钟；之后重启只要几秒。

**数据在两个卷里。** 备份 **db** 和 **upload** 两个卷就是全部账目和附件；删掉它们等于清空实例，升级前先备份。

**公网访问先设好账号。** Firefly III 自己有登录，但公网地址谁拿到都能看到登录页；建完管理员再按隧道电源，别让一个空的注册页在公网上放着。

**端口冲突。** 宿主 3524 被占了，就改编排里冒号前面那个数字，容器里的 8080 不用动。

**更新。** 拉新镜像重建容器即可，两个数据卷不动；升级前先备份。

---

原文：<https://www.passnat.com/posts/self-hosted-firefly-iii-docker-nas-internal-penetration-remote-access>


## 相关文章

- [OpenBudgeteer 自建家庭预算：NAS Docker 部署 + 桶预算 + 报表](openbudgeteer-nas-docker-household-bucket-budget.md)
