---
title: "直播录制神器DouyinLiveRecorder，50+平台一键自动守护，NAS循环值守永不错过精彩"
date: 2025-10-16
description: "DouyinLiveRecorder是功能强大的多平台直播录制工具，支持抖音、TikTok、快手、B站、虎牙等50+平台自动录制。独有循环值守功能，主播上线即自动录制，让你轻松备份喜爱主播精彩内容。支持Docker一键部署到NAS，配置简单，永久自动化运行。"
canonical: https://www.passnat.com/posts/douyin-live-recorder-50-platforms-automatic-recording-nas
---

## 直播录制神器 DouyinLiveRecorder - 一键守护你的喜爱主播

### 项目介绍

DouyinLiveRecorder 是一款功能强大的多平台直播录制工具，基于 FFmpeg 实现，支持超过 50+ 个直播平台的自动化录制。无论你想录制抖音、TikTok、快手、B站、虎牙还是国际直播平台，这个工具都能轻松搞定。

它最大的特点是**支持循环值守**——只需配置好直播间链接，软件就会自动监测主播是否上线，一旦开播立即自动录制，让你再也不用担心错过精彩直播。支持的平台包括：抖音、TikTok、快手、虎牙、斗鱼、YY、B站、小红书、Twitch、YouTube 等超过 50 个平台，且还在持续更新中。

项目地址：[点我跳转 GitHub](https://github.com/ihmily/DouyinLiveRecorder)

### 部署安装

如果需要在外网也能访问录制的直播内容或进行远程管理，建议配置内网穿透服务。推荐使用 [帕斯内网穿透](https://passnat.com)，先注册好账号。具体操作步骤可参考 [内网穿透全平台上手攻略](https://www.passnat.com/posts/p)，让你随时随地都能管理录制任务。

#### Docker 快速部署

这是最简单的部署方式，特别适合技术小白用户。

**第一步：获取 Docker 镜像**

打开你的 NAS 设备或 Docker 管理界面，点击「本地镜像」→「添加镜像」→「从 URL 添加」

在镜像地址栏填入：ihmily/douyin-live-recorder:latest

等待镜像下载完成后，点击「启动」按钮启动容器。

**第二步：配置基本参数**

勾选「开机自动启动」确保服务稳定运行，然后进入下一步。

**端口设置**

容器默认使用端口 8000，这是程序内部运行的端口。如果你的 NAS 上已经有其他服务占用了 8000 端口，可以修改为其他端口号（比如 8001、8002 等），但容器内的端口保持不变即可。

**存储位置配置**

为了保存录制的视频文件和配置信息，需要创建本地存储映射：

创建一个文件夹用于存放配置文件和录制的视频（建议命名为 'douyin-recorder'）

将该文件夹映射到容器内的 '/app' 目录

这样所有录制的视频和配置文件都会保存在你的 NAS 本地，方便管理和备份。

**环境变量设置（可选）**

大多数情况下你不需要设置环境变量，直接下一步启动即可。如果需要特殊配置，可参考以下可选项：

'TZ'：时区设置（默认已配置为 'Asia/Shanghai'，无需修改）

其他高级配置项通常通过配置文件而非环境变量设置

**第三步：启动并验证**

点击启动容器后，等待几秒钟让程序完全启动。

容器启动后，你就可以通过浏览器访问：'http://你的NAS地址:8000' 来管理录制任务。

**第四步：添加直播间链接**

进入程序后，在配置文件中添加你想录制的直播间链接。支持的链接格式包括：

抖音：'https://live.douyin.com/745964462470'

TikTok：'https://www.tiktok.com/@pearlgaga88/live'

快手：'https://live.kuaishou.com/u/yall1102'

B站：'https://live.bilibili.com/320'

虎牙：'https://www.huya.com/52333'

YouTube：'https://www.youtube.com/watch?v=...'

一行一个链接，程序会自动循环检测这些直播间，主播一旦开播就自动录制。

**小贴士：**

如果想暂时跳过某个直播间，在链接前加 '#' 注释即可

如果想为特定直播间设置录制画质，可在链接前加上画质标签，如 '超清,https://live.douyin.com/...'

建议设置较长的循环检测间隔（如 5-10 分钟），避免请求过于频繁被平台封禁 IP

### 使用建议

**关于录制格式**

建议将录制格式设置为 'ts' 格式。相比 'mp4' 格式，'ts' 格式在异常中断时更不容易导致文件损坏，特别是在长时间录制的场景下。

**关于海外平台**

如果需要录制 TikTok、YouTube 等海外平台，需要在配置中启用代理并填入代理地址（如 '127.0.0.1:7890'）。

**关于录制停止**

如需停止某个直播间的录制，只需在配置文件中的对应链接前加上 '#' 符号，程序会自动停止监测并正常保存已录制的视频。

### 总结

DouyinLiveRecorder 让直播录制变得简单高效。无论你是想备份喜爱主播的精彩内容，还是需要进行直播存档管理，这个工具都能胜任。通过 Docker 部署到 NAS，一次配置，永久自动化运行，真正做到「一劳永逸」。

现在就开始部署吧，让你再也不错过任何精彩直播！

---

原文：<https://www.passnat.com/posts/douyin-live-recorder-50-platforms-automatic-recording-nas>


## 相关文章

- [录播姬BililiveRecorder开源B站直播录制工具Docker一键部署指南，NAS远程访问。](bilibili-recorder-docker-nas-remote-access-guide.md)
- [code-server 自建网页版 VS Code：飞牛 NAS Docker 部署远程开发环境，内网穿透出门也能写代码](codeserver.md)
- [DopplerTask开源自动化平台Docker快速部署指南，NAS轻松搭建工作流自动化系统](doppler-task-docker-nas-workflow-automation-guide.md)
- [开源自动化工作流平台 Activepieces 内网穿透部署完整指南及应用教程](activepieces---.md)
- [在线编译器探索平台Compiler Explorer，支持30+编程语言实时代码编译查看汇编代码](compiler-explorer-online-ide-30-programming-languages.md)
