---
title: "多协议文件传输服务 SFTPGo，NAS 极简部署支持 SFTP FTP WebDAV，内网穿透远程访问"
date: 2025-10-16
description: "发现高效安全的文件传输解决方案。SFTPGo支持SFTP、HTTP/S、FTP/S、WebDAV多协议，兼容本地、S3、Google Cloud Storage等存储后端。功能完整的WebAdmin管理界面和WebClient用户界面让文件管理变得简单易用。支持双因素认证，为你的文件交换提供企业级安全保障。"
canonical: https://www.passnat.com/posts/sftp-go-nas-sftp-ftp-webdav-remote-access
---

## 项目介绍

SFTPGo 是一个功能完整、高度可配置的事件驱动型文件传输解决方案。它支持多种服务器协议（SFTP、HTTP/S、FTP/S、WebDAV）和多种存储后端（本地文件系统、加密本地文件系统、S3 兼容对象存储、Google Cloud Storage、Azure Blob Storage 等），让你可以灵活地与内部团队或业务伙伴进行文件交换和存储。

SFTPGo 提供了功能强大的 WebAdmin 管理界面，让你轻松创建和管理用户、文件夹、用户组等资源。同时还有 WebClient 用户界面，允许最终用户在浏览器中修改凭证、浏览和管理文件，以及设置支持 Microsoft Authenticator、Google Authenticator、Authy 等应用的双因素认证。

无论你是在本地部署还是云端使用，SFTPGo 都能提供安全、可靠的文件传输体验。

项目地址：[点我跳转](https://github.com/drakkan/sftpgo)

## 部署安装

如需在外也打开项目需要配置内网穿透使用，点击前往[帕斯内网穿透](https://passnat.com)，先注册好账号备用。
具体的操作方法可以[点我查看](https://www.passnat.com/posts/p)内网穿透全平台上手攻略

### 使用 Docker 快速部署

打开飞牛 NAS 或其他的 NAS 设备，点击本地镜像-添加镜像-从 URL 添加。

镜像填drakkan/sftpgo:latest等待下载完成后点击启动按钮启动容器。

**端口设置**

SFTPGo 需要映射多个端口以支持不同的协议：

8080：WebAdmin 管理界面和 WebClient 用户界面的访问端口（容器内端口）

2022：SFTP 协议端口（容器内端口）

21：FTP 协议端口（容器内端口，可选）

443：HTTPS 端口（容器内端口，可选）

根据你的需求选择需要映射的端口。如果提示端口被占用，可以修改宿主机端口（冒号前的数字），但容器内端口需要保持不变。例如，如果 8080 被占用，可以改为8888:8080。

**存储位置**

SFTPGo 需要以下目录用于数据持久化：

/srv/sftpgo/data：用户数据和文件存储目录（重要）

/var/lib/sftpgo：数据库和配置文件存储目录（重要）

/srv/sftpgo/backups：备份文件存储目录（可选）

请在 NAS 上创建这些文件夹，并将它们分别映射到容器内的对应路径。

**环境变量设置**

SFTPGo 的环境变量配置相对简单，主要配置如下：

必须设置：

SFTPGO\_LOG\_FILE\_PATH：日志文件路径，建议设置为空字符串""以输出到标准输出（便于查看 Docker 日志）

可选设置：

SFTPGO\_COMMON\_\_UPLOAD\_MODE：上传模式，默认为0

SFTPGO\_COMMON\_\_ACTIONS\_\_EXECUTE\_ON：执行操作的触发条件

SFTPGO\_COMMON\_\_DEFENDER\_\_ENABLED：是否启用防御机制，默认为true

其他环境变量可根据需要在官方文档中查阅。如果不需要特殊配置，可以不设置任何环境变量，使用默认配置即可。

**启动容器**

完成上述配置后，点击下一步启动容器。勾选开机自动启动选项，容器将在 NAS 重启后自动启动。

**访问应用**

容器启动成功后，可以通过以下地址访问 SFTPGo：

WebAdmin 管理界面：http://你的NAS\_IP:8080/admin

WebClient 用户界面：http://你的NAS\_IP:8080/client

首次访问时，系统会提示你设置管理员账号和密码。设置完成后即可开始使用 SFTPGo 管理文件和用户。

## 总结

通过上述步骤，你已经成功在 NAS 上部署了 SFTPGo。现在你可以：

创建多个用户账号，为不同的人员分配不同的文件访问权限

支持 SFTP、FTP、HTTP 等多种协议，满足不同客户端的需求

利用云存储后端（如 S3、Google Cloud Storage 等）扩展存储容量

通过 WebClient 界面让用户在浏览器中直接管理文件

启用双因素认证增强安全性

SFTPGo 是一个功能强大且易于部署的文件传输解决方案，无论是个人使用还是企业级应用都能胜任。

本篇教程结束

---

原文：<https://www.passnat.com/posts/sftp-go-nas-sftp-ftp-webdav-remote-access>
