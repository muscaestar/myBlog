---
title: "用 Emacs 来管理 Hugo 博客"
author: ["Muscaestar"]
date: 2019-12-29T20:51:00+11:00
lastmod: 2019-12-29T20:51:58+11:00
tags: ["blog", "Hugo", "Emacs", "org-mode"]
draft: false
---

Emacs 是个“神奇”的软件，你可以用它做“任何事”，当然这也就包括了写博客和管理博文。上一篇博文中，我讲了如何使用最基础的 Hugo 框架来写博客。今天这篇就来介绍一下，我是如何使用 org-mode 和 ox-hugo，来让 Hugo 博客体验更丝滑。

<!--more-->


## 为什么使用 Emacs？Org-mode? {#为什么使用-emacs-org-mode}

我想大部分程序员应该都听过 GNU/Emacs 的名声，或者至少听说过 Vim 和 Emacs 的宇宙最强编辑器大战，这种老梗。很多 Vim 或者 Emacs 的使用者，或者说，初学者，都挺喜欢津津乐道这种“上古编辑器”的自定义啊，学习门槛高啊，什么不需要使用鼠标的高效啊，装逼性强啊之类的优点。不是说不好，我也挺喜欢的，不过说实话，稍微走出“上古编辑器”这个圈地自萌的领域，就会发现，其实真的愿意使用 Vim 或 Emacs 的人，真的越来越少了。主要一个原因，现代 IDE 越来越好用了，并且大都自带 Vim, Emacs 插件。键位对上了，而且开箱即用，不得不说，用一下真的很香。

既然这么香，那我何必在这里写一个配置 Emacs 的文章？还只是一个微不足道的小功能？我承认，我也很喜欢开箱即用，节省了很多时间和麻烦。但是但是，开箱即用，永远都不可能完全贴合每一个使用者的习惯。所谓编辑器，要不是你适应它，就得它适应你，最后还是时间花费上的权衡。说现代软件越来越先进，其实也不全对，比如 org-mode 的存在，就让我一直费解：10 多年了，为什么没有人能做出一个超越 org-mode 的，集项目管理，时间规划，
Markup 格式，还能内置跑代码等，各种功能于一身的软件？

Org-mode 就是我一直使用 Emacs 的原因，至今没有找到任何一个替代品。它的功能实在太强大，各部分之间的集成又实在是太顺滑。我曾经沉迷尝试各种效率软件，但没有一个能像
org-mode 一样真的 **改变** 我的生活和习惯。我想应该有许多 Emacs 使用者，也是如此深深喜爱着 org-mode。

大概，这篇文章应该就是只为 org-mode 而写，为 org-mode 爱好者而写。


## 为什么使用 ox-hugo? {#为什么使用-ox-hugo}

