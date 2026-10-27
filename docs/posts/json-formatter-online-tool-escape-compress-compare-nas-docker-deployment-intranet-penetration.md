---
title: "ZJSON在线JSON格式化编辑工具，支持转义压缩对比，NAS Docker一键部署，内网穿透远程访问使用"
date: 2025-10-16
description: "ZJSON是功能强大的在线JSON格式化编辑工具，支持标准及非标准JSON处理、错误提示、转义压缩等多种操作。采用Vite5+Vue3+NestJS现代技术栈，提供深浅主题、国际化支持、文件对比、多任务标签页等丰富功能。专为开发者设计，可快速处理API数据、调试配置文件、进行数据转换。支持Docker一键部署到NAS，开箱即用。"
canonical: https://www.passnat.com/posts/json-formatter-online-tool-escape-compress-compare-nas-docker-deployment-intranet-penetration
---

## 项目介绍

ZJSON 是一个功能强大的在线 JSON 格式化和编辑工具。它不仅支持标准 JSON 的格式化，还能够处理非标准 JSON 格式并提供详细的错误提示，让开发者能够快速定位和修复问题。

项目采用现代化的技术栈构建，前端使用 Vite5 + Vue3 + Pinia + Ant-design-vue + Monaco-editor，后端基于 NestJS 框架。无论是专业开发者还是技术爱好者，都能通过这个工具轻松处理 JSON 数据。

ZJSON 提供了丰富的功能特性：支持 JSON 转义、反转义、压缩、键值引号设置等多种操作；支持本地保存和读取历史档案；提供深浅双色主题和中英文国际化支持；支持多任务标签页管理；还能进行文件对比和代码编辑。无论你是在处理 API 响应数据、调试配置文件，还是进行数据转换，ZJSON 都能提供强大的支持。

项目地址：[点我跳转](https://github.com/CN-Tower/zjson)

官方在线版本：<https://www.zjson.net>

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略。

ZJSON 采用前后端分离的架构，包含客户端和服务器两个部分。最快速的部署方式是使用 Docker 容器化部署。

### Docker 单命令部署

打开飞牛 NAS 或其他的 NAS 设备，点击本地镜像 - 添加镜像 - 从 URL 添加。

在镜像地址栏填入以下镜像地址：

```
cntower/zjson:latest
```

等待镜像下载完成后，点击启动按钮启动容器。

### 端口配置说明

勾选开机自动启动后进入下一步。在端口设置这里，ZJSON 需要映射以下端口：

容器内端口 3000：前端应用访问端口（必须映射）
容器内端口 3001：后端 API 服务端口（必须映射）

建议将容器端口 3000 映射到宿主机的 3000 端口，容器端口 3001 映射到宿主机的 3001 端口。如果这些端口在你的 NAS 上已被占用，可以修改宿主机端口号，但需要保持格式正确。例如，可以映射为 8080 -> 3000 和 8081 -> 3001。

### 存储配置说明

ZJSON 是一个前后端分离的应用，通常不需要额外的持久化存储配置。如果你需要保存用户数据或历史记录，可以创建一个数据目录并映射到容器内的 '/app/data' 路径。

### 环境变量设置

ZJSON 的基础部署无需设置特殊的环境变量。所有功能都可以开箱即用。如果你有特殊需求（如自定义配置、性能调优等），可以参考官方文档进行高级配置。

其他的都不用填写，直接下一步启动就好了。

### 访问应用

创建成功后可以访问 'http://你的NAS地址:3000' 来打开 ZJSON 的前端界面。此时你就可以正常使用 JSON 格式化、编辑、对比等所有功能了。

到这里就可以正常显示项目了。

## 总结

ZJSON 是一个开箱即用的强大 JSON 处理工具，通过 Docker 部署只需要简单的几步操作就能在你的 NAS 上运行。无论是日常开发调试还是数据处理，ZJSON 都能提供便捷高效的解决方案。现在就开始部署，享受专业级的 JSON 处理体验吧！

本篇教程结束。

---

原文：<https://www.passnat.com/posts/json-formatter-online-tool-escape-compress-compare-nas-docker-deployment-intranet-penetration>


## 相关文章

- [开源时间追踪Kimai完整部署指南,NAS极简配置轻松打造专业计时系统](kimai-open-source-time-tracking-deployment-guide-nas-simple-configuration.md)
- [自建图片社区平台Szurubooru，Docker极简部署NAS，内网穿透远程访问图片库](szurubooru-docker-nas-deployment-intranet-penetration-remote-access-image-library.md)
- [在线AI智能抠图工具，支持Web和API接口，NAS一键部署，内网穿透远程访问使用](ai-intelligent-image-matting-web-api-nas-deployment.md)
- [极简链接中心YAL，Go语言零依赖部署，NAS一键启动个人导航门户，内网穿透远程访问](yal-go-zero-dependency-nas-personal-navigation-portal.md)
- [开源低代码平台Appsmith本地部署指南，NAS极简安装Docker Compose，无公网内网穿透远程访问](appsmith-nas-docker-compose-local-deployment-guide.md)
