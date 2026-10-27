---
title: "Swagger Editor：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源接口文档编辑器 Swagger Editor：左边写 OpenAPI 的 YAML，右边实时渲染文档，写错缩进当场报行号，还能生成服务端和客户端代码框架；用帕斯内网穿透拿公网地址，在外面也能打开接着改。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/swagger-editor-nas-docker-openapi-api-docs-intranet-penetration
---

## 项目介绍

Swagger Editor 是一个开源的接口文档编辑器，写的是 OpenAPI 规范。左边用 YAML（或 JSON）把接口描述清楚：每个接口的路径、方法、参数、请求体、返回什么；右边实时渲染成给人看的文档，标题、说明、接口列表、示例数据都在。

它本身没有后端、没有数据库、没有账号：整个编辑器是一份静态页面，跑起来就能用。写错了缩进或者漏了字段，编辑器下面会列出错误和行号，点一下跳到那一行；写对了，右边跟着变。顶部还能按这份描述生成服务端和客户端的代码框架，省得手敲。

适合的场景：自己写个小服务，先定接口再写代码；或者前后端两个人对着一份文档说话，改一处大家看到的是同一版。项目地址：[点我跳转](https://github.com/swagger-api/swagger-editor)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Swagger Editor 用 Docker Compose 装起来。先穿透、后部署，装完直接用公网地址打开，以后在外面也能打开接着改。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3711（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 swagger，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  swagger-editor:
    image: swaggerapi/swagger-editor:latest
    container_name: swagger-editor
    ports:
      - "3711:80"
    restart: unless-stopped
```

这段编排不用改任何东西：镜像是官方的，没有账号口令，也没有数据卷。唯一要注意的是端口——镜像内部的 nginx 监听的是 80，所以冒号右边写 80，左边 3711 是宿主上的端口。3711 被占用的话，把冒号左边的数字换一个，隧道里的本地端口也跟着改。

勾上「创建项目后立即启动」，等镜像拉完、容器变成运行中就行。

### 三、第一次打开

用公网地址打开，浏览器里直接就是编辑器，没有登录页。第一次进来会带着一份默认示例，左边是示例 YAML，右边是渲染出来的文档。把示例全选删掉，换成自己的内容。

编辑器右上角有个太阳图标，可以切换浅色主题；浅色下长时间看眼睛舒服一些。

### 四、写第一份接口文档

一份最小的 OpenAPI 3.0 文档，开头三块：

**openapi：** 写版本号 3.0.3。

**info：** 写标题、版本和一句说明，右边文档的大标题和副标题就是这里来的。

**servers：** 写接口的地址前缀。自己用的话，写个占位主机名就行，比如 nas.local。

往下是 paths，一条接口一段：方法（get / post）、路径、summary、参数、返回。参数里写清楚名字、位置（query / path）、类型和一句说明；返回里写状态码和 content。反复用的数据结构放在 components 的 schemas 里，用 $ref 引用。

```yaml
openapi: 3.0.3
info:
  title: 家庭藏书 API
  version: 1.0.0
  description: 家里书架上的书，用接口查询、借出、归还。
servers:
  - url: http://nas.local:8080
paths:
  /books:
    get:
      summary: 查询书单
      parameters:
        - name: q
          in: query
          description: 按书名或作者搜
          schema:
            type: string
      responses:
        '200':
          description: 书单
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Book'
    post:
      summary: 添加一本书
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Book'
      responses:
        '201':
          description: 已添加
  /books/{id}/borrow:
    post:
      summary: 借出
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: 借出成功
components:
  schemas:
    Book:
      type: object
      properties:
        id:
          type: integer
        title:
          type: string
        author:
          type: string
        status:
          type: string
          enum: [在架, 借出]
```

贴进去之后，右边会实时渲染出三条接口和一张 Book 结构表。把 info 下面某一行少写两个空格，编辑器下面立刻出现一行红色的错误，写着行号和原因，右边则显示内容无法解析——改回去，报错就消失。

### 五、展开接口、生成代码、保存

右边每一条接口都能点开：Parameters 里是参数表，Responses 里是返回码和说明，下面的 Example Value 会按你写的 schema 直接生成一份示例 JSON，返回长什么样一眼看到。

觉得文档没问题了，顶部「Generate Server」选一个语言，就能下载一份服务端代码框架，里面每个接口都是一个待填的函数；「Generate Client」则是给调用方用的。

**记得保存。** 这个编辑器是纯前端，内容只在浏览器里，刷新页面就回到默认示例。写好的 YAML 在「File」菜单里下载下来，存回 NAS；下次打开再把文件拖进去。

### 几点说明

**没有账号，也就没有权限。** 谁能打开这个地址，谁就能改这份文档。公网地址别到处发，用完了可以在插件里把隧道关掉。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，编辑器本身不需要 HTTPS 才能用；但它是一份能改的文档，重要的东西别放在公网入口上。

**它只是编辑器。** 生成的文档要给别人看，可以用 Swagger UI；要真正跑起接口，得有自己的服务端。这个镜像只负责写和预览。

**升级。** 镜像标签用的是 latest，升级前把写好的 YAML 下载备份一份，再到 Docker 里重新拉取镜像、重启项目。

---

原文：<https://www.passnat.com/posts/swagger-editor-nas-docker-openapi-api-docs-intranet-penetration>
