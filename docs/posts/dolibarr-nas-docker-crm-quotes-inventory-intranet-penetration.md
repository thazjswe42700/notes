---
title: "Dolibarr 小店管理：NAS Docker 部署 + 内网穿透"
date: 2026-10-19
description: "在 NAS 上用 Docker Compose 部署开源小店管理后台 Dolibarr：客户档案、报价单、商品和库存放在同一个后台，报价金额自动算，库存按仓库记数量；用帕斯内网穿透拿公网地址，手机打开就能查客户和库存。含完整编排和步骤。"
canonical: https://www.passnat.com/posts/dolibarr-nas-docker-crm-quotes-inventory-intranet-penetration
---

## 项目介绍

Dolibarr 是一套开源的小店管理后台，把客户档案、报价单和商品库存放在同一个地方。客户建一张卡：名称、地址、联系人、跟进记录都在上面；报价单选客户、挑商品、填数量，金额自动算；商品按编号建好，库存按仓库记数量，卖出去多少、还剩多少一目了然。

它不只是一个客户表。装上核心模块之后，报价单可以接着转成订单和发票，商品和库存也跟报价行连在一起；这次只演示最常用的三块：客户、报价、库存，用一套虚构的小店数据走一遍。

界面有简体中文，账号分权限，后台在浏览器里用，电脑和手机都能打开。

