---
title: 赛博大善人白嫖录（二）
summary: 五分钟带你速通白嫖赛博大善人 Cloudflare 的 R2 对象存储服务，零成本搭建属于自己的高速图床。
tags:
  - 笔记
  - 白嫖录
  - Cloudflare
date: 2026-07-07T12:22:12+08:00
lastmod: 2026-07-12T15:27:21+08:00
draft: false
---

五分钟带你速通白嫖赛博大善人 Cloudflare 的 R2 对象存储服务，零成本搭建属于自己的高速图床。

## 前置知识

> Cloudflare 的 R2 对象存储服务是什么？

简单来说就是：Cloudflare 提供的一种云端文件存储服务，是一个可以用来存放图片、视频、文件等资源的“云硬盘”。类似的还有亚马逊 S3、阿里云 OSS、腾讯 COS 等。

它的具体收费情况是这样的：

![](https://paperblog-cdn.opensery.com/2026/07/20260712135046293.png)

但我这里用来存放博客中的图片，所以不用担心会超出。

## 准备工作

如果你也想开始白嫖，需要准备以下东西：

- 一个 Cloudflare 账号
- 一个支付方式：
  - PayPal
  - Apple Pay
  - Google Pay
  - 支持 VISA / Mastercard 等机构的银行卡
- 一个域名（推荐）

要开通 R2 服务必须绑定支付方式，但是不超出白嫖额度的话是不会扣款的。

## 白嫖过程

### 注册域名

> 如果你没有域名的话，可以参考下面的内容自行注册。

购买域名可以来这几个地方：

- [阿里云](https://wanwang.aliyun.com/domain)
- [腾讯云](https://cloud.tencent.com/product/domain)
- [GoDaddy](https://www.godaddy.com/)
- [Cloudflare](https://www.cloudflare.com/products/registrar/)

我这里图方便，所以也是在 Cloudflare 购买的域名，优点是续费同价，但缺点是首次购买也是同价。其他的例如阿里云和腾讯云首次购买域名的话会有优惠，基本上几块钱就可以拿下了。

### 开通服务

访问 [Cloudflare](https://dash.cloudflare.com/) 官网，访问右侧导航栏的“存储与数据库“，找到“R2 对象存储”并点击“概述”即可：

![](https://paperblog-cdn.opensery.com/2026/07/20260712140744287.png)

此时点击“将R2 订阅添加到我的帐户”，然后绑定你自己的支付方式就行：

![](https://paperblog-cdn.opensery.com/2026/07/20260712140902902.png)

这里的"账单地址"信息正常填写就行，应该没有什么限制的。

填写完成后点击“激活 R2“就可以了，此时会跳转到服务页面：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141153019.png)

接下来点击右上角的“创建存储库”：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141238003.png)

填写存储桶名称，位置的话会自动选择的，完了之后点击左下角“创建存储桶”：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141338984.png)

### 公开访问

如果你上传文件的话，会发现这个文件还是不能通过互联网正常访问，因为此时的“公开访问”是**禁用**状态：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141521015.png)

现在，点击“设置”按钮，在“自定义域”中点击添加：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141722417.png)

填写你的子域名，例如我这里写的是 `paperblogs-cdn.opensery.com` 这串：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141908944.png)

最后点击“继续”，点击“连接域”即可：

![](https://paperblog-cdn.opensery.com/2026/07/20260712141957093.png)

这样上传的图片就可以公开访问了。例如这里上传了一张名为 exmaple.png 图片，那么你就可以通过 https://paperblogs-cdn.opensery.com/example.png 进行互联网访问了。

但是现在你应该还有一个问题：难道每次都要手动在 R2 这里上传文件后，自己再拼接出完整的链接才可以使用吗？那么这里就要推荐一个工具了，有请 [PicGo](https://github.com/Molunerfinn/picgo) 出场。

## 配置 PicGo

安装的过程就不再赘述了，到 https://github.com/Molunerfinn/PicGo/releases 下载安装即可。

PicGo 是通过 Token 来调用 API 接口以管理图片的，所以还需要先申请 Cloudflare 的凭证。来到 Cloudflare 的 R2 对象存储服务页面，点击右下角的“管理”按钮：

![](https://paperblog-cdn.opensery.com/2026/07/20260712143558617.png)

然后点击右上角“创建 Account API 令牌“：

![](https://paperblog-cdn.opensery.com/2026/07/20260712143629033.png)

按照我下面图片的内容填写就行，名称你可以改成自己想要的：

![](https://paperblog-cdn.opensery.com/2026/07/20260712143712353.png)

![](https://paperblog-cdn.opensery.com/2026/07/20260712143724505.png)

完成后点击右下角“创建 Account API 令牌”就可以了：

![](https://paperblog-cdn.opensery.com/2026/07/20260712143802449.png)

这里需要注意两个值：

- **访问密钥 ID**
- **机密访问密钥**

这两个就是 PicGo 用于调用 Cloudflare API 接口的凭证了，需要你记到小本本里，同时也需要你注意保密。

接下来我们就可以来配置 PicGo 了，它的界面如下：

![](https://paperblog-cdn.opensery.com/2026/07/20260712142800163.png)

我们需要先安装 S3 插件，点击左侧导航栏的第五个按钮：

![](https://paperblog-cdn.opensery.com/2026/07/20260712142837666.png)

输入 `s3` 进行搜索：

![](https://paperblog-cdn.opensery.com/2026/07/20260712142913764.png)

一般第一个就是，作者是 wayjam 大大。点击安装按钮即可。

然后来到左侧导航栏第二个按钮：

![](https://paperblog-cdn.opensery.com/2026/07/20260712143024996.png)

点击“新建配置”，并输入配置名称：

![](https://paperblog-cdn.opensery.com/2026/07/20260712143143881.png)

然后把之前的两个密钥对应填入就可以了：

![](https://paperblog-cdn.opensery.com/2026/07/20260712144101396.png)

桶名是你刚刚创建存储桶时的名称，然后修改“上传文件路径”为

```
{year}/{month}/{fileName}.{extName}
```

就像这样：

![](https://paperblog-cdn.opensery.com/2026/07/20260712153933877.png)

滑下来，“地区”填写 `auto` 就可以，“自定义节点”需要填写这个地方的值：

![](https://paperblog-cdn.opensery.com/2026/07/20260712144616689.png)

填完之后是这个样子：

![](https://paperblog-cdn.opensery.com/2026/07/20260712144927273.png)

最后滑倒最底部，修改“自定义输出 URL 模板”这里：

![](https://paperblog-cdn.opensery.com/2026/07/20260712145235886.png)

这里需要注意 `paperblogs-cdn.opensery.com` 这串需要换成你自己的子域名。

完整的配置如下图所示：

![](https://paperblog-cdn.opensery.com/2026/07/20260712145339612.png)

最后点击右上角的“确定”就完成了。接下来你就可以上传一张图片来测试是否配置成功。

另外有几个小提示：

1. 在 PicGo 中上传图片后，默认会以 Markdown 可用的链接格式进入到你的剪贴板中。意思是你上传文件后，进行粘贴操作（Ctrl + V）的话，会出现类似 `![](https://paperblogs-cdn.opensery.com/2026/07/20260712000000000.png)` 这样的文本，在 Markdown 中可即时预览到图片。
2. 可以在设置中，修改通过 PicGo 上传的快捷键。我这里改的是 Ctrl + Shift + V，写 Markdown 的时候就可以直接 Ctrl + Shift + V 将图片上传，然后使用 Ctrl + V 粘贴链接。
3. PicGo 只能上传图片，其他文字、文档、文件等都无法上传。
