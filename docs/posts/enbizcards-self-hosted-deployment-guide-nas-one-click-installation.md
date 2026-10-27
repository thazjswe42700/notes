---
title: "开源数字名片生成器EnBizCard自建部署指南，NAS一键安装创建专业交互式名片"
date: 2025-10-16
description: "EnBizCard是开源数字名片生成器，支持创建精美交互式HTML名片。完全自主托管，数据隐私可控，支持多种联系方式、vCard下载、二维码分享、作品展示等功能。内置多种主题和访客分析，Docker快速部署无需技术基础。"
canonical: https://www.passnat.com/posts/enbizcards-self-hosted-deployment-guide-nas-one-click-installation
---

## 项目介绍

EnBizCard 是一个开源的数字名片生成器，让你能够创建精美的交互式HTML数字名片。与其他第三方服务不同，你可以将其免费托管在自己的网站上，完全掌控自己的数据。

这个项目设计简洁优雅，功能丰富。支持多种联系方式快速拨号、WhatsApp、邮件等一键联系功能，支持vCard下载让访客直接保存你的联系信息到手机，还能通过二维码或链接分享你的名片。你可以展示个人作品、图片、视频、音乐等内容，甚至嵌入自定义HTML内容。同时内置多种主题、自定义字体、访客分析等功能。最重要的是，你的所有数据完全属于你自己，隐私设计从一开始就被考虑在内。

项目地址： [点我跳转](https://github.com/vishnuraghavb/EnBizCard)

## 部署安装

若要在外网也能访问你的数字名片，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的配置步骤可以参考 [这份上手指南](https://www.passnat.com/posts/p)，轻松实现远程访问。

### 使用 Docker Compose 快速部署

这是最推荐的部署方式，操作简单快捷。

复制下列的配置文件到你的 NAS 或服务器上，保存为 'docker-compose.yml'：

```yaml
version: '3.8'

services:
  enbizcard:
    image: vishnuraghavb/enbizcard:latest
    container_name: enbizcard
    ports:
      - "22212:3000"
    environment:
      - NODE_ENV=production
    restart: unless-stopped
    volumes:
      - enbizcard_data:/app/data

volumes:
  enbizcard_data:
```

打开你的飞牛 NAS 或其他 NAS 设备的管理界面。

找到'应用中心'或'Docker'相关选项，选择'从文件导入'或'Compose 部署'，将上面的配置文件粘贴进去。

系统会自动下载镜像并创建容器。稍等片刻，容器启动完成后，你就可以通过浏览器访问应用了。

**端口配置说明：**

上面配置中的 '22212' 是外部访问端口，如果你的 NAS 上该端口已被占用，可以修改为其他可用端口（如 '8080'、'8888' 等）

容器内部端口固定为 '3000'，无需修改

修改时只需改动冒号前面的数字即可，格式保持为 '外部端口:3000'

**存储配置说明：**

上面配置中的 'enbizcard\_data' 卷会自动为你的数据创建持久化存储，确保重启容器后数据不会丢失。如果需要在 NAS 上指定具体的存储位置，可以将 'enbizcard\_data:/app/data' 改为 '/你的NAS路径:/app/data'。

**环境变量设置：**

此项目的默认配置已经足够使用，无需额外设置环境变量。如有特殊需求，可自行扩展。

部署完成后，在浏览器中访问 'http://你的NAS地址:22212'（或你修改后的端口），即可看到 EnBizCard 的管理界面，开始创建你的数字名片了。

## 总结

通过 EnBizCard，你可以拥有一个完全自主、隐私优先的数字名片解决方案，无需依赖第三方服务，数据安全可控。部署过程简单快捷，即使是技术小白也能轻松上手。赶快为自己创建一张专业的数字名片吧！

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/enbizcards-self-hosted-deployment-guide-nas-one-click-installation>
