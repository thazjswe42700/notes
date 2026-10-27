---
title: "Reactive Resume 自建简历工作台：飞牛 NAS 部署与公网使用"
date: 2026-10-15
description: "飞牛 NAS 上部署 Reactive Resume 简历工作台，附数据库编排、网页访问口令、中文排版、PDF 导出和手机修改步骤。"
canonical: https://www.passnat.com/posts/reactive-resume-fnos-private-resume-builder-passnat
---

## 把简历工作台放在自己的 NAS

Reactive Resume 用来编辑简历、选择版式并导出 PDF。简历内容保存在自己的 NAS；换台电脑或使用手机，打开同一工作台继续修改。本文使用虚构演示资料，不包含真实联系方式。

## 先准备公网访问地址

[帕斯内网穿透](https://passnat.com)用于把家里的简历工作台开放到公网。

飞牛自带的应用中心里搜索 PassNAT 内网穿透插件，安装并打开。创建一条名为 resume 的隧道，选择 HTTP 协议，填写自己的子域名并选择顶级域名。本地地址保留 127.0.0.1，本地端口填 3501。设置网页访问用户名和口令后提交；公网地址先保存下来，暂时不打开电源。

## 在飞牛部署

文件管理的我的文件下准备一个空文件夹。Docker 的 Compose 中点新增项目，名称填 resume，路径选择刚才的空文件夹，来源选创建 docker-compose.yml，粘贴下面的编排。

```yaml
services:
  database:
    image: postgres:latest
    restart: unless-stopped
    environment:
      POSTGRES_USER: resume
      POSTGRES_PASSWORD: change-me-db-password
      POSTGRES_DB: resume
    volumes:
      - ./database:/var/lib/postgresql
    healthcheck:
      test: [CMD-SHELL, "pg_isready -U resume -d resume"]
      interval: 5s
      timeout: 5s
      retries: 12
  resume:
    image: amruthpillai/reactive-resume:latest
    restart: unless-stopped
    environment:
      DATABASE_URL: postgresql://resume:change-me-db-password@database:5432/resume
      APP_URL: http://换成隧道公网地址
      AUTH_SECRET: change-me-auth-secret-at-least-32-characters
      STORAGE_BACKEND: local
      FLAG_DISABLE_SIGNUPS: "false"
    ports:
      - "3501:3000"
    volumes:
      - ./data:/app/data
    depends_on:
      database:
        condition: service_healthy
```

把 POSTGRES\_PASSWORD 与 DATABASE\_URL 中的 change-me-db-password 一起换成同一个数据库口令，建议使用随机字母数字。APP\_URL 填刚创建隧道得到的完整公网地址，保留 http://。AUTH\_SECRET 换成至少 32 个字符的随机密钥；它与数据库口令分别设置。3501 是 NAS 对外端口，3000 是容器内部端口。

database 与 data 两个目录分别保存数据库和工作台文件，备份时都要保留。勾选创建项目后立即启动并确认，等待镜像拉取和容器启动。

## 打开工作台并创建自己的简历

确认容器启动后，回 PassNAT 插件给本期隧道通电。绿点亮起后，用公网地址打开；浏览器先询问网页访问口令，填写创建隧道时设置的那一份。应用内创建自己的账户后即可使用，账号注册不需要另行部署邮件服务。访问口令请自行保管。

设置的偏好设置中将界面语言改为中文（简体）。新建一份简历，为它取名，按栏目填写基本信息、工作经历和项目。演示中的小林与示例工作室均为虚构资料，请替换成自己的经历。

## 选择版式与导出 PDF

在撰写页修改字段并查看预览，切到设计页选择模板、字号与页边距。中文简历可选择 Noto Sans SC 字体。点击下载为 PDF 文档，将文件保存到电脑，再打开检查姓名、工作经历和分页是否完整。

## 手机上接着修改

在手机浏览器打开同一个公网地址，通过访问口令后进入自己的工作台。打开已有简历修改求职方向，等内容保存后再下载 PDF，就能处理临时补充的投递材料。

本教程使用官方 amruthpillai/reactive-resume:latest 镜像，数据库沿用上游示例的 postgres:latest。对象存储与 AI 服务均不是本期必需项；本期使用本地存储，不需要外部 AI 密钥。

---

原文：<https://www.passnat.com/posts/reactive-resume-fnos-private-resume-builder-passnat>
