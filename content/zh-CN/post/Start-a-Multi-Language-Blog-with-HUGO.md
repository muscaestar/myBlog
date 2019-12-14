---
title: "用Hugo构建一个多语言博客"
subtitle: "以及在Github Pages上的部署"
date: 2019-12-12T11:56:10+11:00
tags: ["blog", "Hugo"]
draft: false
---
Hugo 是一个非常好用的静态网站生成器，也适合用来创建简单的博客。它有非常多的功能，
并且支持各种Markdown 语言。本篇文章主要用来记录本博客的创建，主要内容有：
1. 安装和部署
2. 设置多语言
<!--more-->

# 为什么用Hugo？
主要就是快嘛，这也是它在官网和社区中介绍的最大的特点。至于具体究竟多快，其实对我来说并不重要，满足基本的博客要求就行了。
对我来说，在看过各个博客框架后，最终让我选择Hugo的原因是，它对org的支持。
再详细一点，就是Hugo可以和Emacs的org-mode很好得整合起来。
不过这篇文章不是关于Emacs的，因为即使不用Emacs，Hugo本身就已经很好用了。
我也是用最通常的使用方法体验了一下Hugo，太简单方便了，这篇文章就来记录一下这个过程。仅仅需要一点点命令行知识，就可以马上上手Hugo。

# 开始之前
请确保已经了解一下三件事项：
1. 最最最基础的shell知识
2. 懂得如何查阅官方文档，其实就是愿意耐心读
3. 用过Github

操作系统： `macOS`

# 安装和部署

