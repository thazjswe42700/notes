# NAS 实操笔记

NAS / Docker / 内网穿透的部署文档。每篇都能照着做完：完整 compose、必改配置项、常见报错与排查。

## 文章

### [I Hate Money：NAS 上用 Docker 自建共同开销记账](posts/ihatemoney-nas-docker.md)

几个人出去玩、合租、一起吃饭，钱怎么分清楚。自托管、数据在自己 NAS 上、手机浏览器就能记账，记完自动给出「谁付给谁多少」的结算建议。

`Docker Compose` · `SQLite` · `内网穿透` · 单容器

### [云微 WechatOnCloud：把微信挂在 NAS 上，浏览器随时随地收发消息](posts/wechat-on-nas-docker.md)

在自己的 NAS 上跑一个官方原版电脑微信，浏览器扫码登录、收发消息、传文件。面板 + 实例结构，还能挂 QQ 和常驻浏览器。

`Docker Compose` · `面板 + 实例` · `内网穿透` · 约 0.6 GB/实例

## 阅读方式

- 想直接跑起来 → 每篇的「快速开始」
- 卡在某一步 → 每篇的「常见问题」，报错原文都在里面
- 关心安全 → 每篇的「安全与暴露面」

## 关于

- compose 均在飞牛 NAS（fnOS）实测跑通，其它支持 Docker 的 NAS 同理
- 外网访问用 [帕斯内网穿透](https://passnat.com/)，可替换为任何等价方案
- 更多文章：<https://www.passnat.com/>

内容采用 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 许可。
