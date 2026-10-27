---
title: "无代码可视化爬虫平台 Spider-Flow，流程图定义任务无需编程，NAS 一键部署内网穿透远程访问"
date: 2025-10-16
description: "Spider-Flow是灵活可配置的可视化爬虫平台，支持流程图设计、多种数据提取方式、JavaScript动态渲染、代理配置、数据库保存、任务监控等功能。无需编码即可快速构建爬虫应用，适合数据采集爱好者和专业开发者。丰富插件生态提供Selenium、Redis、MongoDB等扩展能力。"
canonical: https://www.passnat.com/posts/no-code-web-scraper-spider-flow-flowchart-nas-deployment
---

## 项目介绍

Spider-Flow 是一个高度灵活可配置的爬虫平台，以流程图的方式定义爬虫任务。它采用可视化流程设计理念，让用户无需编写复杂代码就能快速构建爬虫应用。无论你是数据采集爱好者还是专业开发者，Spider-Flow 都能帮助你轻松应对各种网页数据抓取需求。

该平台支持多种数据提取方式（Xpath、JsonPath、CSS选择器、正则表达式等），能够处理JSON、XML、二进制等多种数据格式。同时支持JavaScript动态渲染页面爬取、代理配置、数据库自动保存、任务监控和日志追踪等强大功能。丰富的插件生态（Selenium、Redis、MongoDB、OSS等）让你可以根据需求灵活扩展。

[点我跳转](https://github.com/ssssssss-team/spider-flow)

项目地址：

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往 [帕斯内网穿透](https://passnat.com)，先注册好账号备用。

具体的操作方法可以 [点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略

### Docker 单命令部署

打开飞牛 NAS 或其他的 NAS 设备，点击本地镜像 - 添加镜像 - 从 URL 添加。

在镜像地址栏填入：ssssssss/spider-flow:latest

等待镜像下载完成后，点击启动按钮启动容器。

**端口映射设置**

勾选开机自动启动后进入下一步。在端口设置中，需要配置以下映射：

容器内端口：8088（这是 Spider-Flow 应用运行的端口，不要修改）

宿主机端口：8088（如果你的 NAS 上 8088 端口已被占用，可以修改为其他端口，比如 8089、8090 等，但容器内端口必须保持为 8088）

如果提示端口被占用，只需修改宿主机端口即可，容器内端口保持不变。

**存储位置配置**

Spider-Flow 会自动生成爬虫配置和任务数据。建议在 NAS 上创建一个专用文件夹（如 /mnt/spider-flow）用于持久化存储，这样重启容器后数据不会丢失。

**环境变量设置**

Spider-Flow 的基础版本无需特殊环境变量配置，所有功能开箱即用。如果你需要使用数据库功能或其他高级特性，可以根据实际需求在后续配置中添加。

其他设置保持默认，直接下一步启动即可。

**访问应用**

容器启动成功后，在浏览器中访问：http://你的NAS地址:8088

稍等片刻，Spider-Flow 的可视化界面就会加载完成。你可以开始创建爬虫流程、配置数据提取规则、监控任务执行情况。

## 功能亮点

可视化流程设计：通过拖拽流程图快速定义爬虫逻辑，无需编程基础

多种数据提取方式：支持 Xpath、JsonPath、CSS 选择器、正则表达式混合使用

动态页面支持：能够爬取 JavaScript 渲染的动态内容

数据库集成：支持自动保存数据至数据库或文件

任务监控：实时查看爬虫执行状态和详细日志

插件扩展：丰富的插件库满足各类高级需求

本篇教程结束

---

原文：<https://www.passnat.com/posts/no-code-web-scraper-spider-flow-flowchart-nas-deployment>
