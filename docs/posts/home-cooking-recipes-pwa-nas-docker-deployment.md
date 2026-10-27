---
title: "家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署"
date: 2025-10-16
description: "来做菜是专为居家烹饪打造的菜谱聚合平台，支持多条件筛选快速查找菜肴。拥有海量家常菜谱、PWA离线支持、Docker快速部署等功能特性。无论厨房小白还是烹饪爱好者，都能轻松找到合适的菜肴，让烹饪变得简单便捷。"
canonical: https://www.passnat.com/posts/home-cooking-recipes-pwa-nas-docker-deployment
---

## 来做菜 - 居家菜谱聚合平台

### 项目介绍

**来做菜** 是一款专为居家烹饪打造的菜谱聚合平台，致力于帮助用户在材料有限的情况下，也能轻松找到合适的菜肴。项目采用现代化技术栈构建，支持网页访问和离线使用，让烹饪变得更加简单便捷。

无论你是厨房小白还是烹饪爱好者，都能在这里找到适合自己的菜谱。应用界面简洁清爽，菜谱数据丰富齐全，支持多条件筛选，让你快速定位心仪的菜肴。最贴心的是，它支持 PWA 技术，可以像原生应用一样添加到手机主屏幕，随时随地查看菜谱。

项目地址：[点我跳转](https://github.com/YunYouJun/cook)

## 部署安装

如果你想在自己的服务器或 NAS 上部署这个项目，为了能在外网也能访问到项目，建议配置内网穿透服务。可以使用 [帕斯内网穿透](https://passnat.com) 来实现这一需求，先注册好账号备用。具体的操作指南可以 [点我查看](https://www.passnat.com/posts/p) 内网穿透全平台快速上手手册。

#### 使用 Docker 快速部署

这是最简单快速的部署方式，只需一条命令即可完成：

**第一步：拉取镜像**

打开你的 NAS 设备或服务器终端，执行以下命令拉取最新的 Docker 镜像：

```bash
docker pull yunyoujun/cook:latest
```

**第二步：启动容器**

镜像下载完成后，使用以下命令启动容器：

```bash
docker run -it -d --name cook -p 8080:80 yunyoujun/cook:latest
```

**第三步：访问应用**

启动成功后，打开浏览器访问 `http://你的IP地址:8080`，即可看到来做菜的界面。

**端口说明：** 上述命令中 `-p 8080:80` 表示将容器内的 80 端口映射到主机的 8080 端口。如果 8080 端口已被占用，你可以修改前面的数字，例如改为 `-p 8081:80` 或其他未被占用的端口。

**启动与停止：**

容器启动后，如需重启或停止，可使用以下命令：

```bash
# 启动容器
docker start cook

# 停止容器
docker stop cook
```

## 功能特性

✨ 菜谱丰富 - 精选海量家常菜谱，涵盖各类烹饪风格

🔍 智能筛选 - 根据材料、难度等多维度快速查找菜肴

📱 PWA 支持 - 支持添加到主屏幕，获得类原生应用体验

💾 离线可用 - 支持离线浏览已加载的菜谱内容

🎯 材料优化 - 菜谱材料经过精心筛选，适合居家烹饪

## 总结

通过上述简单的 Docker 部署步骤，你就能拥有一个属于自己的菜谱平台。无论是个人使用还是家庭共享，来做菜都能为你的烹饪生活增添便利。赶快部署起来，开启你的居家美食之旅吧！

本篇教程结束

---

原文：<https://www.passnat.com/posts/home-cooking-recipes-pwa-nas-docker-deployment>


## 相关文章

- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [自建密码生成器Docker一键部署，支持离线模式安全检测泄露，NAS极简配置内网穿透远程访问](self-hosted-password-generator-docker-one-click-deploy-offline-mode-security-breach-detection-nas-simple-config-intranet-penetration-remote-access.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
- [SideQuests 任务管理系统 Docker 一键部署，NAS 极简配置内网穿透远程访问任务](sidequests-docker-nas-deployment-intranet-penetration.md)
