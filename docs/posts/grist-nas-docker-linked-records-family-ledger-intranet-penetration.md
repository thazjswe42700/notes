---
title: "Grist 家庭台账：NAS Docker 部署 + 内网穿透"
date: 2026-10-20
description: "在 NAS 上用 Docker Compose 部署开源表格数据库 Grist：用启动密钥初始化管理员，建家庭成员和支出明细两张表，经手人用 Reference 关联到人，Access Rules 控制谁能看谁能改；用帕斯内网穿透拿公网地址，家人手机上也能查。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/grist-nas-docker-linked-records-family-ledger-intranet-penetration
---

## 项目介绍

Grist 是一个把电子表格和数据库合在一起的开源工具。表面上还是网格：一行一条记录、一列一个字段；底下每张表都有明确的列类型，文本、日期、数字分得清，不会把日期当文字排序。

它比普通表格多出来的，是表跟表之间的关系。比如记家庭开支，一张「家庭成员」表放姓名、关系、每月预算；一张「支出明细」表放项目、日期、金额。支出明细里加一列「经手人」，类型选 Reference，指向家庭成员表，单元格里显示的就是那个人的名字，点开还能跳到对应的记录。查账时按经手人一筛，谁花了多少一目了然，不用在几张表之间来回对，也不会把名字写错。

权限也是内置的：可以给文档设协作者角色，也可以用 Access Rules 写更细的规则——谁能看、谁能改、谁能增删，按角色和条件分开控制。数据都在自己的 NAS 上，文档存在一个卷里。

项目地址：[点我跳转](https://github.com/gristlabs/grist-core)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 Grist 装起来。Grist 的服务器地址可以自动探测，用公网地址打开就按公网地址记，家里人后面用同一个地址就能进。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 8484（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址，先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 grist，路径在「我的文件」下面挑一个空文件夹。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  grist:
    image: gristlabs/grist:latest
    container_name: grist
    restart: unless-stopped
    ports:
      - "8484:8484"
    environment:
      # 会话签名密钥：换成你自己的长随机串（改一次所有登录态失效）
      GRIST_SESSION_SECRET: "换成你自己的长随机字符串"
      # 首次初始化管理员的钥匙：浏览器打开 /boot 输入它，再填管理员邮箱
      GRIST_BOOT_KEY: "换成你自己的启动密钥"
      # 只开一个组织，界面更简单
      GRIST_SINGLE_ORG: family
      # 强制登录：未登录访客不能以默认用户身份进入
      GRIST_FORCE_LOGIN: "true"
      # 隐藏计费 / 升级 / 帮助等入口
      GRIST_HIDE_UI_ELEMENTS: "billing,sendToDrive,supportGrist,helpCenter,newsletter,multiSite,templates"
    volumes:
      - grist-data:/persist
volumes:
  grist-data:
```

逐行说明：

**GRIST\_SESSION\_SECRET**：会话签名密钥，换成一串自己的长随机字符；改一次，所有已登录的会话失效。

**GRIST\_BOOT\_KEY**：首次初始化管理员的启动密钥，换成自己的，不要用示例值。

**GRIST\_SINGLE\_ORG**：只开一个组织，界面更简单；名字可以按自己家改。

**GRIST\_FORCE\_LOGIN**：强制登录。这一行很重要——不开的话，没登录的访客会直接以默认用户身份进来，相当于把文档敞开在公网上。

**GRIST\_HIDE\_UI\_ELEMENTS**：把计费、升级、帮助中心这些入口隐藏掉，界面更干净。

**grist-data:/persist**：文档数据都存在这个具名卷里，备份它一个就够。

勾上「创建项目后立即启动」，点确认。单容器，一般十几秒就起来了，日志里出现「Grist is available at」就可以访问。

### 三、第一次打开：初始化管理员

用公网地址打开，会跳到 boot 页，要求输入启动密钥。填入编排里 GRIST\_BOOT\_KEY 那一行的值，点检查；通过之后填管理员邮箱，这个邮箱就是以后的管理员账号。

接着会进 Quick setup：确认服务器地址（用公网地址打开时它会自动探测，确认无误即可）、选社区版、点继续。这几步只做一次，之后打开就是正常的文档列表。

**关于登录。** 强制登录打开之后，未登录的访客进不来。Grist 社区版的登录走邮箱链接，发信需要在环境变量里配 SMTP（GRIST\_SMTP\_HOST 等）；不配 SMTP 时，管理员先在本机用启动密钥进入，再按需要给协作者分配角色。邮件邀请这一步本文没有演示。

### 四、建表：关联字段怎么连

新建一个空白文档，左侧默认有一张 Table1。把页面改名为「家庭成员」，三列分别改成：姓名（文本）、关系（文本）、每月预算（数字），填入几行中性数据，比如爸爸 / 本人 / 1200。

点左下「Add new」→「Add page」，控件选 Table，数据选「New Table」，建第二页，改名「支出明细」。列设成：项目（文本）、日期（Date）、金额（数字）。

**关联字段：**再加一列，列类型选 Reference，「Data from table」选家庭成员，「Show column」选姓名。这样每条支出后面显示的就是具体的人，而不是一串编号；点单元格还能打开对应的记录。

查账时按经手人一筛，或者把金额列一汇总，谁花了多少、这个月花了多少，都在一张表里。

### 五、权限：Access Rules

左侧 TOOLS 里点「Access Rules」，第一次进去点「Enable Access Rules」。启用之后是一张规则表，每行一个条件，右边四个格子分别对应读、改、增、删（R / U / C / D）。

默认规则是三行：编辑者和所有者全权；查看者只读；其他人没有任何权限。

要更细就点「Add table rules」，选一张表单独写规则，比如只让某几个人看某张表；也可以点「Add user attributes」按属性分组。基础的协作者角色（Owner / Editor / Viewer）在分享菜单的 Manage Users 里分配。

**提醒：** 启用 Access Rules 之后，编辑者不能再改文档结构、不能改公式；只有所有者能复制或下载文档。这是有意的设计，规则表本身就是权限的边界。

### 几点说明

**数据在哪。** 文档、表结构和历史都在 grist-data 这个具名卷（容器里的 /persist）。备份这一个卷就够了。

**升级。** 镜像标签用的是 latest，升级前先备份卷，再拉取新镜像、重启项目。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Grist 本身不要求 HTTPS；这是给自己家看的台账，公网地址别到处发，不用的时候可以在插件里把隧道关掉。

**没有用到的能力。** 本文不涉及 AI 助手、OpenRouter、OAuth 登录，也不依赖外部服务；全部功能都在自己的 NAS 上跑。

---

原文：<https://www.passnat.com/posts/grist-nas-docker-linked-records-family-ledger-intranet-penetration>