## 安装 
运行下列命令。相关文档：[Quick Start](https://gohugo.io/getting-started/quick-start/)
```shell
$ brew install hugo    # Homebrew
$ hugo version         # Verify, in my case v0.60.1
$ hugo new site <projectName> # Create project directory
$ cd <projectName>     # Go to the new directory 
$ git init             # Version control
```
然后把它作为Github repository。这里不过多赘述。

## 添加主题
我使用的主题是：[Beautiful Hugo](https://themes.gohugo.io/beautifulhugo/)

注意：本篇文章中有个别配置都是适用于Beautiful Hugo，可能与其他主题有所区别。

安装主题：
```shell
$ git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo
# use theme's example config file as a start point
$ cat themes/beautifulhugo/exampleSite/config.toml >> config.toml 
```
在开始配置之前，推荐观察一下`<project>/themes/beautifulhugo/exampleSite`目录下的文件，帮助理解整体框架。

随便改改配置文件`config.toml`看看有什么效果。相关文档：[Configuration](https://gohugo.io/getting-started/configuration/). 

****注意事项：`baseURL`一定要设置对。本博客的设置是类似于这样的url结构`"https://<username>.github.io/<projectName>/"`**

## 简单测试
```shell
$ hugo new post/testing-post.md # Create a new post
```
创建的新文档在此目录下`<project>/content/post/`.

****Beautiful Hugo的设置：为了部分功能可以使用，必须用特定的路径名，如 `page`和`post`**

现在可以开server看一看页面的效果：
```bash 
$ hugo server -D # Start the Hugo server locally
```
在浏览器里输入<http://localhost:1313/>查看效果。( 本博客的情况下，链接为 http://localhost:1313/projectName/ ）
第一次的话，应该会看起来比较奇怪，多修改几次`config.toml`文件，直到满意为止。（友情提示：差不多就得了。）

如果这一步完成了，就可以开始部署了。

## 部署博客
可用的方法有很多，我这里选择的是部署到project page 上，会用到`gh-pages`分支。官方文档写的很详细啦，照着做就可以：
- [General Description](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
- [Specific to my
case](https://gohugo.io/hosting-and-deployment/hosting-on-github/#deployment-of-project-pages-from-your-gh-pages-branch) (use `origin` to replace `upstream`)

一定要一步不漏做下来，不出意外的话，gh-pages 也就设置好了。并且根据指示，你也创建了一个叫`publish_to_ghpages.sh`的脚本，简单说是博文一键发布脚本。

****注意事项：在官方文档指示中，要用`origin`字段来替代它使用的`upstream`字段。** 
主要是因为我的github repo 的主分支叫`origin`.

## 简易发布流程
成功部署之后，在此项目的github网页上，你能看到两个分支。
`gh-pages`分支的内容是最后发布到网页的内容。这样做，你的源文件和发布内容就能分开维护和版本控制，我个人比较喜欢这样的做法。

发布流程：
1. 运行 `hugo new post/Article-Name.md`
2. 编写文章
3. 运行 `hugo server -D` 查看页面效果
4. 发布之前，需要在文章头部的front matter做这个修改`draft = false`
5. commit 主分支的新改动
6. 运行 `./publish_to_ghpages.sh` 

完成。

# 多语言设置
Beautiful Hugo 本身提供了一些支持多语言的功能，我的设置如下：
- 英文作为默认语言
- 中文作为第二语言
- 为不同语言设置各自的主菜单
- 切换网站语言的选项，放置在主菜单中，通常在页面右上角。（Beautiful Hugo提供）
- 每篇文章的标题下，有一个切换语言的选项。（Beautiful Hugo提供）

需要修改一下文档：
- `config.toml`
- `nav.html`
- `/content/`路径下的文件结构

## 修改`config.toml`
相关文档：[Multilingual](https://gohugo.io/content-management/multilingual/)

以下为`config.toml`的节选：
``` toml {linenos=table, linenostart=15}
DefaultContentLanguage = "en"
DefaultContentLanguageInSubdir = true
[languages]
  [languages.en]
    contentDir = "content/en" #English
    weight = 1
    languageName = "English" # Name shown in the main menu
    subtitle = "This is subtitle for this website"
    [languages.en.params]
      linkedin = "https://muscaestar.github.io/myBlog/en/"
    [[languages.en.menu.main]]
        name = "Blog"
        url = ""
        weight = 1
    [[languages.en.menu.main]]
        name = "About"
        url = "page/about/"
        weight = 3
    [[languages.en.menu.main]]
        name = "Tags"
        url = "tags"
        weight = 3
  [languages.zh-CN]
    contentDir = "content/zh-CN" #Simplified Chinese
    weight = 2
    languageName = "简体中文" # Name shown in the main menu
    subtitle = "副标题"
    [languages.zh-CN.params]
      linkedin = "https://muscaestar.github.io/myBlog/zh-CN/"
    [[languages.zh-CN.menu.main]]
        name = "博客"
        url = ""
        weight = 1
    [[languages.zh-CN.menu.main]]
        name = "关于"
        url = "page/about/"
        weight = 3
    [[languages.zh-CN.menu.main]]
        name = "标签"
        url = "tags"
        weight = 3
```

## 修改 `nav.html`
`nav.html` 是一个 partial template. 相关文档：[Partial
Templates](https://gohugo.io/templates/partials/). Partial template 可以被放在`/layouts/partials/` 或者 `/themes/<themename>/layouts/partials/`. 

`nav.html` 由Beautiful Hugo提供, 用来构建主菜单，默认即可使用。但是本博客的baseURL被设置为 `<username>.github.io/<projectName>`, 所以有个地方是需要改动的。

推荐在改动前做以下操作：
```shell
# make a copy of theme's nav.html and place it in your own place
$ cp /themes/beautifulhugo/layouts/partials/nav.html /layouts/partials/nav.html
```
需要修改的`nav.html`是放在`/layouts/partials/`路径下的. Hugo 会将你的个人设置优先于主题的设置。([Hugo's Lookup
Order](https://gohugo.io/templates/lookup-order/) )

以下是`nav.html`的节选. 因为我的baseURL值为`muscaestar.github.io/myBlog/`, 所以我把`/myBlog/`加到特定的地方，详情见行39,48.
```html {linenos=table, hl_lines=[8, 17], linenostart=32}
{{ if .Site.IsMultiLingual }}
    {{ if ge (len .Site.Languages) 3 }}
    <li class="navlinks-container">
        <a class="navlinks-parent">{{ i18n "languageSwitcherLabel" }}</a>
        <div class="navlinks-children">
        {{ range .Site.Languages }}
            {{ if not (eq .Lang $.Site.Language.Lang) }}
            <a href="/myBlog/{{ .Lang }}" lang="{{ .Lang }}">{{ default .Lang .LanguageName }}</a>
            {{ end }}
        {{ end }}
        </div>
    </li>
    {{ else }}
    <li>
        {{ range .Site.Languages }}
        {{ if not (eq .Lang $.Site.Language.Lang) }}
            <a href="/myBlog/{{ .Lang }}" lang="{{ .Lang }}">{{ default .Lang .LanguageName }}</a>
        {{ end }}
        {{ end }}
    </li>
    {{ end }}
{{ end }}
```

## 修改路径`/content/`下的文件结构
路径名必须与配置文件`config.toml`中的`contentDir`值相匹配。本博客的结构如下：
```
content/      content/      
└── en/       └── zh-CN/       
    ├── page/     ├── page/ 
    └── post/     └── post/ 
```

# 写在最后
至此，一个多语言博客就设置部署完成了。文中提到的发布流程也足够日常发布博文使用，
如果没什么特别要求，一个简单的博客就建成了。当然Hugo还有很多高级功能可以使用，
这里就不多介绍，看官方文档就可以。接下来计划介绍如何整合Hugo和org-mode，让org-mode写博客的体验更加丝滑。




