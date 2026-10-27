---
title: "Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署 Mathesar + PostgreSQL：数据库里的表直接变成网页表格，不懂 SQL 也能筛选、排序、录入；自带建库建表界面，按 PostgreSQL 角色给家人分配只读权限。用帕斯内网穿透拿公网地址，在外面也能打开。含完整编排、首次建管理员和权限设置步骤。"
canonical: https://www.passnat.com/posts/mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration
---

## 项目介绍

Mathesar 是一个开源的数据库网页界面。它在 PostgreSQL 和普通浏览器之间加了一层：数据库里的表会直接显示成能点、能排序、能筛选的网页表格，不懂 SQL 的家人打开浏览器就能看和改。在界面上点几下就能新建数据库、新建表和列、录入数据，也能对已有的库做筛选、分组和导出。

它适合把家里的物品、保修、书籍、收藏这类信息集中放在 NAS 上，再按人分配权限：有人只能看，有人能改。数据库本身还是标准 PostgreSQL，以后想用别的工具接也随时能接。项目地址：[点我跳转](https://github.com/mathesar-foundation/mathesar)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Mathesar 和 PostgreSQL 用 Docker Compose 装起来。编排里有一项配置要填访问地址（DOMAIN\_NAME），所以先穿透、后部署，装完就能直接用公网地址打开。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8000（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来，下一步要把它填进编排。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 mathesar，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: postgres:17
    restart: unless-stopped
    environment:
      POSTGRES_DB: mathesar_django
      POSTGRES_USER: mathesar
      POSTGRES_PASSWORD: change-me-db
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U mathesar -d mathesar_django"]
      interval: 5s
      timeout: 5s
      retries: 20
  mathesar:
    image: mathesar/mathesar:latest
    restart: unless-stopped
    ports:
      - "8000:8000"
    environment:
      POSTGRES_DB: mathesar_django
      POSTGRES_USER: mathesar
      POSTGRES_PASSWORD: change-me-db
      POSTGRES_HOST: db
      POSTGRES_PORT: "5432"
      SECRET_KEY: change-me-secret
      ALLOWED_HOSTS: "*"
      DOMAIN_NAME: "http://nas.local:8000"
      DEBUG: "false"
      MATHESAR_INSTANCE_NAME: "Mathesar"
    depends_on:
      db:
        condition: service_healthy
volumes:
  pgdata:
```

这段编排里要改三处：

**change-me-db**：数据库口令，出现两次（数据库容器和应用容器），两处要一致。

**change-me-secret**：Django 的密钥，换成一串够长的随机字符，别用默认值。

**DOMAIN\_NAME**：把 http://nas.local:8000 整个换成你刚才拿到的公网地址（带 http:// 和端口）。这个值用来生成页面里的地址，装好之后再改需要动环境变量并重启，建议一次填对。

端口 8000 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，第一次会拉取 Mathesar 和 PostgreSQL 两个镜像，数据库健康检查通过之后应用才会启动，通常十几秒就能打开。

### 三、第一次打开：建管理员

浏览器打开你的公网地址，会自动跳到登录页；第一次没有任何账号时，再跳到「Complete Installation」页面。在这里填管理员用户名和两次密码（至少 8 位），下面两个「分享匿名数据」的勾选项按自己意愿处理，然后点「Complete Installation」。这一步不需要邮箱、不需要邮件验证，提交完直接进主界面。

主界面第一次是空的，提示「No Databases Connected」。Mathesar 管的是 PostgreSQL 数据库，点「Connect Database」，选「Create a New Database」，它会用容器里这个 PostgreSQL 服务新建一个库。数据库名填 home\_inventory，想要中文显示名就选「Use a custom nickname instead」，填「家庭物品台账」。下面的示例数据（Bike Shop、Movie Rentals 之类）是英文虚构数据，自己建表的话一个都不要勾，直接「Create Database」。

### 四、建一张自己的表

点进新库，进入 public schema，点「New Table」，选「From Scratch」。表名填「家庭物品」，说明写「家里常用物品、存放位置和保修到期提醒」，主键用默认的 id，点下一步。

接着加列：在表格右上角点「New Column」，填列名、选类型，再点「Add」。这张表用了六列：物品名称（Text）、类别（Text）、数量（Number）、存放位置（Text）、保修截止（Date）、已借出（Boolean）。

录数据点左下角「New Record」，双击单元格进入编辑，输完按回车。同一行里可以继续双击别的格子。布尔列直接点复选框。数据会即时保存到 PostgreSQL，刷新页面还在。

右上角的「Filter / Sort / Group」可以对任意列做筛选、排序和分组；「Export」能把当前表导出成 CSV，想拿到 Excel 里继续算也没问题。

### 五、给家人分配只读权限

Mathesar 的权限跟着 PostgreSQL 角色走。先进「Database Settings → Roles」，点「Create Role」，角色名填 family\_viewer，勾上 LOGIN 并设一个密码，点「Create Role」。

回到「家庭物品」表，点右侧的「Table Permissions」，在 Granted Privileges 里点「Add Role(s)」，选 family\_viewer，权限保持默认的 Read，点「Save」。这样这个角色对这张表只有读权限，没有改数据的权限；想让人能编辑，就在同一个下拉里换成更高的权限。

数据库口令、角色密码都只存在编排文件和你自己的记录里，不要写进博客或截图。

### 几点说明

**数据在哪。** PostgreSQL 的数据在 pgdata 这个具名卷里，备份这个卷就够了；Mathesar 自己的上传文件默认在容器内，长期使用可以再给它挂一个目录。

**邮件是可选的。** 不配 SMTP 也能建管理员、建库、建表、分配权限，全部功能可用；只是没有邮件通知和找回密码邮件，忘记密码需要管理员在后台处理。

**明文 HTTP。** 内网穿透给的是 http 地址，Mathesar 在明文 HTTP 下登录和编辑都正常，不需要 HTTPS 才能用；但公网地址谁拿到都能打开登录页，别到处发，用完可以在插件里把隧道关掉。

**界面语言。** 当前版本界面是英文（安装页的语言下拉只有英、西、法、日），表名、列名和数据可以写中文，表格和录入都不受影响。

**升级。** 镜像标签用的是 latest，升级前先备份 pgdata 卷，再到 Docker 里重新拉取镜像、重启项目；大版本升级前先看官方发布说明。

---

原文：<https://www.passnat.com/posts/mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration>
