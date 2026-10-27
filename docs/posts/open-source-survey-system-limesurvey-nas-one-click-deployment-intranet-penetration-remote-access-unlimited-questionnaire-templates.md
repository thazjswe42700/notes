---
title: "开源问卷调查系统LimeSurvey，NAS一键部署，内网穿透远程访问，无限问卷模板"
date: 2025-10-16
description: "LimeSurvey是全球排名第一的开源在线问卷调查平台，提供无限问卷数量、900+专业模板、30+问题类型、80+语言支持和GDPR合规等企业级特性。支持高级数据分析、API集成、插件扩展，适用于客户反馈、学术研究、多语言问卷和数据隐私保护场景。"
canonical: https://www.passnat.com/posts/open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates
---

## 项目介绍

LimeSurvey 是全球排名第一的开源在线问卷调查平台，自 2006 年以来一直致力于为全球 80 多个国家的企业、专业人士、学术机构、教师、学生和政府部门提供优质的问卷服务。这是一个功能强大、完全开源的调查问卷系统，具有无限制的问卷数量、超过 900 个专业模板、30 多种问题类型、条件逻辑、问卷分支、多语言支持（80+ 语言）和 GDPR 合规等企业级特性。

无论你是需要收集客户反馈、进行学术研究、运营全球多语言问卷、还是优先考虑数据隐私和合规性，LimeSurvey 都能提供完整的解决方案。平台支持高级数据分析、API 集成、插件扩展等功能，让你可以灵活定制和扩展功能。

项目地址：[点我跳转](https://github.com/LimeSurvey/LimeSurvey)

## 部署安装

如需在外网也能访问项目，建议配置内网穿透服务。你可以选择 [帕斯内网穿透](https://passnat.com) 这类专业的穿透工具，先注册账号备用。具体的配置步骤可以参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，按照指引轻松实现远程访问。

LimeSurvey 提供了 Docker 部署方案，让你可以快速启动服务。下面是最简便的单命令启动方式：

### 使用 Docker 快速启动

打开你的飞牛 NAS 或其他 NAS 设备

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：limesurvey/limesurvey:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器

### 容器配置说明

**勾选开机自动启动** 确保 NAS 重启后服务能自动恢复运行

**端口映射设置**：

容器内部端口：80（HTTP 服务端口）

主机映射端口：8080（可根据实际情况修改，如果 8080 被占用可改为其他端口如 8081、8082 等）

如果遇到端口冲突提示，只需修改主机端口即可，容器内的 80 端口保持不变

**存储位置配置**：

LimeSurvey 需要持久化存储问卷数据和上传文件，建议创建以下目录映射：

容器内路径 /var/www/html 映射到 NAS 本地目录（用于存储应用文件和数据库）

如果使用外部数据库，可选择映射 /var/www/html/upload 目录用于存储用户上传的附件

**环境变量设置**（可选）：

环境变量 | 说明 | 默认值 | 是否必填

---------|------|--------|-------

`DB\_TYPE` | 数据库类型 | mysql | 可选

`DB\_HOST` | 数据库主机地址 | localhost | 可选

`DB\_PORT` | 数据库端口 | 3306 | 可选

`DB\_NAME` | 数据库名称 | limesurvey | 可选

`DB\_USER` | 数据库用户名 | root | 可选

`DB\_PASSWORD` | 数据库密码 | 空 | 可选

如果你的 NAS 已有 MySQL/MariaDB 数据库，可以通过配置这些环境变量来连接外部数据库，提高系统稳定性。如无特殊需求，保持默认配置即可。

其他参数保持默认设置，直接点击 **启动** 完成部署

### 访问应用

容器启动成功后，在浏览器中访问：http://你的NAS地址:8080

首次访问会进入安装向导，按照提示完成初始化配置（包括管理员账户创建、数据库设置等），完成后即可正常使用 LimeSurvey 创建和管理问卷

### 功能特性速览

📊 **无限问卷和题目**：创建无数量限制的问卷

🎨 **900+ 专业模板**：快速启动各类调查项目

🌍 **多语言支持**：80+ 语言覆盖全球用户

🔐 **企业级安全**：GDPR 合规、数据加密、双因素认证

📈 **高级分析**：内置数据分析和统计工具

🔌 **API 和插件**：支持 REST API、XML-RPC 等集成方式

本篇教程到此结束。祝你使用愉快！

---

原文：<https://www.passnat.com/posts/open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates>