开始之前，何不看看这个：[Hugo的编辑器插件](https://gohugo.io/tools/editors/). Hugo 其实支持了很多的编辑器, Vim, VS
Code, Atom, Sublime 等等。所以要不是真的 org-mode 爱好者，只要选择自己合适的插件就足够了。工具只是工具，适合自己的才是最好的。

在 Emacs 上，Hugo 有两款插件，[easy-hugo](https://github.com/masasam/emacs-easy-hugo) 和 [ox-hugo](https://ox-hugo.scripter.co/). 这两款插件的定位是不一样的，
easy-hugo 是一款在 Emacs major-mode 上使用的博客内容管理工具，主要功能是通过一些快捷操作来实现原来需要在命令行上的重复操作。ox-hugo 从最最基本的功能上来说，就是一款 org 格式转 Markdown 格式的工具，听起来很普通的样子，但是它对功能的设计十分惊艳，完美地和 org-mode 的优点契合在一起。这也是我在使用之后，才发觉的。

Org-mode 决不仅仅是一个 markup 语言，这只是它表面的皮毛罢了。你可以看看 ox-hugo 的开发者的观点：[Why ox-hugo?](https://ox-hugo.scripter.co/doc/why-ox-hugo/)


## 所思比所做更值得注意 {#所思比所做更值得注意}


### 想想究竟什么才适合你 {#想想究竟什么才适合你}

对于自定义，从来就没有什么简单明了的“该怎么做”。
[官方文档](https://ox-hugo.scripter.co/)里对所有功能都有详细的介绍。对于达成一个特定的功能，ox-hugo 里是有很多选择的，这也就会造成一些特殊的困扰：究竟应该如何组合各个不同的功能。相信每个玩自定义的朋友在刚开始都会有类似麻烦。在做出选择之前，我通常会考虑一下几个问题：

-   我对目前的操作流程满意吗？满意程度在将来会改变吗？
-   我需要使用它的全部功能吗？
-   哪几个功能是我真的需要的？
-   在安装它之前，我能够通过其他地方的改进，来改善目前的操作流程吗？
-   配置所需时间真的小于将来节省下来的时间吗？

多多思考一下自定义功能的价值，还是很有用处的，有些时候需要适可而止。否则总有一天，你会感叹自己把大把的时间都浪费在了没有实际意义的配置上。


### 一些我的理由 {#一些我的理由}

分享一下我决定使用 ox-hugo 的理由：

-   我已经习惯于使用 org-mode 记笔记，我希望我可以把笔记内容直接复制粘贴作为博文的内容，而不需要改变格式。
-   我的博客是中英双语，同时维护一篇博文的两个语言版本，意味着我得在两个不同的路径下管理两个文件。为了确保 Hugo 将两份文件视作同一博文的两个版本，很多时候我必须同时修改两个文件的内容，以避免冲突。假如我可以在一个文件中管理多个文本，效率会有很大的提升。
-   当我用中文在 Emacs 里写作时，通常开着 word-warp mode，也就是自动换行。这会导致一个问题，就是当 Hugo 在将文档转为 html 文件时，每个自动换行的点都会多出一个空格，十分影响美观。ox-hugo 能够自动处理这个问题。
-   假如我的所有博文都能放在同一个文件中，我就能很方便地统一管理 tag 以及其他设定了。
-   假如我的 org 文件过大，我也可以再新建新的 org 文件，并且不会影响之前的任何已发布的设置。
-   似乎迁移去更产品化的博客系统也能解决我存在的这些问题，可是我就不一定能很好地使用
    org-mode 和 evil-mode 键位。可能会出现许多新的麻烦。
-   我已经有了许多 org-mode 下的笔记，我也计划将它们作为博文内容发布。因此，配置
    ox-hugo 一定能在将来这方面节省下我的时间。


## 我的配置 {#我的配置}


### 集合所有博文的 org 文件 {#集合所有博文的-org-文件}

所有的博文都放在这份文件中，我只要在文件开头写上这些，就是全局设定。

```org
 #+HUGO_FRONT_MATTER_FORMAT: yaml
 #+HUGO_BASE_DIR: ~/myBlog/
 #+HUGO_AUTO_SET_LASTMOD: t
```


### 使用 YASnippet 制作模板 {#使用-yasnippet-制作模板}

[Documentation](https://joaotavora.github.io/yasnippet/snippet-development.html) of write your own YASnippet template.

Everytime I want to create a new post, I can just type
`<post` and then hit `TAB`.

Here's my YASnippet template setting.

```snippet
# -*- mode: snippet -*-
# name: hugo-new-post
# key: <post
# expand-env: ((yas-indent-line 'fixed) (yas-wrap-around-region 'nil))
# --

 * ${1:Post title} / ${2:博文标题}
 ** TODO $1
 :PROPERTIES:
 :EXPORT_FILE_NAME: ${3:File-name}
 :EXPORT_HUGO_SECTION: en/post
 :END:
 Abstract
 #+hugo: more
 *** Heading 1
 text

 ** TODO $2
 :PROPERTIES:
 :EXPORT_FILE_NAME: $3
 :EXPORT_HUGO_SECTION: zh-CN/post
 :EXPORT_HUGO_LOCALE: zh
 :END:
 前言
 #+hugo: more
 *** 正文标题 1
 正文
```


## 写在最后 {#写在最后}

这篇博文并没有涉及如何使用，我觉得官网文档已经描述的很清楚了，同时我也觉得，对于
Emacs 上的自定义，写下我自己的配置用处也不是很大，因为这终究只是适合我个人的配置。我想，真的重要的，是在过程中的个人思考吧。
