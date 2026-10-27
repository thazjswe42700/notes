---
title: "免费开源简历生成器OpenResume Docker部署教程，ATS兼容专业简历制作工具"
date: 2025-10-16
description: "OpenResume是免费开源简历生成器，采用Next.js+React+TypeScript技术构建。支持简历制作、PDF导入、ATS兼容性检测，完全本地运行无需注册。用户已获得Google、Meta等顶级公司面试机会。本文详细介绍Docker快速部署方式，端口配置说明及使用指南，帮助你轻松部署并创建专业ATS友好的国际标准简历。"
canonical: https://www.passnat.com/posts/free-open-source-resume-generator-openresume-docker-deployment-tutorial-ats-compatible-professional-resume-creation-tool
---

## 项目介绍

OpenResume 是一个功能强大的开源简历生成器和简历解析工具，致力于为每一位求职者提供免费、专业的简历设计方案。这个项目采用 Next.js 13 + React + TypeScript + Tailwind CSS 技术栈构建，集简历制作、PDF导入、ATS兼容性检测于一身。

无论你是初次求职还是职场老手，OpenResume 都能帮助你在几分钟内创建一份符合国际标准、ATS友好的专业简历。项目完全本地运行，无需注册账号，你的个人信息永远不会上传到云端。更棒的是，OpenResume 的用户已经成功获得来自 Dropbox、Google、Meta 等顶级科技公司的面试机会和 Offer。

项目地址：[点我跳转](https://github.com/xitanggg/open-resume)

## 部署安装

如果你想在办公室、家里或任何地方随时访问自己部署的 OpenResume，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com) 来实现远程访问，先注册账号备用。详细的操作指南可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手教程。

### Docker 快速启动（推荐）

这是最简单快速的部署方式，只需一条命令即可启动：

打开你的 NAS 设备或服务器，进入 Docker 管理界面。

点击 **本地镜像** - **添加镜像** - **从 URL 添加**

在镜像地址栏填入：xitanggg/open-resume:latest

等待镜像下载完成后，点击 **启动** 按钮启动容器。

### 端口配置说明

在启动容器前，需要配置端口映射。OpenResume 内部运行在 **3000 端口**，你可以选择将其映射到宿主机的任意端口。

如果你的 3000 端口未被占用，直接映射为 3000:3000 即可

如果 3000 端口已被其他应用占用，可修改为 8080:3000、9000:3000 等其他端口

记住：冒号前面是宿主机端口（你访问时使用），冒号后面是容器内端口（固定为 3000，不要改）

### 存储配置

OpenResume 完全在浏览器本地运行，不需要额外的数据持久化目录，所以无需创建特殊的存储映射。

### 环境变量

OpenResume 无需设置任何环境变量，保持默认配置即可正常运行。

### 启动完成

配置完成后点击 **启动**，等待容器启动成功。在浏览器中访问 http://你的IP:3000（如果映射了其他端口，请替换为对应端口号），即可看到 OpenResume 的界面。

到这里就可以开始制作你的专业简历了！

本篇教程结束

---

原文：<https://www.passnat.com/posts/free-open-source-resume-generator-openresume-docker-deployment-tutorial-ats-compatible-professional-resume-creation-tool>


## 相关文章

- [Reactive Resume 自建简历工作台：飞牛 NAS 部署与公网使用](reactive-resume-fnos-private-resume-builder-passnat.md)
- [自建 BentoPDF：飞牛 NAS Docker 部署隐私优先的 PDF 工具箱，内网穿透外网访问](bentopdf-self-hosted-pdf-toolkit-nas-docker-deployment-intranet-penetration.md)
- [开源FileGator自托管文件管理系统，无需数据库轻松部署，支持多用户协作与云存储](open-source-filegator-self-hosted-file-management-system-no-database-easy-deployment-multi-user-collaboration-cloud-storage.md)
- [PDFMathTranslate 整篇翻译：NAS Docker 部署 + 内网穿透](pdfmathtranslate-nas-docker-pdf-translate-formula-layout-intranet-penetration.md)
- [DocuSeal 自建电子签署：NAS Docker 部署 + 签署链接怎么发](docuseal-nas-docker-e-signature-contract.md)
