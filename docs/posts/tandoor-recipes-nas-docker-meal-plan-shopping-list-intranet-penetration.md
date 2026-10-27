---
title: "Tandoor Recipes：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源菜谱管理 Tandoor Recipes：菜谱写清份量和每样配料，一周菜单排进日历，采购单按菜单汇总成一张；用帕斯内网穿透拿公网地址，在外面也能翻菜谱、勾采购单。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/tandoor-recipes-nas-docker-meal-plan-shopping-list-intranet-penetration
---

## 项目介绍

Tandoor Recipes 是一个开源的菜谱管理工具，解决的问题是「菜谱存得到处都是，真要做的时候还得翻半天」。它把菜谱拆成结构化的东西：每样配料有数量、单位和食物名，做法按步骤写，一条菜谱标好做几份。好处是后面排菜单、算采购的时候，这些数字能直接用上。

围绕菜谱还有几块：用餐计划是一个日历，按周或按月看，早中晚餐都能往里排；购物清单按菜单把要买的东西汇总成一张，买一样勾一样；储物间记家里现在有什么；关键词和搜索能把菜谱按食材、菜系找出来；烹饪手册则可以把菜谱整理成一本一本。

份量是它和普通笔记最不一样的地方：一条菜谱写清楚是几份、每样配料多少，排进菜单时填这次做几份，采购单会按菜单里的份数把数量合起来，不用自己拿计算器加。

