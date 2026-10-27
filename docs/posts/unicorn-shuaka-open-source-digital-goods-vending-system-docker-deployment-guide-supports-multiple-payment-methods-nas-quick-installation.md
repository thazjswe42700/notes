---
title: "独角数卡开源数字商品售货系统Docker部署指南，支持多种支付方式NAS快速安装"
date: 2025-10-16
description: "独角数卡是开源免费的数字商品自动化售货系统，基于Laravel框架构建。支持卡密、虚拟商品自动售卖，集成支付宝、微信、PayPal等十多种支付方式。提供Docker快速部署方案，支持自定义模板和多语言，代码完全开源可溯源，MIT协议免费使用。"
canonical: https://www.passnat.com/posts/unicorn-shuaka-open-source-digital-goods-vending-system-docker-deployment-guide-supports-multiple-payment-methods-nas-quick-installation
---

## 独角数卡 - 开源数字商品售货系统

### 项目介绍

**独角数卡** 是一个开源式的自动化数字商品售货系统解决方案。它基于业界流行的 Laravel 框架构建，采用 Laravel-admin 后台管理系统和 Bootstrap 前端 UI，为站长提供了高效、稳定、快速的商品销售平台。

这是一个完全开源免费的产品，遵守 MIT 开源协议。系统支持自定义前端模板、国际化多语言包，所有代码完全开源，所有扩展包通过 Composer 加载，代码内容完全可溯源。无论是虚拟商品、卡密、充值卡等数字产品，都可以通过该系统轻松实现自动化售卖。

支持多种主流支付接口集成，包括支付宝（当面付、PC支付、手机支付）、微信支付、PayPal、Stripe 等十多种支付方式，让您的客户可以选择最便捷的支付方式。

项目地址：[点我跳转](https://github.com/assimon/dujiaoka)

### 部署安装

如需在外网环境中访问您的独角数卡系统，建议配置内网穿透服务，推荐使用[帕斯内网穿透](https://passnat.com)。先注册好账号以备后续使用。具体的操作方法可以[点击查看](https://www.passnat.com/posts/p)帕斯网络穿透全平台快速入门指南。

独角数卡提供了 Docker 部署方案，让您可以快速启动系统。以下为最便捷的单命令部署方式：

#### Docker 单命令启动

打开您的 NAS 设备或服务器管理面板（如飞牛 NAS、群晖等）

点击本地镜像 → 添加镜像 → 从 URL 添加

镜像地址填写：assimon/dujiaoka:latest

等待镜像下载完成后，点击启动按钮启动容器

**容器启动配置说明：**

勾选开机自动启动后进入下一步

**端口映射设置：**

容器内端口：80（HTTP 访问端口）

容器内端口：443（HTTPS 访问端口，可选）

如果您的宿主机 80 端口已被占用，可修改宿主机端口号（如改为 8080），但容器内端口需保持为 80。修改后访问时使用ip:8080即可。

**存储位置配置：**

独角数卡需要持久化存储数据库和配置文件。建议在宿主机创建以下目录并进行映射：

/data/dujiaoka/mysql - MySQL 数据库存储目录

/data/dujiaoka/app - 应用程序文件目录

**环境变量设置：**

必须设置：

```
APP_KEY=base64:your_app_key_here
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=dujiaoka
DB_USERNAME=dujiaoka
DB_PASSWORD=your_password_here
REDIS_HOST=redis
REDIS_PORT=6379
```

可选设置（保持默认即可）：

```
APP_DEBUG=false
APP_ENV=production
```

其他配置项保持默认值，直接点击启动即可完成部署。

**访问您的系统：**

创建成功后，打开浏览器访问http://ip:80（如果修改了端口则为http://ip:修改的端口号）

默认管理后台地址：http://ip:80/admin

默认管理员账号：admin

默认管理员密码：admin

⚠️ 重要提示：首次登录后请立即修改默认密码，确保系统安全。

到这里您的独角数卡系统就已经部署完成并可以正常使用了。

本篇教程结束

---

原文：<https://www.passnat.com/posts/unicorn-shuaka-open-source-digital-goods-vending-system-docker-deployment-guide-supports-multiple-payment-methods-nas-quick-installation>


## 相关文章

- [开源个人关系管理系统Monica私密部署指南，NAS一键Docker快速安装配置教程](open-source-personal-crm-monica-private-deployment-guide-nas-docker-quick-install.md)
- [自托管书签管理工具Servas无公网部署指南 NAS极简配置内网穿透远程访问](servas-self-hosted-bookmark-manager-nas-deployment-guide-intranet-penetration-remote-access.md)
- [企业IT运维管理系统CAT一键部署方案，Docker容器快速启动NAS设备应用](enterprise-it-operations-management-system-cat-one-click-deployment-docker-container-nas.md)
- [开源RSS阅读器一键部署，聚合订阅内容无限浏览，NAS自建私人阅读平台内网穿透远程访问](open-source-rss-reader-one-click-deploy-aggregate-subscriptions-unlimited-browsing-nas-self-hosted-private-reading-platform-intranet-penetration-remote-access.md)
- [自建云相册Lsky Pro，NAS极简部署，支持多云存储，内网穿透远程访问图片管理分享系统](lsky-pro-nas-deployment-multi-cloud-storage-remote-access.md)
