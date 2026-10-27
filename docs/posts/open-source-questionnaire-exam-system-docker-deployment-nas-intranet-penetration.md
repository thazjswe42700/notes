---
title: "开源问卷考试系统卷王Docker部署指南，NAS一键安装私有问卷平台，支持内网穿透远程访问"
date: 2025-10-16
description: "卷王是功能最强大的开源问卷调查和在线考试系统，支持20+题型、高级逻辑设置、公式自定义、多人协作。完全免费开源，支持私有部署，响应式设计完美适配PC和移动端，数据安全可控。"
canonical: https://www.passnat.com/posts/open-source-questionnaire-exam-system-docker-deployment-nas-intranet-penetration
---

## 项目介绍

卷王是一款功能最强大的开源调查问卷系统和在线考试系统。它集问卷调查、在线考试、投票等多种功能于一身，拥有超过20种丰富的题型支持，包括填空、选择、下拉、级联、矩阵、分页、签名、题组、文件上传等。

这个项目的亮点在于其强大的逻辑设置能力，支持显示隐藏逻辑、值计算逻辑、文本替换、值校验、必填判断、选项自动勾选、跳转逻辑等高级功能，甚至支持通过公式实现自定义逻辑，这些功能的强大程度超过目前主流的商业问卷产品。

项目采用响应式设计，完美适配电脑端和移动端，支持多人协作管理问卷，后端支持多种关系型数据库，具备完善的RBAC权限控制系统。最重要的是，卷王是完全开源免费的，支持私有部署，你可以在自己的服务器上搭建属于自己的问卷考试系统。

项目地址：[点我跳转](https://github.com/javahuang/SurveyKing)

## 部署安装

如果你需要在外网也能访问你的问卷系统，建议配置内网穿透工具来实现远程访问。你可以前往 [帕斯内网穿透](https://passnat.com) 注册账号，这样即使在公司或出差也能随时管理你的问卷。详细的操作步骤可以 [点击这里查看](https://www.passnat.com/posts/p)，他们提供了全平台的详细上手攻略。

### Docker 一键部署

卷王提供了最简便的Docker部署方式，只需一条命令即可快速启动：

```bash
docker run -p 1991:1991 surveyking/surveyking
```

**部署步骤：**

1. 打开NAS设备或服务器

如果你使用飞牛NAS或其他NAS设备，进入Docker管理界面

点击'本地镜像' → '添加镜像' → '从URL添加'

2. 添加镜像

在镜像地址栏填入：surveyking/surveyking

等待镜像下载完成后点击启动

3. 容器配置

**开机自启**：勾选'开机自动启动'，这样重启后容器会自动运行

**端口映射**：设置容器端口为 1991，如果该端口已被占用，可修改为其他端口（如 8888:1991），但容器内部端口需保持为 1991

**存储位置**：无需特殊配置，系统会自动处理数据存储

4. 环境变量设置

该项目无需设置特殊的环境变量，保持默认配置即可正常运行。

5. 启动完成

点击下一步完成配置后启动容器

等待几秒钟容器启动完成

6. 访问应用

打开浏览器访问：http://你的IP:1991

使用默认账号密码登录：**admin / 123456**

⚠ **登录后第一件事就是改掉这个密码** —— 这个实例等一下要挂到公网上，默认口令等于没有口令，等于把问卷和考试数据向所有人敞开。

登录后即可开始创建问卷和考试

**端口说明：**

如果你的服务器或NAS上1991端口已被其他应用占用，可以修改映射规则

例如：将 8888:1991 设置为容器外部端口为8888，内部端口为1991

然后访问 http://你的IP:8888 即可

到此，卷王问卷考试系统就已经部署完成了。你现在可以开始创建属于自己的问卷和考试，无需依赖任何第三方平台，所有数据都掌握在自己手中。

本篇教程结束

---

原文：<https://www.passnat.com/posts/open-source-questionnaire-exam-system-docker-deployment-nas-intranet-penetration>


## 相关文章

- [SurveyKing开源问卷考试系统一键部署指南 无限题型支持NAS私有部署免费使用](survey-king-open-source-questionnaire-exam-system-one-click-deployment-guide-unlimited-question-types-nas-private-deployment-free.md)
- [开源问卷调查系统LimeSurvey，NAS一键部署，内网穿透远程访问，无限问卷模板](open-source-survey-system-limesurvey-nas-one-click-deployment-intranet-penetration-remote-access-unlimited-questionnaire-templates.md)
- [开源私有视频会议系统Nettu Meet，Docker一键部署，支持屏幕共享和内网穿透远程访问](open-source-nettu-meet-video-conference-docker-deployment.md)
- [开源日程管理系统Cal.com自建部署，完全控制数据NAS本地托管，支持内网穿透远程访问](open-source-calendar-management-cal-com-self-hosted-deployment-local-nas-storage-intranet-penetration-remote-access.md)
- [免费私有相册系统Lomorage，Docker快速部署NAS，内网穿透远程访问家庭影库](lomorage-free-private-photo-gallery-docker-nas-deployment-remote-access.md)