界面自带简体中文，支持多用户，可以建家庭让家里人共享菜单和采购单。它也能从网站导入菜谱，但本文只用自己写的菜谱，不依赖任何外部抓取。项目地址：[点我跳转](https://github.com/TandoorRecipes/recipes)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Tandoor Recipes 用 Docker Compose 装起来。先穿透、后部署，是因为编排里有一行要填公网地址，装完直接用这个地址打开，在外面也能翻菜谱、勾采购单。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3718（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来，下一步要用。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 tandoor，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db_recipes:
    restart: always
    image: postgres:16-alpine
    volumes:
      - ./postgresql:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: tandoor
      POSTGRES_PASSWORD: 换成你自己的口令
      POSTGRES_DB: djangodb
  web_recipes:
    image: vabene1111/recipes:latest
    restart: always
    depends_on:
      - db_recipes
    ports:
      - "3718:80"
    volumes:
      - ./staticfiles:/opt/recipes/staticfiles
      - ./mediafiles:/opt/recipes/mediafiles
    environment:
      SECRET_KEY: 换成一串你自己的随机字符
      DB_ENGINE: django.db.backends.postgresql
      POSTGRES_HOST: db_recipes
      POSTGRES_PORT: "5432"
      POSTGRES_USER: tandoor
      POSTGRES_PASSWORD: 换成你自己的口令
      POSTGRES_DB: djangodb
      ALLOWED_HOSTS: "*"
      CSRF_TRUSTED_ORIGINS: "https://你的公网地址"
      TZ: Asia/Shanghai
      TIMEZONE: Asia/Shanghai
```

这段编排里有三处要改成你自己的：

**数据库口令**：POSTGRES\_PASSWORD 出现两次（数据库容器和应用容器各一处），必须换成同一串，字母数字混着来。

**SECRET\_KEY**：换成一串你自己的随机字符，长度二三十位就够。

**CSRF\_TRUSTED\_ORIGINS**：换成上一步拿到的公网地址，https:// 开头，后面不要带路径。这一行是让登录表单认这个地址，填错了登录会报校验失败。

端口 3718 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」：数据库先起，应用容器启动时要跑一遍数据库迁移，第一次大约两三分钟，这期间打开地址可能是 502，等它跑完再刷新。

数据都在项目目录下的三个文件夹里：postgresql 是数据库，staticfiles 是静态文件，mediafiles 是上传的菜谱图片，备份这三个文件夹就够了。

### 三、第一次打开：建管理员

用公网地址打开，第一次会跳到安装页：填用户名、密码、确认密码，点「创建超级用户账号」。建完会回到登录页，用刚建的账号登录，就进到主界面了。

界面默认就是简体中文，侧栏是 Home、搜索、用餐计划、购物、导入、储物间、烹饪手册、数据库和设置。

### 四、建第一条菜谱

**先建菜谱。** 首页点「创建食谱」，食谱页填名字、描述、份量（比如 4 份）和工作时间；切到「步骤」页，每一步下面是一行行配料，填数量、单位、食物，再写这一步的做法。单位（g、ml、个、根）和食物第一次输入时下拉里选「创建」就行，以后再输入会自动补全。

**步骤可以有多步。** 一步里放几样配料、写一段说明，加一步就点底部的加号。全部写完点右下角「创建」，菜谱就存下来了。详情页会把配料按步骤列出来，数量、单位、食物一栏栏对齐。

### 五、排一周菜单、生成采购单

左侧进「用餐计划」，默认是周视图，七列是周一到周日。点某一天的格子，选一条菜谱，填这次做几份、选用餐类型（早/午/晚餐），保存；一天可以排多餐，换一天接着排，一周的晚饭很快就排满了。

排好之后进「购物」，采购单会按菜单里的菜谱和份数，把一周要买的配料汇总成一张：面条多少克、番茄几个、鸡肉多少克，去超市买一样勾掉一样。份数排得多的菜，数量也会跟着合进去。

菜单和采购单都能随时改：哪天的菜不想吃了，在用餐计划里删掉那一条，采购单重新汇总一次就行。

### 六、找菜谱、做整理

顶部搜索框或者 Ctrl+K 可以直接搜菜名和配料，比如输入「番茄」就能把用到番茄的菜找出来。关键词可以给菜谱打标签（家常、快手、汤），搜的时候按标签筛。

「烹饪手册」把菜谱按主题整理成册，适合把一类菜放在一起；「储物间」记家里现在有哪些食材，做采购的时候心里有数。

### 几点说明

**数据在哪。** 数据库在 postgresql 文件夹里，上传的图片在 mediafiles 里，静态文件在 staticfiles 里，三个一起备份。换机器时把整个项目目录带走，编排原样贴回去就能恢复。

**升级。** 镜像标签用的是 latest，升级前先把三个文件夹备份一份，再到 Docker 里重新拉取镜像、重启项目。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Tandoor 本身不需要 HTTPS 才能用；但它存的是自己家的菜谱和菜单，公网地址别到处发，用完了可以在插件里把隧道关掉。

**一家人一起用。** 多人的时候在设置里建一个家庭，把账号加进同一个家庭，用餐计划和采购单就能共享；这篇只写从零用起来这条路，权限和家庭等熟了自己再摸。

**关于导入。** 它的「导入」可以从网站抓菜谱，但本文不涉及，所有演示菜谱都是自己写的；想用导入功能的话，建议只导入自己有权使用的内容。

---

原文：<https://www.passnat.com/posts/tandoor-recipes-nas-docker-meal-plan-shopping-list-intranet-penetration>


## 相关文章

- [ReDoc 接口文档：NAS Docker 部署 + 内网穿透](redoc-api-docs-nas-docker-openapi-intranet-penetration.md)
- [Swagger Editor：NAS Docker 部署 + 内网穿透](swagger-editor-nas-docker-openapi-api-docs-intranet-penetration.md)
- [Dolibarr 小店管理：NAS Docker 部署 + 内网穿透](dolibarr-nas-docker-crm-quotes-inventory-intranet-penetration.md)
- [Standard Notes 私密笔记自建 - 端到端加密开源方案，NAS 极简部署指南](standard-notes-private-notes-self-hosted-end-to-end-encryption-open-source-nas-simple-deployment-guide.md)
- [自建 Dockhand 容器面板：飞牛 NAS Docker 部署 + 内网穿透](dockhand-docker-management-panel-nas-deployment-intranet-penetration.md)
