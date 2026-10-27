---
title: "开源电商平台C-Shopping完整部署指南，Next.js+MongoDB+Docker容器化快速搭建"
date: 2025-10-16
description: "C-Shopping是基于Next.js14、Tailwind CSS、Redux Toolkit等前沿技术打造的全栈开源电商平台。具备完整的用户端和管理后台功能，支持商品浏览、购物车、订单管理、支付结算等核心电商功能。采用MongoDB数据库和Docker容器化部署，提供响应式设计和完美的移动端适配。项目开箱即用，展示现代Web开发最佳实践。"
canonical: https://www.passnat.com/posts/c-shopping-deployment-guide-nextjs-mongodb-docker
---

## 项目介绍

C-Shopping 是一个基于最新技术栈打造的全栈开源电商平台。它采用 Next.js 14 + Tailwind CSS + Headless UI + Redux Toolkit + RTK Query + JWT 等前沿技术构建，配合 MongoDB 数据库和 Docker 容器化部署，为用户提供了一个完整、美观、高性能的购物体验。

这个项目不仅仅是一个简单的电商应用，更是一个展示现代化 Web 开发最佳实践的完整生态。无论是前端的响应式设计、后端的 API 架构，还是数据库的集成，C-Shopping 都做到了开箱即用。界面设计精美流畅，支持桌面端和移动端的完美适配，让用户可以在任何设备上享受顺畅的购物体验。项目还包含完整的管理后台，方便店铺管理员进行商品、订单、用户等全方位的管理。

项目地址：[点我跳转](https://github.com/huanghanzhilian/c-shopping)

## 部署安装

如果你想在办公室或家里随时随地访问这个项目，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)

来实现远程访问，先注册账号备用。具体的详细操作步骤可以参考 [这份完整的上手攻略](https://www.passnat.com/posts/p)

，里面有针对各个平台的详细说明。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，只需一条命令即可启动整个应用。

**第一步：复制配置文件**

将下面的配置内容保存为 `docker-compose.yml` 文件：

```yaml
version: '3.8'

services:
  web:
    container_name: web
    build:
      context: .
      dockerfile: Dockerfile
    restart: always
    ports:
      - '3000:3000'
    environment:
      - WAIT_HOSTS=db:27017
      - WAIT_HOSTS_TIMEOUT=300
      - WAIT_SLEEP_INTERVAL=30
      - WAIT_HOST_CONNECT_TIMEOUT=30
    depends_on:
      - db

  db:
    container_name: db
    image: 'mongo:4.4.13'
    restart: always
    volumes:
      - type: volume
        source: MONGO_DATA
        target: /data/db
      - type: volume
        source: MONGO_CONFIG
        target: /data/configdb
    ports:
      - '27017:27017'

volumes:
  MONGO_DATA:
    name: MONGO_DATA
  MONGO_CONFIG:
    name: MONGO_CONFIG
```

**第二步：打开 NAS 设备**

登录到你的飞牛 NAS 或其他 NAS 设备的管理后台。

**第三步：导入项目**

1. 将上述 `docker-compose.yml` 文件上传到 NAS 的某个目录中
2. 通过 NAS 的 Docker 管理界面找到'导入'或'Compose'功能
3. 选择上传的 `docker-compose.yml` 文件
4. 点击启动，系统会自动拉取镜像并创建容器

**第四步：配置说明**

这个配置包含两个服务：

- web 服务：C-Shopping 应用主体，运行在容器内的 3000 端口
- db 服务：MongoDB 数据库，运行在容器内的 27017 端口

如果你的 NAS 上已经有其他应用占用了 3000 或 27017 端口，可以修改上面配置中的端口映射。例如，如果想改为 8080 端口访问，只需将 `'3000:3000'` 改为 `'8080:3000'` 即可。注意冒号前面是外部访问端口，冒号后面是容器内部端口，一般只需修改冒号前面的数字。

**数据持久化**

配置中已经配置了 MongoDB 数据的持久化存储（MONGO\_DATA 和 MONGO\_CONFIG 卷），这样即使容器重启，你的数据也不会丢失。

**第五步：启动应用**

完成上述配置后，系统会自动启动所有容器。等待 2-3 分钟让应用完全启动。

**第六步：访问应用**

打开浏览器，访问 `http://你的NAS\_IP:3000`，就能看到 C-Shopping 的首页了。

**初始化设置**

首次访问时，需要进行以下初始化操作：

1. 点击'注册'创建一个账户
2. 创建完成后，通过 NAS 的 MongoDB 管理工具或命令行连接数据库，执行以下命令将账户升级为管理员：

```
mongo
use choiceshop
db.users.update({name:'你的账户名'},{$set:{role:'admin'}})
db.users.update({name:'你的账户名'},{$set:{root:true}})
```

3. 创建根分类（这是必需的），执行以下命令：

```
db.categories.insert({
    'name' : '精选商品',
    'slug' : 'choiceshop',
    'image' : 'http://huanghanzhilian-test.oss-cn-beijing.aliyuncs.com/shop/upload/image//icons/zHle_bmdM_dhu2K938MMM.webp',
    'colors' : {
        'start' : '#EF394E',
        'end' : '#EF3F55'
    },
    'level' : 0
})
```

4. 完成后，访问 `http://你的NAS\_IP:3000/admin` 进入管理后台

## 功能特性

**用户端功能**

🏠 首页展示与分类浏览
🔍 商品搜索功能
📦 详细的商品信息展示
🛒 购物车管理
💳 订单结算与支付
👤 个人中心与账户管理
📋 订单历史查看
⭐ 商品评价与收藏
📍 收货地址管理

**管理端功能**

👥 用户管理
📂 商品分类管理
📝 商品规格管理
🛍️ 商品库存管理
📦 订单处理与跟踪
⭐ 评价审核管理
🎨 首页轮播图管理
🎯 广告横幅管理

## 技术亮点

✨ Next.js 14 驱动 - 享受闪电般的页面加载速度和服务端渲染能力

🎨 Tailwind CSS 设计 - 现代化的样式系统，每个界面都精心打磨

🔧 Headless UI 架构 - 完全自由的组件定制，突破传统 UI 框架的束缚

🔐 JWT 安全认证 - 采用业界标准的 JWT 令牌机制，保障用户数据安全

🗄️ MongoDB 数据库 - 灵活的文档型数据存储，完美适配电商业务

🐳 Docker 容器化 - 一键部署，无需担心环境差异问题

到这里，C-Shopping 的部署就完全搞定了。现在你可以开始体验这个功能完整、设计精美的开源电商平台了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/c-shopping-deployment-guide-nextjs-mongodb-docker>
