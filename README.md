# notes

NAS / Docker / 内网穿透的实操笔记。每篇都是能照着做完的部署文档：完整 compose、必改配置项、常见报错和排查思路。

> 站点：<https://thazjswe42700.github.io/notes/>　·　原文与更多文章：<https://www.passnat.com/>

## 文章

| 文章 | 主题 | 技术栈 |
|---|---|---|
| [I Hate Money：自托管共同开销记账](docs/posts/ihatemoney-nas-docker.md) | 合租 / 出游 AA 记账，自动算分摊与结算 | Docker Compose · SQLite · 内网穿透 |
| [云微：把微信挂在 NAS 上，浏览器里用](docs/posts/wechat-on-nas-docker.md) | 官方原版微信跑在容器里，网页扫码收发消息 | Docker Compose · 面板 + 实例 · 内网穿透 |

## 怎么读

- 想直接跑起来：看每篇的 **快速开始**，复制 compose、改两三个变量、启动
- 卡在某一步：看 **常见问题**，报错原文都在里面
- 关心安全：看 **安全与暴露面** 一节，公网地址、密码、白名单都在这

## 关于这些笔记

- 所有 compose 都在飞牛 NAS（fnOS）上实测跑通，其它能跑 Docker 的 NAS 同理
- 需要从外网访问的部分，用的是 [帕斯内网穿透](https://passnat.com/)，也可以用任何等价方案替换
- 文档型写法：只写能复现的事实，不写感受

## License

文档内容采用 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh)，compose 片段可自由取用。
