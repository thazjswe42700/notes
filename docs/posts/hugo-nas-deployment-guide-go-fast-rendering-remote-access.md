---
title: "Hugo极速静态网站生成器NAS部署指南,Go语言编写秒速渲染,内网穿透远程访问"
date: 2025-10-16
description: "Hugo是一个用Go语言编写的高性能静态网站生成器，提供数秒内完成网站渲染能力。支持企业官网、博客、文档中心等多种类型建设，内置图片处理、JavaScript打包、Sass编译等强大功能，并支持多语言和Hugo Modules共享内容。"
canonical: https://www.passnat.com/posts/hugo-nas-deployment-guide-go-fast-rendering-remote-access
---

## 项目介绍

Hugo 是一个用 Go 语言编写的高性能静态网站生成器，以其极速的编译能力和灵活的框架设计而闻名。它能在数秒内（通常不到一秒）渲染完整的网站，是构建各类网站的理想选择。

无论你是想搭建企业官网、政府网站、非营利组织网站、教育机构网站、新闻资讯站、活动网站、项目展示页面，还是想创建文档中心、摄影作品集、落地页、个人博客、专业简历等，Hugo 都能胜任。它拥有强大的多语言支持、灵活的分类系统、先进的模板引擎和快速的资源处理管道，让你的创意得以完美呈现。

Hugo 还内置了图片处理、JavaScript 打包、Sass 编译、Tailwind CSS 处理等强大的资源处理功能，并支持通过 Hugo Modules 与其他项目共享内容、主题和配置。

项目地址：[点我跳转](https://github.com/gohugoio/hugo)

## 部署安装

如果你需要在外网访问本地部署的 Hugo 站点，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。详细的部署指南可以 [点击查看](https://www.passnat.com/posts/p)。

### 使用 Docker 快速部署

打开你的 NAS 设备或本地 Docker 环境，执行以下命令即可快速启动 Hugo：

```bash
docker run --rm -it -v $(pwd):/src -v $(pwd)/public:/src/public klakegg/hugo:latest
```

或者，如果你需要运行开发服务器实时预览内容变化：

```bash
docker run --rm -it -v $(pwd):/src -p 1313:1313 klakegg/hugo:latest server
```

### 容器配置说明

**端口映射：**

容器内部服务端口：`1313`
如果端口冲突，可修改命令中的 `-p 1313:1313` 为 `-p 你的端口:1313`

**存储挂载：**

/src - Hugo 项目目录（必须挂载）
/src/public - 生成的静态网站输出目录（可选，用于持久化生成结果）

**环境变量：**

Hugo 基础部署无需特殊环境变量配置，保持默认即可正常使用。

### 访问你的网站

部署完成后，可以通过以下地址访问：

```
http://你的IP:1313
```

开发服务器会自动监听内容变化，当你修改 Markdown 文件或配置时，网站会实时更新。

本篇教程到此结束，祝你使用 Hugo 愉快！

---

原文：<https://www.passnat.com/posts/hugo-nas-deployment-guide-go-fast-rendering-remote-access>