项目地址：[点我跳转](https://github.com/Dolibarr/dolibarr)

## 部署安装

只要能跑 Docker 的 NAS 都能装，下面用飞牛 NAS 演示：先用 [帕斯内网穿透](https://passnat.com) 拿到一个公网地址，再把 Dolibarr 用 Docker Compose 装起来。编排里要填这个地址，所以先穿透、后部署，装完直接用公网地址登录，以后在店里或者外面，手机打开就能查客户和库存。

### 一、先用帕斯内网穿透拿到公网地址

飞牛的「应用中心」里搜 PassNAT，装上这个穿透插件，打开它，点「创建隧道」。隧道名字随便起（不超过 12 个字符），接入节点和传输协议留默认，本地 IP 留默认的 127.0.0.1，本地端口填 3606（就是下面编排里冒号左边那个端口）。

建好之后点开这条隧道，按一下电源，状态变成运行中，详情里就能看到分配好的公网地址（形如 IP:端口），先记下来。

不是飞牛的系统，PassNAT 文档的「连接使用」里有其他系统的连接教程：[点我跳转](https://doc.passnat.com/docs/category/%E8%BF%9E%E6%8E%A5%E4%BD%BF%E7%94%A8)。

### 二、新建 Compose 项目

打开飞牛的「Docker」应用，切到「Compose」标签，点右上角的新增项目。项目名称填 dolibarr，路径在「我的文件」下面挑一个空文件夹，没有就当场新建一个。来源选「创建 docker-compose.yml」，把下面这段整个贴进去：

```yaml
services:
  db:
    image: mariadb:10.11
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD: "换成一长串随机密码"
      MARIADB_DATABASE: "dolibarr"
      MARIADB_USER: "dolibarr"
      # 下面三处 DOLI_DB_PASSWORD / MARIADB_PASSWORD 填同一个密码
      MARIADB_PASSWORD: "换成一长串随机密码"
    volumes:
      - db_data:/var/lib/mysql

  dolibarr:
    image: dolibarr/dolibarr:latest
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "3606:80"
    environment:
      DOLI_DB_TYPE: "mysqli"
      DOLI_DB_HOST: "db"
      DOLI_DB_HOST_PORT: "3306"
      DOLI_DB_NAME: "dolibarr"
      DOLI_DB_USER: "dolibarr"
      DOLI_DB_PASSWORD: "换成一长串随机密码"
      DOLI_INSTALL_AUTO: "1"
      DOLI_INIT_DEMO: "0"
      DOLI_PROD: "1"
      DOLI_ADMIN_LOGIN: "admin"
      DOLI_ADMIN_PASSWORD: "换成管理员密码"
      DOLI_URL_ROOT: "http://你的NAS地址:3606"
      PHP_INI_DATE_TIMEZONE: "Asia/Shanghai"
    volumes:
      - documents:/var/www/documents

volumes:
  db_data:
  documents:
```

整段编排里要改的是两个密码和一个地址：

**数据库密码**：MARIADB\_PASSWORD 和 DOLI\_DB\_PASSWORD 一共三处，填同一串随机字符，字母数字混着来。

**管理员密码**：DOLI\_ADMIN\_PASSWORD，第一次登录用的就是它，装好之后可以在用户设置里改。

**DOLI\_URL\_ROOT**：换成上面拿到的公网地址，结尾带上 3606，比如 http://你的地址:3606。

端口 3606 被占用的话，把冒号左边的数字换一个，隧道里的本地端口和 DOLI\_URL\_ROOT 也跟着改。勾上「创建项目后立即启动」，镜像下完就起来。第一次启动会自动建库、把上百张表导进去，在 NAS 上要等几分钟，看到页面能打开就装好了；编排里 DOLI\_PROD=1，装完会写 install.lock，安装入口自动关掉。

数据库和附件都在两个具名卷里（dolibarr\_db\_data、dolibarr\_documents），备份这两个卷就够了。

### 三、第一次打开

用公网地址打开，用户名 admin，密码就是编排里 DOLI\_ADMIN\_PASSWORD 填的那串。界面语言跟着浏览器走，想固定成中文的话，点右上角用户名进「信息卡」，把语言选成简体中文再保存。

左边菜单已经有「合作方」「产品」「商务」三块，下面按这个顺序把数据记起来。

### 四、客户、商品、报价

**先建客户。** 「合作方 → 新建合作方」，填名称，勾上「客户」，地址、城市按实际填，保存。客户卡上还能加联系人和跟进备注，报价单和以后的订单都挂在这个客户下面。

**再建商品。** 「产品 → 新建产品」，填产品编号、标签和售价，产品性质选成品；保存后进「库存」标签，第一次先按页面提示建一个仓库（比如「小店仓库」），再点「合适的库存」，选仓库、填数量，保存。进多少、卖多少，都在这个页面里加减，商品列表会直接显示实物库存。

**最后做报价。** 「商务 → 报价单 → 新建报价单」，选客户，报价日期和有效期自动带出来；在下面的「添加新行」里选预定义产品、填数量，单价和金额自动算，两行加完点「验证」，状态从草稿变成已验证。报价单可以打印成 PDF，也可以接着转成订单和发票。

### 五、手机上查

手机浏览器打开同一个公网地址，产品列表照样能看，表格往右滑就是实物库存；报价单列表里也能看到客户、日期和金额。在店里接待客户、或者在外面被问「还有没有货」，掏出手机就能答，不用回电脑前翻表。

### 几点说明

**数据在哪。** 业务数据在 dolibarr\_db\_data 卷里，附件和生成的 PDF 在 dolibarr\_documents 卷里，两个卷一起备份就行。想搬到别的机器，把这两个卷导出来带走。

**更新镜像。** 编排用的是 latest 标签，重新拉取镜像再重启项目就会走到新版本；动手前先把两个卷备份一份。

**明文 HTTP 访问。** 内网穿透给的是 http 地址，Dolibarr 本身不需要 HTTPS 才能用；但它记的是客户和经营数据，公网地址别到处发，不用的时候可以在插件里把隧道关掉。

**这次没碰的东西。** 模块市场、第三方支付、邮件发送都没配；核心的客户、报价、库存三块不依赖它们，先把这三块用顺，其他功能等熟了再按需开。

---

原文：<https://www.passnat.com/posts/dolibarr-nas-docker-crm-quotes-inventory-intranet-penetration>
