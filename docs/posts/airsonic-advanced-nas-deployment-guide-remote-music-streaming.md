---
title: "个人音乐流媒体服务器Airsonic-Advanced部署教程，NAS内网穿透远程访问音乐库"
date: 2025-10-16
description: "Airsonic-Advanced是功能强大的个人音乐流媒体服务器，基于Spring Boot构建，可管理数百GB音乐库。支持MP3、AAC、OGG等多种格式，提供网页界面和多设备播放。本文详解Docker部署步骤、端口配置、存储映射和环境变量设置，帮助用户快速搭建个人音乐流媒体服务，随时随地享受高品质音乐体验。"
canonical: https://www.passnat.com/posts/airsonic-advanced-nas-deployment-guide-remote-music-streaming
---

## 项目介绍

Airsonic-Advanced 是一款功能强大的个人音乐流媒体服务器，基于现代化的 Java 框架（Spring Boot 2.x + Spring Framework 5.x）构建。它是 Airsonic 的高级版本，专为处理大规模音乐库而设计，可以轻松管理数百GB的音乐收藏。

这个项目让你可以随时随地通过网页界面访问自己的音乐库，支持多设备同时播放。无论是在办公室、家里厨房还是客厅，都能享受高品质的音乐体验。Airsonic-Advanced 不仅支持MP3、AAC、OGG等常见格式，还通过转码插件支持WMA、FLAC、APE、Musepack等多种音频格式的即时转换和流媒体播放。

项目地址：[点我跳转](https://github.com/airsonic-advanced/airsonic-advanced)

## 部署安装

如果需要在外网也能访问你的音乐服务器，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先在官网注册账号。具体的配置步骤可以参考 [这份完整攻略](https://www.passnat.com/posts/p)，涵盖全平台使用方法。

### 快速部署指南

打开你的飞牛NAS或其他支持Docker的NAS设备
点击'本地镜像' - '添加镜像' - '从URL添加'
在镜像地址栏填入：airsonic/airsonic-advanced:latest
等待镜像下载完成后，点击启动按钮启动容器
勾选'开机自动启动'选项，然后进入下一步

### 端口配置

在端口设置界面进行如下配置：

容器内端口：8080（这是应用默认监听的端口，通常无需修改）
宿主机端口：可设置为 8080 或其他未被占用的端口（如8081、8082等）

如果提示端口被占用，只需修改宿主机端口号即可，容器内部端口保持8080不变。

### 存储位置配置

Airsonic-Advanced 需要持久化存储以保存配置和数据库信息。建议在NAS上创建以下文件夹并进行映射：

/music - 用于存放你的音乐文件（映射到NAS上的音乐库目录）
/var/airsonic - 用于存储应用配置和数据库文件（映射到NAS上的数据目录）
/var/airsonic/Podcasts - 播客存储目录（可选）

### 环境变量设置

**必须设置：**

JAVA\_OPTS：Java虚拟机参数（默认值：-Xmx512m，根据NAS内存调整，建议为可用内存的50%）

**可选设置：**

AIRSONIC\_PORT：应用监听端口（默认：8080）
AIRSONIC\_CONTEXT\_PATH：应用上下文路径（默认：/airsonic，可留空）
AIRSONIC\_DEFAULT\_MUSIC\_FOLDER：默认音乐文件夹路径

其他变量保持默认即可，直接下一步启动容器。

### 访问应用

创建成功后，在浏览器中访问：http://你的NAS\_IP:8080

首次访问会进入初始化界面，使用默认用户名 admin 和密码 admin 登录（强烈建议首次登录后立即修改密码）

到这里你就可以开始配置音乐库、添加播放器、享受个人流媒体服务了。

## 总结

Airsonic-Advanced 以其卓越的性能和丰富的功能，为音乐爱好者提供了一个完整的个人音乐服务解决方案。通过简单的Docker部署，你可以在几分钟内搭建属于自己的音乐流媒体服务器，随时随地畅享音乐。

本篇教程结束

---

原文：<https://www.passnat.com/posts/airsonic-advanced-nas-deployment-guide-remote-music-streaming>
