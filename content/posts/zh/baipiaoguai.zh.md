---
title: "赛博大善人白嫖录（一）"
summary: "一篇文章告诉你：我是如何“白嫖”赛博大善人 Cloudflare 的 Workers 和 Pages 服务，搭建属于自己的博客站点的。"
tags:
  - "笔记"
  - "白嫖录"
  - "Cloudflare"
date: "2026-07-04T12:45:57+08:00"
lastmod: "2026-07-05T20:17:42+08:00"
draft: false
---

一篇文章告诉你：我是如何“白嫖”赛博大善人 Cloudflare 的 Workers 和 Pages 服务，搭建属于自己的博客站点的。

## 前置知识

> Cloudflare 的 Workers 和 Pages 服务是什么？这俩又有什么区别？

Cloudflare 的 **Workers** 和 **Pages** 都是 Cloudflare 提供的“边缘计算 + 前端部署”服务，其中：

- **Workers** 一般用于编写“后端逻辑”的运行时。
- **Pages** 一般用于托管“前端网站”。

你也可以简单理解为：

- **Pages**：负责“网站本体”，例如：博客页面。
- **Workers**：负责“动态功能”，例如：评论 / API / 鉴权等。

这俩组合起来就可以搭建一套全栈的 Web 应用。

如果你没有搞懂那也没有关系，说明咱们一样，只用到 **Pages** 服务就够了。

## 准备工作

如果你也想开始白嫖，需要准备以下东西：

