---
title: "Sure 个人财务账本：NAS 上 Docker 部署，账户收支和预算放一起"
date: 2026-10-20
description: "用 NAS 上的 Docker Compose 部署开源个人财务应用 Sure：手工建账户、记收支，首页看现金流和支出分类，报表看月度结余和储蓄率，配帕斯内网穿透出门也能用手机翻账本。"
canonical: https://www.passnat.com/posts/sure-personal-finance-nas-docker-accounts-cashflow-intranet-penetration
---

## 项目介绍

Sure 是一个开源的个人财务应用，可以把自己的账户、收支流水、分类和预算放进同一个账本里。它把「账上还剩多少」和「这个月钱花在哪」放在一个页面上：总资产、现金流、支出分类、月度报表都能直接看。

这一期只用它最基础、也最可靠的部分：手工建账户、手工记流水，所有数据都存在自己的 NAS 上。不接银行、不连外部服务，账本完全由自己维护。

项目地址：[点我跳转](https://github.com/we-promise/sure)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿一个公网地址，再用 Docker Compose 把 Sure 装上。编排本身不用填公网地址，但出门要用手机看账本，隧道提前准备好会省事。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 3000（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 sure，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  web:
    image: ghcr.io/we-promise/sure:stable
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment: &rails_env
      SELF_HOSTED: "true"
      SECRET_KEY_BASE: "换成你自己的随机字符串"
      DISABLE_SSL: "true"
      RAILS_FORCE_SSL: "false"
      DB_HOST: db
      DB_PORT: "5432"
      POSTGRES_USER: sure_user
      POSTGRES_PASSWORD: "换成你自己的数据库口令"
      POSTGRES_DB: sure_production
      REDIS_URL: redis://redis:6379/1
    volumes:
      - app-storage:/rails/storage
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy

  worker:
    image: ghcr.io/we-promise/sure:stable
    command: bundle exec sidekiq
    restart: unless-stopped
    environment: *rails_env
    volumes:
      - app-storage:/rails/storage
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy

  db:
    image: postgres:16
    restart: unless-stopped
    environment:
      POSTGRES_USER: sure_user
      POSTGRES_PASSWORD: "换成你自己的数据库口令"
      POSTGRES_DB: sure_production
    volumes:
      - postgres-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U sure_user -d sure_production"]
      interval: 5s
      timeout: 5s
      retries: 10

  redis:
    image: redis:latest
    restart: unless-stopped
    volumes:
      - redis-data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 5s
      retries: 10

volumes:
  app-storage:
  postgres-data:
  redis-data:
```

**SECRET\_KEY\_BASE**：换成你自己的随机字符串，长度至少 64 个字符。它用来签会话，换了之后原来的登录状态会失效。

**POSTGRES\_PASSWORD**：换成你自己的数据库口令，web、worker 和 db 三处要写成同一个值。

**DISABLE\_SSL**：隧道那头是 HTTP 时保持 true，不然浏览器会一直跳 HTTPS、打不开页面。

**volumes**：三个卷分别放应用文件、数据库和缓存。数据库卷里就是全部账目，备份把它拷走就行。

勾上「创建项目后立即启动」，点确认。首次启动要跑数据库迁移，大约半分钟到一分钟，期间页面打不开是正常的。

### 三、第一次初始化

浏览器打开公网地址，第一次会跳到注册页，填邮箱和密码建一个账号。第一个注册的账号就是管理员。

接着是三步初始化：第一步填名字、家庭名称和国家；第二步选语言、货币和日期格式（货币选人民币，日期选 YYYY-MM-DD 就好）；第三步问你想用 Sure 做什么，按自己的需要勾几项，点完成。

**默认分类要先建一次**：打开左侧的 Categories，点「Use defaults (recommended)」，一次生成餐饮、买菜、交通、房租、购物、水电等常用分类。不建也能记账，但报表里所有支出都会挤在「未分类」里。

### 四、手工建账户、记流水

回到首页，点「Add account」，先选账户类型：现金、投资、加密货币、房产、车辆、信用卡、贷款等。这里选现金，然后在「How would you like to add it?」里选 **Enter account balance**，填账户名称和当前余额，货币选人民币，保存。

要记一笔支出，点右上角的「New transaction」，页签留在 Expense，填描述、选账户、填金额、选分类和日期，点「Add transaction」。收入就切到 Income 页签，同样填一遍。房租、买菜、水电、工资，一条条录进去，首页的现金流和支出分类会跟着变。

**说明一下录入方式**：这一期不接任何外部账户，所有流水都是手工录入或从 CSV 导入。这样最省事，也最可控，适合先把账本跑起来。

### 五、看结果

首页的 Cashflow 会画出这个月的钱从哪来、到哪去：收入一根粗线，支出按分类分出去，右边剩下的是结余。下面的 Outflows 是支出分类的占比，房租、吃饭、购物各占多少一眼就能看出来。

左侧的 Reports 是月度报表：总收入、总支出、净结余、储蓄率，还有按月的对比表。Budgets 页把收入和支出按分类拆开，适合月底复盘。手机打开同一个地址，布局会自动变成竖版，底栏直接切页面。

### 几点说明

**数据在哪。** 账目在数据库卷里，应用文件在 app-storage 卷里。换机器时把这三个卷备份出来，重新起容器就能恢复。

**更新。** 重新拉取镜像、重启项目即可；升级前先把数据库卷备份一份。

**HTTPS。** 如果以后换成 HTTPS 隧道，把 DISABLE\_SSL 去掉，并按官方文档设置 RAILS\_ASSUME\_SSL。

---

原文：<https://www.passnat.com/posts/sure-personal-finance-nas-docker-accounts-cashflow-intranet-penetration>
