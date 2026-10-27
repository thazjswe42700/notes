---
title: "ReDoc 接口文档：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署 ReDoc：把接口写成一份 OpenAPI 的 YAML，页面自动排成目录、请求参数和返回示例，同事用浏览器打开一个地址就能查，不用登录，手机也能看；用帕斯内网穿透拿公网地址，在外面也能打开。含完整 YAML、编排和步骤。"
canonical: https://www.passnat.com/posts/redoc-api-docs-nas-docker-openapi-intranet-penetration
---

## 项目介绍

ReDoc 是一个开源的接口文档渲染器。你只要写好一份 OpenAPI 的 YAML，它就把所有接口排成一整页：左边是分组目录，点开接口，中间列每个路径参数、查询参数和请求体，右边直接给返回的 JSON 示例。

页面是静态的，不用登录，也不需要数据库。把 YAML 和编排放在 NAS 上，同事用浏览器打开一个地址就能查；接口改了，换掉 YAML，页面跟着更新，不用再发文件、翻聊天记录。

这篇用一份「家庭藏书 API」的示例 YAML 演示：四个图书接口、一个借阅接口，返回三本书。换成你自己的接口，格式照这个写就行。

项目地址：[点我跳转](https://github.com/Redocly/redoc)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再用 Docker Compose 把 ReDoc 装起来。装完用公网地址打开，同事在外面、用手机也能看同一页文档。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3652（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、准备接口说明文件

在存放编排的文件夹里新建一个文件，名字就叫 openapi.yaml，把下面这份示例整个贴进去。注意缩进是空格，不要用 Tab。

```yaml
openapi: 3.0.3
info:
  title: 家庭藏书 API
  version: 1.0.0
  description: |
    给家里的书架做一份接口说明：查书、看详情、登记借出。
    数据只存在 NAS 上，局域网内用浏览器打开就能看。
servers:
  - url: https://nas.local/api
    description: NAS 上的本地服务
tags:
  - name: 图书
    description: 查看和登记书架上的书
  - name: 借阅
    description: 谁把书借走了
paths:
  /books:
    get:
      tags: [图书]
      summary: 列出书架上的书
      description: 支持按书名搜索、按书柜筛选，默认一次返回 20 条。
      parameters:
        - name: q
          in: query
          description: 书名或作者关键词
          schema: { type: string }
          example: 三体
        - name: shelf
          in: query
          description: 书柜编号
          schema: { type: string }
          example: A-03
        - name: limit
          in: query
          schema: { type: integer, default: 20, maximum: 100 }
      responses:
        '200':
          description: 图书列表
          content:
            application/json:
              schema:
                type: array
                items: { $ref: '#/components/schemas/Book' }
              example:
                - id: 1024
                  title: 三体
                  author: 刘慈欣
                  shelf: A-03
                  status: 在架
                - id: 1025
                  title: 人类简史
                  author: 尤瓦尔·赫拉利
                  shelf: B-01
                  status: 在架
                - id: 1026
                  title: 设计中的设计
                  author: 原研哉
                  shelf: A-03
                  status: 借出
    post:
      tags: [图书]
      summary: 登记一本新书
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/BookInput' }
            example:
              title: 置身事内
              author: 兰小欢
              shelf: C-02
      responses:
        '201':
          description: 登记成功
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Book' }
  /books/{id}:
    get:
      tags: [图书]
      summary: 查看一本书的详情
      parameters:
        - name: id
          in: path
          required: true
          schema: { type: integer }
          example: 1024
      responses:
        '200':
          description: 图书详情
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Book' }
        '404':
          description: 没有这本书
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Error' }
    delete:
      tags: [图书]
      summary: 从书架移除一本书
      parameters:
        - name: id
          in: path
          required: true
          schema: { type: integer }
      responses:
        '204':
          description: 已移除
  /books/{id}/lend:
    post:
      tags: [借阅]
      summary: 登记借出
      description: 记下借给了谁，书架上的状态会变成「借出」。
      parameters:
        - name: id
          in: path
          required: true
          schema: { type: integer }
          example: 1024
      requestBody:
        required: true
        content:
          application/json:
            schema: { $ref: '#/components/schemas/Lend' }
            example:
              borrower: 小林
              due_date: '2026-11-02'
      responses:
        '200':
          description: 借出登记成功
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Book' }
components:
  schemas:
    Book:
      type: object
      description: 书架上的书
      required: [id, title, author, shelf, status]
      properties:
        id:
          type: integer
          example: 1024
        title:
          type: string
          example: 三体
        author:
          type: string
          example: 刘慈欣
        shelf:
          type: string
          description: 书柜与层号
          example: A-03
        status:
          type: string
          enum: [在架, 借出]
          example: 在架
    BookInput:
      type: object
      required: [title, author, shelf]
      properties:
        title:
          type: string
          example: 置身事内
        author:
          type: string
          example: 兰小欢
        shelf:
          type: string
          example: C-02
    Lend:
      type: object
      required: [borrower]
      properties:
        borrower:
          type: string
          description: 借书人
          example: 小林
        due_date:
          type: string
          format: date
          description: 约定归还日期
          example: '2026-11-02'
    Error:
      type: object
      properties:
        code:
          type: integer
          example: 404
        message:
          type: string
          example: 没有这本书
```

几个位置按自己的情况改：title 写你的服务名；description 写一两句给同事看的说明；servers 里的 https://nas.local/api 换成你的接口地址，或者直接删掉这一行；下面的 paths 和 components 换成你自己的接口。不想手写也可以先拿这份示例跑通，看到效果再改。

### 三、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 redoc，路径选刚才放 openapi.yaml 的那个文件夹。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  redoc:
    image: redocly/redoc:latest
    container_name: redoc
    restart: unless-stopped
    ports:
      - "3652:80"
    environment:
      SPEC_URL: openapi.yaml
      PAGE_TITLE: 家庭藏书 API
    volumes:
      - ./openapi.yaml:/usr/share/nginx/html/openapi.yaml:ro
```

这段编排要看两处：

**SPEC\_URL: openapi.yaml**：告诉 ReDoc 去读同目录下的哪份文件。文件名改了，这里跟着改。

**volumes 那一行**：把宿主上的 openapi.yaml 挂进容器里 nginx 的网站目录，只读。冒号右边是容器里的路径，不要改；冒号左边是文件名，要跟 SPEC\_URL 对上。

端口 3652 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。勾上「创建项目后立即启动」，镜像下完就起来了。这个镜像很小，只有几十兆，几秒钟就能拉完。

### 四、打开看看

用公网地址打开，先看到文档标题和简介。左边是「图书」「借阅」两组目录，点开列出接口；点 GET /books，中间是 q、shelf、limit 三个查询参数，右边是 200 返回的 JSON，三条示例数据整整齐齐。

手机上打开同一个地址，排版会自动收成一列：目录、参数和返回示例都在，不用缩放也能看。把地址发到工作群，同事点开就能查，不用登录，也不用装任何东西。

### 五、接口改了怎么办

改完自己的接口，把新的 openapi.yaml 覆盖上去，刷新浏览器就是新页面，容器不用重启。如果改了文件名，记得同步改编排里的 SPEC\_URL 和 volumes。

接口文档跟着代码走：写接口的时候先把 YAML 写好，前端后端都照这一页来，改动也有据可查。比在聊天记录里翻「这个字段到底叫啥」省事得多。

### 几点说明

**它只负责展示。** ReDoc 读 YAML、排版、显示示例，不提供接口本身，也不做鉴权。文档页面谁拿到地址都能看，所以别把口令、密钥写进 YAML 的示例里。

**数据就是那份 YAML。** 没有数据库，也没有要备份的卷。把 openapi.yaml 和 compose.yml 两个文件存好，换一台 NAS 照样能起来。

**镜像标签用 latest。** 升级时重新拉取镜像、重启项目即可；升级前先把两个文件备份一份，万一新版对 YAML 的要求变了，还能退回去。

**和 Swagger UI 的区别。** 同样是读 OpenAPI，ReDoc 更偏「读文档」：三栏排版、搜索、右侧常驻返回示例；需要在线调试接口的话，另装一个 Swagger UI 更合适。这篇只讲 ReDoc。

---

原文：<https://www.passnat.com/posts/redoc-api-docs-nas-docker-openapi-intranet-penetration>


## 相关文章

- [Swagger Editor：NAS Docker 部署 + 内网穿透](swagger-editor-nas-docker-openapi-api-docs-intranet-penetration.md)
- [轻量级个人导航页面 Flare 快速部署教程，飞牛 NAS Docker 部署 + 内网穿透远程访问](lightweight-personal-navigation-flare-quick-deploy-tutorial-nas-one-click-install-guide.md)
- [Homepage 自建 NAS 导航首页：飞牛 Docker 部署 + 内网穿透外网访问](homepage-nas-service-dashboard-docker-deployment-intranet-penetration.md)
- [极简网页仪表盘SimpleDash，本地快速部署，内网穿透远程访问，YAML配置自定义链接管理](simple-dash-local-dashboard-intranet-penetration-remote-access-yaml-config.md)
- [自建私人API开发环境，Yaade开源协作平台Docker快速部署指南](yaade-open-source-api-development-docker-deployment-guide.md)