1. 一个 [Github](https://github.com/) 账号（推荐）
2. 一个 [Cloudflare](https://www.cloudflare.com/) 账号
3. 一个你需要部署的项目
4. 一位爱动脑以及动手的你

针对第二点中提到的项目，我这里使用的是 [Hugo](https://gohugo.io/) 框架，你也可以选择其他的框架。例如：

- [Astro](https://astro.build/)
- [Hexo](https://hexo.io/)
- 其他你喜欢的框架

当然，你自己手撸一个博客也不是 8 行。

另外，我这里有个小建议，或者说小 Tips：你可以使用 [Scoop](https://scoop.sh/) 来管理一些 CLI 工具。如果你需要，可以使用下面的命令进行安装：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

## 搭建过程

### 安装程序

使用 Scoop 的话就可以直接使用下面的命令安装 Git 以及 Hugo 了：

```shell
scoop install main/git
scoop install main/hugo-extended
```

如果需要自己手动安装可以移步到下面的网站分别进行安装：

1. [Git](https://git-scm.com/)
2. [Hugo](https://github.com/gohugoio/hugo/releases)

安装完成后执行下面的命令确认安装是否成功：

```shell
git version
hugo version
```

如果有类似于下图所示的字符串就代表你安装成功了：

![20260705175430](https://r2.opensery.com/20260705175430.png)

### 创建博客

使用以下命令创建新的 Hugo 项目：

```shell
hugo new site PaperBlog --format yaml
```

这里有两个参数：

1. `PaperBlog` ：指定创建的文件夹名称。你可以根据自己的喜好重命名为其他的，例如 Blog 或者 Hugo 都可以。
2. `yaml` ：指定配置文件的格式。你也可以使用 TOML 或者 YML 或者其他的，都可以。

我这里另外搭配了 [PaperMod](https://github.com/adityatelange/hugo-PaperMod/) 主题，你可以到 Hugo 的 [主题页](https://themes.gohugo.io/) 寻找自己喜欢的主题。

如果你也需要这个主题，那么需要移动到你创建的 Hugo 项目的文件夹下，执行以下命令安装这个主题：

```shell
git init
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
```

然后编辑 `hugo.yaml` 文件，将文件的内容全部删除，替换为：

```yaml
baseURL: "https://blog.opensery.com/"
title: "OpenSevenery"
pagination:
  pagerSize: 5
theme: PaperMod

defaultContentLanguage: zh
defaultContentLanguageInSubdir: true

languages:
  zh:
    weight: 1
    params:
      description: "保持独立思考 - 也许你可以在这里找到一些有用的经验"

      DateFormat: "2006年01月02日"

      profileMode:
        title: "保持独立思考"
        subtitle: "也许你可以在这里找到一些有用的经验"

      homeInfoParams:
        Title: "保持独立思考"
        Content: "也许你可以在这里找到一些有用的经验"

    menu:
      main:
        - identifier: posts
          name: 文章
          url: /posts/
          weight: 10
        - identifier: tags
          name: 标签
          url: /tags/
          weight: 20
        - identifier: search
          name: 搜索
          url: /search/
          weight: 30
        - identifier: about
          name: 关于
          url: /about/
          weight: 40

  en:
    weight: 2
    params:
      description: "Maintain Independent Thinking - Perhaps you can find some useful experience here"

      DateFormat: "January 2, 2006"

      profileMode:
        title: "Maintain Independent Thinking"
        subtitle: "Perhaps you can find some useful experience here"

      homeInfoParams:
        Title: "Maintain Independent Thinking"
        Content: "Perhaps you can find some useful experience here"

    menu:
      main:
        - identifier: posts
          name: Posts
          url: /posts/
          weight: 10
        - identifier: tags
          name: Tags
          url: /tags/
          weight: 20
        - identifier: search
          name: Search
          url: /search/
          weight: 30
        - identifier: about
          name: About
          url: /about/
          weight: 40

enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false

minify:
  disableXML: true
  minifyOutput: true

taxonomies:
  tag: tags

params:
  env: production
  title: "OpenSevenery"
  keywords:
    [
      Blog,
      Portfolio,
      PaperMod,
      OpenSery,
      OpenSevenery,
      Sevenery,
      七尾狐草,
      Maintain Independent Thinking,
      Perhaps you can find some useful experience here,
    ]
  author: "OpenSevenery"
  images: ["./default.png"]
  defaultTheme: auto
  disableThemeToggle: false

  ShowReadingTime: true
  ShowShareButtons: true
  ShowPostNavLinks: true
  ShowBreadCrumbs: true
  ShowCodeCopyButtons: false
  ShowWordCount: true
  ShowRssButtonInSectionTermList: true
  UseHugoToc: true
  disableSpecial1stPost: false
  disableScrollToTop: false
  comments: false
  hidemeta: false
  hideSummary: false
  showtoc: false
  tocopen: false

  assets:
    # disableHLJS: true
    # disableFingerprinting: true
    favicon: "favicon.ico"
    favicon16x16: "favicon.ico"
    favicon32x32: "favicon.ico"
    apple_touch_icon: "favicon.ico"
    safari_pinned_tab: "favicon.ico"

  label:
    text: "OpenSevenery"
    # icon: /favicon.ico
    # iconHeight: 35

  profileMode:
    enabled: true
    imageUrl: "/default.png"
    imageWidth: 120
    imageHeight: 120
    imageTitle: "OpenSevenery"
    # buttons:
    #   - name: Posts
    #     url: posts
    #   - name: Tags
    #     url: tags

  socialIcons:
    - name: github
      url: "https://github.com/OpenSevenery/"

  analytics:
    google:
      SiteVerificationTag: "XYZabc"
    bing:
      SiteVerificationTag: "XYZabc"
    yandex:
      SiteVerificationTag: "XYZabc"

  cover:
    hidden: true
    hiddenInList: true
    hiddenInSingle: true

  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    location: 0
    distance: 1000
    threshold: 0.4
    minMatchCharLength: 0
    limit: 10
    keys: ["title", "permalink", "summary", "content"]

pygmentsUseClasses: true
markup:
  highlight:
    noClasses: false
    # anchorLineNos: true
    # codeFences: true
    # guessSyntax: true
    # lineNos: true
    # style: monokai

outputs:
  home:
    - HTML
    - RSS
    - JSON
```

这里需要**注意**：属于我的信息以及链接都需要替换你自己的，例如：

- OpenSery
- OpenSevenery
- https://blog.opensery.com/
- https://github.com/OpenSevenery/

另外如果你没有设置这个主题的话，只需要替换 `baseURL` 这行数据，这里有两个分支：

- 你有自己的域名：将 `baseURL` 中的 `blog.opensery.com` 替换成自己的域名。
- 你没有自己的域名：先不管，后面白嫖 Cloudflare 的时候会给你一个域名。

### 创建仓库

来到 [Github](https://github.com/) 的官方网站，登录你自己的账号后，点击左侧顶部的“New”：

![20260705183334](https://r2.opensery.com/20260705183334.png)

然后填入仓库名称：

![20260705183436](https://r2.opensery.com/20260705183436.png)

然后滑到最底下点击“Create repository”就行：

![20260705183714](https://r2.opensery.com/20260705183714.png)

创建完成后如下图所示：

![20260705185546](https://r2.opensery.com/20260705185546.png)

之后回到你的 Hugo 项目，执行以下命令：

```
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/OpenSevenery/PaperBlogs.git
git push -u origin main
```

**注意**：这里的 `https://github.com/OpenSevenery/PaperBlogs.git` 需要替换成你自己的仓库链接，可以在你的仓库页面中找到。

### 白嫖服务

访问 [Cloudflare](https://www.cloudflare.com/) 的官方网站，登录你自己的账号后，点击左侧导航栏中“计算”下面的“Workers和Pages”：

![20260705180507](https://r2.opensery.com/20260705180507.png)

点击右上角蓝色的“创建应用程序”，点击下面的“开始使用”：

![20260705180028](https://r2.opensery.com/20260705180028.png)

这里选择“导入现有 Git 存储库”:

![20260705182931](https://r2.opensery.com/20260705182931.png)

这里点击“添加账户”，按照提示进行授权，并且给予 Cloudflare 访问你刚刚创建的博客仓库的权限。

![20260705184021](https://r2.opensery.com/20260705184021.png)

回到 Cloudflare 之后，看看有没有你的博客仓库：

![20260705184138](https://r2.opensery.com/20260705184138.png)

如果找不到你的博客仓库，则点击蓝色小字”Cloudflare Pages“，然后再像前面一样给予仓库权限即可。

最后点击“开始设置”，按照下图填入数据即可：

![20260705191043](https://r2.opensery.com/20260705191043.png)

这里没有域名的同学需要**注意**，Cloudflare 会根据你的项目名称给你一个域名，例如我这里是：

```plaintext
“您的项目将被部署到 paperblogs.pages.dev。”
```

此时你就要将 `hugo.yaml` 文件中 `baseURL` 的 `blog.opensery.com` 部分，替换为给你的域名。我这里替换之后就是：

```
baseURL: https://paperblogs.pages.dev/
```

替换之后在 Hugo 项目的文件夹下执行命令：

```
git add .
git commit -m "change baseURL"
git push
```

这些命令用于将你的操作上传到 Github 中。

最后，全体都有！回到 Cloudflare 中，点击“保存并部署”，稍微等待片刻...

![20260705191427](https://r2.opensery.com/20260705191427.png)

那么至此，你就拥有了属于自己的个人博客咯。

## 作者说

本来还以为可以很快就写完这篇文章的，结果大大小小的坑在写之前踩了点，写的时候又踩了点，不过还好，总算还是比较平稳的落地了。其实还有点坑没填，但其实好像貌似大概大约或许也没什么了吧？那就先算呗，爬坑爬累了，写也写累了，先鸽了，嘻嘻。
