---
title: "自建密码生成器Docker一键部署，支持离线模式安全检测泄露，NAS极简配置内网穿透远程访问"
date: 2025-10-16
description: "发现生成密码神器PwGen，支持Docker一键部署。内置HaveIBeenPwned泄露库检查，可生成随机密码和密码短语，支持离线模式、本地设置保存、多语言和PWA体验。环境变量可自定义密码长度、字符类型等参数，轻松部署即刻使用。"
canonical: https://www.passnat.com/posts/self-hosted-password-generator-docker-one-click-deploy-offline-mode-security-breach-detection-nas-simple-config-intranet-penetration-remote-access
---

## 项目介绍

PwGen 是一款功能强大的在线密码生成器应用，采用 Docker 容器化部署方案。这个项目提供了一个简洁易用的网页界面，让你可以轻松生成安全可靠的密码或密码短语。无论你是需要为账户创建强密码，还是想要生成易于记忆的密码组合，PwGen 都能满足你的需求。

项目支持多种密码生成模式，包括随机密码和单词组合密码短语，可自定义密码长度、字符类型、大小写、数字和特殊符号等参数。更重要的是，它内置了与 HaveIBeenPwned 数据库的集成，能够实时检查你生成的密码是否曾被泄露过，确保你使用的密码真正安全可靠。此外，项目还支持离线模式、本地设置保存、多语言支持（英文、芬兰文、法文）以及 PWA 渐进式网页应用体验。

项目地址：[点我跳转](https://github.com/jocxfin/pwgen)

## 部署安装

如果你需要在外网也能访问这个密码生成器，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备好。具体操作步骤可参考 [内网穿透全平台快速指南](https://www.passnat.com/posts/p)，轻松实现随处可得的安全密码生成体验。

### 最快速部署方式

打开你的 NAS 设备或任何支持 Docker 的系统，执行以下命令即可一键启动：

```bash
docker run -d -p 5069:5069 jocxfin/pwgen:latest
```

就这么简单！容器会在后台运行，无需任何额外配置。

### 启用离线模式

如果你的网络环境受限或希望增强隐私保护，可以禁用在线密码泄露检查功能：

```bash
docker run -d -p 5069:5069 -e NO_API_CHECK=true jocxfin/pwgen:latest
```

### 自定义配置部署

如果你想精细化控制密码生成的默认参数，可以通过环境变量来设置：

```bash
docker run -d -p 5069:5069 \
  -e NO_API_CHECK=false \
  -e PW_LENGTH=16 \
  -e PW_INCLUDE_UPPERCASE=true \
  -e PW_INCLUDE_DIGITS=true \
  -e PW_INCLUDE_SPECIAL=true \
  -e PW_EXCLUDE_HOMOGLYPHS=true \
  -e PP_WORD_COUNT=4 \
  -e PP_CAPITALIZE=true \
  -e PP_SEPARATOR_TYPE=dash \
  -e PP_LANGUAGE=en \
  -e MULTI_GEN=true \
  -e SHOW_SAVE_SETTINGS=true \
  jocxfin/pwgen:latest
```

## 环境变量说明

必须设置的环境变量：

无 - 项目开箱即用，所有参数都有合理的默认值。

可选设置的环境变量：

变量名 | 说明 | 默认值
NO\_API\_CHECK | 禁用 HaveIBeenPwned 检查（离线模式） | false
PW\_LENGTH | 随机密码长度 | 12
PW\_INCLUDE\_UPPERCASE | 密码中包含大写字母 | true
PW\_INCLUDE\_DIGITS | 密码中包含数字 | true
PW\_INCLUDE\_SPECIAL | 密码中包含特殊符号 | true
PW\_EXCLUDE\_HOMOGLYPHS | 排除易混淆的字符（如 0/O、1/l） | false
PP\_WORD\_COUNT | 密码短语中的单词数量 | 3
PP\_CAPITALIZE | 密码短语中单词首字母大写 | true
PP\_SEPARATOR\_TYPE | 单词分隔符类型（space/dash/number/special） | space
PP\_LANGUAGE | 密码短语语言（en/fi/fr） | en
PP\_HIDE\_LANG | 隐藏语言选择下拉菜单 | false
MULTI\_GEN | 同时生成多个密码（最多5个） | false
SHOW\_SAVE\_SETTINGS | 显示本地设置保存选项 | true
ROBOTS\_ALLOW | 允许搜索引擎爬虫索引 | false

## 访问应用

部署完成后，打开浏览器访问：

```
http://你的设备IP:5069
```

例如，如果你的 NAS 地址是 192.168.1.100，则访问 http://192.168.1.100:5069 即可进入密码生成器界面。

界面加载后，你就可以：
选择生成随机密码或密码短语
自定义各种参数设置
一键复制生成的密码
检查密码是否被泄露
保存你的偏好设置到浏览器本地

到此部署完成，尽情享受安全便捷的密码生成体验吧！

---

原文：<https://www.passnat.com/posts/self-hosted-password-generator-docker-one-click-deploy-offline-mode-security-breach-detection-nas-simple-config-intranet-penetration-remote-access>


## 相关文章

- [家常菜谱聚合平台来做菜，材料有限也能做美食，支持离线PWA，NAS一键Docker部署](home-cooking-recipes-pwa-nas-docker-deployment.md)
- [自托管私人相册HomeGallery，AI智能识别，NAS极简部署，内网穿透远程访问](home-gallery-private-photos-ai-recognition-nas-deployment-remote-access.md)
- [SideQuests 任务管理系统 Docker 一键部署，NAS 极简配置内网穿透远程访问任务](sidequests-docker-nas-deployment-intranet-penetration.md)
- [Cogito个人知识库系统自建指南，NAS极简部署智能笔记管理工具，支持内网穿透远程访问](1-seo-slug-aa-bb-cc2-cogitoseoseo-slug------slug.md)
- [自托管笔记书签管理Benotes Docker部署指南，NAS轻松搭建私有知识库](self-hosted-notes-bookmarks-benotes-docker-nas-private-knowledge-base.md)
