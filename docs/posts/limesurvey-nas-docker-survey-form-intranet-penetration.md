---
title: "LimeSurvey 自建问卷：NAS Docker 部署 + 内网穿透"
date: 2026-10-18
description: "收报名、做调查不用把题目交给外面的平台：在 NAS 上用 Docker Compose 部署开源问卷系统 LimeSurvey，用帕斯内网穿透拿公网地址，发链接朋友手机就能填，统计图表自动出，数据都在自己手里。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/limesurvey-nas-docker-survey-form-intranet-penetration
---

## 项目介绍

LimeSurvey 是一个老牌的开源问卷系统。想收报名、做调查、收集家人朋友的想法，不用把题目交给外面的问卷平台：问卷建在自己的 NAS 上，题目想加几道加几道，谁填了、每道题选了什么，数据都在自己手里。

它支持单选、多选、打分、排序、短文本、长文本这些常见题型。问卷建好后是一条公开链接，填的人不用注册、不用装 App，手机上点开就能填。每交一份，后台的统计页就自动多一段图表，哪个选项最多一眼就能看出来。

项目地址：[点我跳转](https://github.com/LimeSurvey/LimeSurvey)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 LimeSurvey 装上。

为什么穿透要放在部署前面？LimeSurvey 有个安全机制：管理后台第一次从哪个地址打开，它就把哪个地址记成「信任域名」，之后从别的域名访问会被它拒掉。所以先把公网地址拿到手，装完第一次就从这个地址进去，后面发给朋友填的链接才顺。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留它默认的 127.0.0.1，本地端口填 8080（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 limesurvey，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  limesurvey:
    image: martialblog/limesurvey:latest
    ports:
      - "8080:8080"
    environment:
      DB_TYPE: mysql
      DB_HOST: db
      DB_PORT: "3306"
      DB_NAME: limesurvey
      DB_USERNAME: limesurvey
      DB_PASSWORD: 换成你自己的数据库口令
      ADMIN_USER: admin
      ADMIN_NAME: Administrator
      ADMIN_EMAIL: admin@example.com
      ADMIN_PASSWORD: 换成你自己的管理员口令
    volumes:
      - limesurvey_upload:/var/www/html/upload
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: mariadb:11
    environment:
      MYSQL_DATABASE: limesurvey
      MYSQL_USER: limesurvey
      MYSQL_PASSWORD: 换成你自己的数据库口令
      MYSQL_ROOT_PASSWORD: 换成你自己的root口令
    volumes:
      - limesurvey_db:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 5s
      retries: 12
    restart: unless-stopped

volumes:
  limesurvey_upload:
  limesurvey_db:
```

**三个口令**：数据库口令两处（limesurvey 的 DB\_PASSWORD 和 db 的 MYSQL\_PASSWORD）填同一个，只用字母和数字；MYSQL\_ROOT\_PASSWORD 随便换一串；ADMIN\_PASSWORD 是管理员的登录口令，这三个都换成你自己的。管理员用户名默认 admin，也可以改 ADMIN\_USER。

镜像用的是社区维护的打包版（[martialblog/docker-limesurvey](https://github.com/martialblog/docker-limesurvey)），里面已经把 PHP、Apache 和 LimeSurvey 本体装好，管理员账号在第一次启动时按编排里的环境变量自动创建，不用走安装向导。勾上「创建项目后立即启动」，点确认，两个镜像下完就起来了。

### 三、用公网地址打开后台、切成中文

浏览器打开第一步的公网地址，后面加上 /admin，用户名 admin、密码就是编排里的 ADMIN\_PASSWORD。

进去先切中文：右上角头像旁边的「配置」进「全局设置」，在「语言」标签里把默认语言和管理员语言都选成「简体中文」，保存。

如果你是从局域网地址先进的后台，公网域名打开会提示不能访问——这就是开头说的信任域名机制，进容器把 application/config/allowed\_hosts.php 里加上你的公网域名（或者删掉这个文件重启）就好。第一次就用公网地址进，就不会遇到。

### 四、建第一份问卷

点左上角的加号新建问卷。问卷标题和欢迎语在「欢迎页面」那一块直接点一下就能改，改完会自动保存。

在题组的横线上点加号加题：题型面板里「单项选择」下的「列表（单选）」是单选题，「多选题」是可多选，「文本」下的「简短文本」用来填名字。每道题的题干和选项都是点上去直接改，右侧的问题设置里还能切换题型、设必答。

题目加完点右上角的「激活」，问卷就上线了。切到「分享」标签能看到问卷链接，它就是用你的公网地址拼出来的，发到群里就行。

### 五、朋友填、你看统计

朋友在手机上点开链接，看到的是欢迎页，点「下一页」开始，勾选项、填名字、点「提交」，完事。全程不用注册。

你这边打开问卷的「结果 → 统计」，每道题自动一张图：单选是各选项的占比条形图，右上角能切成饼图，还能导出。「Responses list」里是每一份原始回答，一条条看得见。

### 几点说明

**数据在哪。** 问卷、题目、回答都在 limesurvey\_db 这个卷里，上传的图片附件在 limesurvey\_upload，备份就把这两个卷拷走。

**换域名要改信任列表。** 隧道删了重建、公网地址变了，新域名打开会被拒，按第三节的办法把 allowed\_hosts.php 改一下。

**更新。** 镜像标签用的是 latest，升级之前先备份数据卷，再到 Docker 里拉取新镜像、重新启动项目。

---

原文：<https://www.passnat.com/posts/limesurvey-nas-docker-survey-form-intranet-penetration>


## 相关文章

- [开源问卷调查系统LimeSurvey，NAS一键部署，内网穿透远程访问，无限问卷模板](open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates.md)
- [SurveyKing开源问卷考试系统一键部署指南 无限题型支持NAS私有部署免费使用](survey-king-open-source-questionnaire-exam-system-one-click-deployment-guide-unlimited-question-types-nas-private-deployment-free.md)
- [Reactive Resume 自建简历工作台：飞牛 NAS 部署与公网使用](reactive-resume-fnos-private-resume-builder-passnat.md)
- [Leantime 项目管理：NAS Docker 部署 + 内网穿透](leantime-nas-docker-project-management-goals-timesheets-intranet-penetration.md)
- [LibrisLog 读书清单：NAS Docker 部署 + 内网穿透](librislog-nas-docker-reading-list-progress-reviews-intranet-penetration.md)
