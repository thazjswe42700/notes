---
title: "EspoCRM 客户管理：NAS Docker 部署 + 内网穿透"
date: 2026-10-18
description: "在 NAS 上用 Docker Compose 部署开源客户管理系统 EspoCRM：联系人、客户、潜在客户、商机放在一处，商机看板按阶段摆开，手机也能查。含完整编排、管理员初始化和帕斯内网穿透步骤。"
canonical: https://www.passnat.com/posts/espocrm-nas-docker-customer-followup-kanban-intranet-penetration
---

## 项目介绍

EspoCRM 是一个开源的客户管理系统。自己接单、做小生意、跑工作室都能用：联系人、客户、潜在客户、商机放在同一个地方，谁跟到哪一步、报了多少价、下一次什么时候回访，打开看板一眼就能看清。

它最直观的是商机看板：一条商机从左到右分别是预期、评价、提案、协商、成交，卡片上带着金额和预计成交日期。谈成一单就往右挪一格，哪一单卡住了，不用翻聊天记录。联系人的详情页下面还有一条信息流，每次聊到什么都记一句，换人接手也看得懂。

手机浏览器打开同一个地址就能用，在外面也能查客户资料。数据存在自己的 NAS 上。

项目地址：[点我跳转](https://github.com/espocrm/espocrm)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 EspoCRM 装上。顺序别反过来——安装的时候，站点地址就写进配置里了，之后再换地址要改配置、清缓存，比一开始就填对麻烦得多。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 espocrm，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去，再改三处：

```yaml
services:
  db:
    image: mariadb:11
    environment:
      MARIADB_ROOT_PASSWORD: change-me-db-password
      MARIADB_DATABASE: espocrm
      MARIADB_USER: espocrm
      MARIADB_PASSWORD: change-me-db-password
    volumes:
      - db_data:/var/lib/mysql
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 5s
      retries: 30

  espocrm:
    image: espocrm/espocrm:latest
    ports:
      - "8080:80"
    environment:
      ESPOCRM_DATABASE_HOST: db
      ESPOCRM_DATABASE_NAME: espocrm
      ESPOCRM_DATABASE_USER: espocrm
      ESPOCRM_DATABASE_PASSWORD: change-me-db-password
      ESPOCRM_ADMIN_USERNAME: admin
      ESPOCRM_ADMIN_PASSWORD: change-me-admin-password
      ESPOCRM_ADMIN_EMAIL: you@example.com
      # 换成你的访问地址，结尾不要带斜杠
      ESPOCRM_SITE_URL: "http://nas.local:8080"
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

volumes:
  db_data:
```

**ESPOCRM\_SITE\_URL**：填第一步拿到的公网地址，前面带上 http://，结尾不要带斜杠。

**两个口令**：ESPOCRM\_ADMIN\_PASSWORD 是后台管理员的口令，MARIADB\_ROOT\_PASSWORD、MARIADB\_PASSWORD、ESPOCRM\_DATABASE\_PASSWORD 这三处数据库口令要填同一个。都只用字母和数字。

**healthcheck** 那一段不用改，它只是等数据库准备好再启动应用；端口 8080 被占用的话，把冒号左边的数字换一个，隧道端口也跟着改。

勾上「创建项目后立即启动」，点确认，两个镜像下完就起来了。数据存在 db\_data 这个卷里，备份把它整个拷走就行。

### 三、第一次打开：建管理员

用公网地址打开，第一次会自动安装，装完跳到登录页。用编排里填的管理员用户名和口令登录，进去之后到「管理」→「设置」里把语言切成中文、时区改成你所在的位置、默认货币改成人民币。

这些都在后台点，不用改文件。

### 四、把客户和商机用起来

先建「客户」，再在客户下面建「联系人」，把电话、职位、邮箱记上。

有单子在谈，就建一条「商机」：填金额、预计成交日期和阶段。商机列表右上角可以切到看板视图，卡片按阶段分列摆开，谈成一单直接拖到右边。

约好的回访记成「任务」，填上截止时间，首页的「我的活动」里就会按时间排好。联系人详情页下面可以写评论，每次沟通的结论记一条，信息流会按时间串起来。

### 几点说明

**没有配置发信邮箱。** 不配 SMTP 也能正常用上面的功能，只是系统不会替你发提醒邮件；需要邮件提醒再到设置里填 SMTP。

**改了公网地址。** 隧道删了重建、地址变了，要改 ESPOCRM\_SITE\_URL 再重启项目，不然页面里的链接还是旧地址。

**更新。** 镜像标签用的是 latest，升级之前把 db\_data 卷备份一份，再到 Docker 里重新拉取镜像、启动项目。

---

原文：<https://www.passnat.com/posts/espocrm-nas-docker-customer-followup-kanban-intranet-penetration>


## 相关文章

- [Dolibarr 小店管理：NAS Docker 部署 + 内网穿透](dolibarr-nas-docker-crm-quotes-inventory-intranet-penetration.md)
- [Paperless-ngx 自建家庭电子档案馆：飞牛 NAS Docker 部署中文 OCR 文档管理，内网穿透在外面拍张账单就归档](paperless-ngx-nas-document-management-ocr-docker-deployment-intranet-penetration.md)
- [Mathesar 数据库变网页：NAS Docker 部署 + 内网穿透](mathesar-nas-docker-database-web-table-family-inventory-intranet-penetration.md)
- [Spug 运维平台：NAS Docker 部署 + 内网穿透](spug-nas-docker-ops-panel-intranet-penetration.md)
- [Beszel 主机监控：NAS Docker 部署 + 内网穿透](beszel-nas-docker-host-monitoring-dashboard-intranet-penetration.md)
