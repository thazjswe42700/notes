---
title: "自托管短链接服务Snapp，私密数据掌控无忧，NAS极简部署零烦恼，内网穿透远程访问"
date: 2025-10-16
description: "Snapp是功能完整的自托管短链接生成平台，采用现代技术栈构建。支持自定义短码、链接过期时间、密钥保护、详细分析、VirusTotal安全检查、REST API接口、多语言支持及OAuth2.0集成。完全掌控数据，支持Docker快速部署。"
canonical: https://www.passnat.com/posts/self-hosted-url-shortener-snapp-private-data-nas-deployment-intranet-penetration
---

## Snapp - 自托管短链接服务平台

### 项目介绍

Snapp 是一个功能完整的自托管短链接生成平台，专为那些重视隐私和数据控制的用户而设计。它采用现代技术栈构建，包括 Svelte Kit 5、Tailwind CSS 和 ShadCN-Svelte 前端框架，后端使用 Prisma ORM 和 ZenStack 策略引擎，提供企业级的行访问控制和安全认证机制。

相比于商业短链接服务，Snapp 让你完全掌控自己的链接数据。支持自定义短码、链接过期时间、密钥保护、详细的使用分析、VirusTotal 域名安全检查、REST API 接口、多语言支持以及 OAuth 2.0/OIDC 集成。无论是个人博客、企业应用还是 NAS 用户，都可以轻松部署属于自己的短链接服务。

项目地址：[点我跳转](https://github.com/urania-dev/snapp)

### 部署安装

若需要在外网访问你的短链接服务，建议配置内网穿透工具。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册账号备用。具体操作步骤可参考 [帕斯内网穿透全平台快速入门指南](https://www.passnat.com/posts/p)。

#### Docker 快速部署

打开你的 NAS 设备或任何支持 Docker 的系统，进入 Docker 管理界面。

点击「本地镜像」→「添加镜像」→「从 URL 添加」，在镜像地址栏填入：

```
uraniadev/snapp:0.9-rc-029
```

等待镜像下载完成后，点击「启动」按钮创建容器。

**端口配置**

在容器启动向导中，需要设置端口映射。建议将容器内的 3000 端口映射到宿主机的 3000 端口（或其他未被占用的端口）。如果 3000 端口已被占用，可以修改为其他端口号，例如 3001、3002 等，格式为 `宿主机端口:3000`。

**存储配置**

Snapp 使用 SQLite 数据库存储数据。建议在 NAS 上创建一个专用文件夹（例如 `/data/snapp`），然后将容器内的 `/app/dbschema/sqlite/prisma/` 目录挂载到该位置，以确保数据持久化和备份。

**环境变量设置**

以下是必须设置的环境变量：

变量名: ORIGIN, 说明: 实例的源地址, 示例值: https://example.com

变量名: PUBLIC\_URL, 说明: 前端访问的公网地址, 示例值: https://example.com

变量名: DATABASE\_PROVIDER, 说明: 数据库类型, 示例值: sqlite

变量名: DATABASE\_URL, 说明: SQLite 数据库路径, 示例值: file:./db.sqlite

变量名: TOKEN\_SECRET, 说明: JWT 令牌密钥（使用 `openssl rand -base64 32` 生成）, 示例值: 自动生成

变量名: ADMIN\_USERNAME, 说明: 管理员用户名, 示例值: admin

变量名: ADMIN\_PASSWORD, 说明: 管理员密码, 示例值: yourpassword

变量名: ADMIN\_EMAIL, 说明: 管理员邮箱, 示例值: admin@example.com

可选配置项（根据需要设置）：

变量名: ENABLE\_SIGNUP, 说明: 是否允许用户自主注册, 默认值: false

变量名: ENABLED\_MFA, 说明: 启用多因素认证（TOTP）, 默认值: false

变量名: PORT, 说明: 应用监听端口, 默认值: 3000

变量名: ALLOW\_UNSECURE\_HTTP, 说明: 允许非 HTTPS 链接, 默认值: false

变量名: VTAPI\_KEY, 说明: VirusTotal API 密钥（用于域名安全检查）, 默认值: 不填

变量名: SMTP\_HOST, 说明: SMTP 服务器地址, 默认值: 不填

变量名: SMTP\_PORT, 说明: SMTP 端口, 默认值: 465

变量名: SMTP\_USER, 说明: SMTP 账户, 默认值: 不填

变量名: SMTP\_PASS, 说明: SMTP 密码, 默认值: 不填

变量名: SMTP\_FROM, 说明: 邮件发送地址, 默认值: 不填

变量名: PUBLIC\_UMAMI\_WEBSITE\_ID, 说明: Umami 分析网站 ID, 默认值: 不填

变量名: PUBLIC\_UMAMI\_WEBSITE\_URL, 说明: Umami 分析服务地址, 默认值: 不填

其他变量保持默认即可，点击「下一步」启动容器。

**访问应用**

容器启动成功后，在浏览器中访问 `http://你的NAS地址:3000`（或你配置的其他端口），即可看到 Snapp 的登录界面。使用之前设置的管理员账号密码登录，开始创建和管理你的短链接。

### 功能亮点

完全自主控制 - 自托管服务，数据完全掌握在自己手中

安全可靠 - 支持链接密钥保护、多因素认证、域名安全检查

灵活定制 - 自定义短码、设置过期时间、分组管理

数据分析 - 详细的点击统计、访客信息、地理位置追踪

开放接口 - 完整的 REST API，方便与其他系统集成

多语言支持 - 内置意大利语、英语、德语、法语、西班牙语、加利西亚语和中文

### 总结

通过以上步骤，你已经成功在 NAS 或本地服务器上部署了 Snapp 短链接服务。现在你可以创建、管理和分享自己的短链接，同时享受完整的数据控制权和隐私保护。如有任何问题，可参考项目的官方文档或提交 Issue 反馈。

祝你使用愉快！

---

原文：<https://www.passnat.com/posts/self-hosted-url-shortener-snapp-private-data-nas-deployment-intranet-penetration>
