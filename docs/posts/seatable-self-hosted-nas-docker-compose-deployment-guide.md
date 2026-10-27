---
title: "开源无代码数据库SeaTable自托管部署指南，NAS极简配置Docker Compose一键启动"
date: 2025-10-16
description: "SeaTable是开源无代码数据库与表格管理平台，提供类似Airtable的功能。支持实时协作编辑、无限行数存储、跨库数据互联、自动化规则引擎和强大API接口。完美适合团队项目管理、客户追踪、库存管理等场景。Docker Compose快速部署，自托管无API调用限制，支持多种数据可视化插件。"
canonical: https://www.passnat.com/posts/seatable-self-hosted-nas-docker-compose-deployment-guide
---

## SeaTable - 开源数据库与表格管理平台

### 项目介绍

SeaTable 是一款功能强大的开源无代码数据库和应用构建平台，由 Seafile 团队精心打造。它提供了类似 Airtable 的电子表格和数据库功能，让你可以在统一的平台上管理各类数据。

SeaTable 采用现代化的架构设计，核心功能包括：支持实时协作编辑的电子表格界面、移动端友好的响应式 UI、无限行数的数据存储能力、跨库数据互联、无代码自动化规则引擎、强大的 API 和 SQL 接口。特别是作为自托管解决方案，它对 API 调用没有任何限制，让你可以自由扩展和定制。

无论是团队项目管理、客户信息追踪、库存管理还是业务流程自动化，SeaTable 都能满足你的需求。它支持多种数据可视化插件（如看板、日历、地图等），让数据管理变得更加直观高效。

[点我跳转](https://github.com/seatable/seatable)

项目地址：[点我跳转](https://github.com/seatable/seatable)

### 部署安装

如果你需要在远程访问 SeaTable，建议配置内网穿透服务来实现外网访问。可以使用 [帕斯内网穿透](https://passnat.com) 这类专业的内网穿透工具，先注册账号备用。具体的部署方案和上手指南可以参考 [这份详细教程](https://www.passnat.com/posts/p)，涵盖全平台操作步骤。

SeaTable 提供了开箱即用的 Docker Compose 部署方案，非常适合技术小白快速上手。

#### 快速部署步骤

**第一步：准备 Docker Compose 配置文件**

复制下列配置内容到你的 NAS 或服务器上，保存为 `docker-compose.yml` 文件：

```yaml
version: '3'

services:
  db:
    image: mariadb:10.5
    container_name: seatable-db
    environment:
      MYSQL_ROOT_PASSWORD: your_root_password
      MYSQL_DATABASE: seatable
    volumes:
      - seatable_db:/var/lib/mysql
    networks:
      - seatable-network

  seatable:
    image: seatable/seatable:latest
    container_name: seatable
    ports:
      - "80:80"
    environment:
      DB_HOST: db
      DB_ROOT_PASSWORD: your_root_password
      DB_USER: seatable
      DB_PASSWORD: your_db_password
      SEATABLE_SERVER_HOSTNAME: your_domain_or_ip
    volumes:
      - seatable_data:/opt/seatable/data
    depends_on:
      - db
    networks:
      - seatable-network

volumes:
  seatable_db:
  seatable_data:

networks:
  seatable-network:
```

**第二步：打开 NAS 设备**

在飞牛 NAS 或其他 NAS 设备上，找到 Docker 管理界面，进入容器管理模块。

**第三步：启动容器**

点击'导入 Docker Compose'或'从文件导入'选项，上传你刚才保存的 `docker-compose.yml` 配置文件。系统会自动识别并下载所需的 Docker 镜像。

**第四步：配置参数说明**

在启动前，你需要修改配置文件中的以下关键信息：

`your\_root\_password`：MySQL 数据库的 root 账户密码（自定义，请妥善保管）

`your\_db\_password`：SeaTable 数据库用户的密码（自定义）

`your\_domain\_or\_ip`：你的服务器域名或公网 IP 地址

**第五步：端口映射设置**

配置文件中已默认映射了 80 端口（HTTP 访问）。如果你的系统中 80 端口已被占用，可以修改第一个数字：

```
ports:
  - "8080:80"  # 改为 8080 端口访问，容器内部仍为 80
```

记住，冒号前面的是你访问时使用的端口，冒号后面的是容器内部端口，不要改动后者。

**第六步：存储配置**

配置文件中已包含两个数据卷：

`seatable\_db`：用于存储 MySQL 数据库文件

`seatable\_data`：用于存储 SeaTable 应用数据和用户文件

这些卷会自动创建在 NAS 的默认存储位置。如果你需要指定特定路径，可以修改为绝对路径：

```yaml
volumes:
  seatable_db: /mnt/nas/seatable/db
  seatable_data: /mnt/nas/seatable/data
```

**第七步：环境变量说明**

变量名: DB\_HOST | 说明: 数据库主机地址 | 默认值: db | 必需: ✓ 必需

变量名: DB\_ROOT\_PASSWORD | 说明: MySQL root 密码 | 默认值: 无 | 必需: ✓ 必需

变量名: DB\_USER | 说明: 数据库用户名 | 默认值: seatable | 必需: 可选

变量名: DB\_PASSWORD | 说明: 数据库用户密码 | 默认值: 无 | 必需: ✓ 必需

变量名: SEATABLE\_SERVER\_HOSTNAME | 说明: 服务器地址（域名或IP） | 默认值: localhost | 必需: ✓ 必需

其他环境变量可保持默认，无需额外配置。

**第八步：启动应用**

确认所有配置无误后，点击'启动'或'部署'按钮。系统会自动下载镜像、创建容器并启动服务。

**第九步：访问应用**

等待容器完全启动后（通常需要 1-2 分钟），在浏览器中访问：

```
http://你的服务器IP:80
```

或者如果修改了端口，则访问：

```
http://你的服务器IP:8080
```

此时 SeaTable 的登录界面应该正常显示，你就可以开始使用这个强大的数据管理平台了。

### 总结

通过以上步骤，你已经成功在自己的 NAS 或服务器上部署了 SeaTable。这个开源平台为你提供了完整的数据管理和业务流程自动化能力，无需编码即可构建复杂的应用。配合内网穿透工具，你还可以随时随地远程访问你的数据库。

本篇教程到此结束，祝你使用愉快！

---

原文：<https://www.passnat.com/posts/seatable-self-hosted-nas-docker-compose-deployment-guide>


## 相关文章

- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [自建在线表格 NocoDB：飞牛 NAS Docker 部署，Airtable 的开源替代，配内网穿透在外面也能打开自己的表](nocodb-one-click-deploy-airtable-open-source-alternative-nas-local-database-management.md)
- [无代码网页数据提取Maxun自建指南，2分钟训练机器人自动爬取，内网穿透远程访问](no-code-web-scraping-maxun-guide-2-minute-robot-training-intranet-penetration-remote-access.md)
- [自托管提醒应用MIND，Docker极简部署NAS，80+平台消息推送，内网穿透远程访问](self-hosted-reminder-app-mind-docker-nas-deployment-80-plus-messaging-platforms-internal-penetration-remote-access.md)
