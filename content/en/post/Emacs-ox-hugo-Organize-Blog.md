---
title: "Use Emacs to Organize Hugo Blog"
author: ["Muscaestar"]
date: 2019-12-29T17:51:00+11:00
lastmod: 2019-12-29T20:52:04+11:00
tags: ["blog", "Hugo", "Emacs", "org-mode"]
draft: false
---

Emacs is a magical 'software' and you can do 'everything' in it, of course writing
and organizing your blog posts. Last post, I built this blog with vanilla Hugo. This article will introduce org-mode and ox-hugo to
make Hugo better for Emacs users.

<!--more-->


## Why Emacs? Org-mode? {#why-emacs-org-mode}

Almost every programmer have at least heard the famous 'GNU/Emacs', or the joke about the
Editor War between Vim users and Emacs users. Both of them seem to be very proud of
mastering at least one of these 'highly customizable' 'tough-learning' 'COOL' text
editor. Don't get me wrong. Vim and Emacs are great, and I enjoy using both of
them. But to be honest, with the rise of modern IDEs and their 'not bad'
plugins, those actual Vim users and Emacs user are gradually becoming the minority group.
Admit it, out-of-the-box is the new black.

So, why am I bother writing this article introducing a tiny customization
package in Emacs?
Because even though I'm kinda happy with this OOTB world, I still realize that
nobody really feels satisfied with it. Yes, modern software is so convenient.
But the interesting thing is, for example, WHY there's no alternatives for
org-mode?

Finally, here comes the hero, the Org-mode. I think org-mode is the reason
that I insist on sticking with Emacs. My schedules, projects, todos, study notes with plenty of
runnable codes, sudden crazy ideas... so much programming-related or not related
stuffs are well organized with it. It becomes my second brain. It **organizes** my life.
So cliché, but so true.

So I write this article. It's an introduction of some tiny features of the
tremendous Emacs. It's my 'love letter' to the lovely org-mode. I wish I could meet you
earlier. You literally change my life.

OK, that's enough. Let's jump back to the topic.


## Why ox-hugo? {#why-ox-hugo}

First of all, checkout the official recommendation of [Hugo's editor plugins](https://gohugo.io/tools/editors/). Yes,
there're many other plugins, in Vim, VS Code, Atom, Sublime... If you don't feel
anything with
org-mode, its time to leave. I believe other plugins should work fine
and 'more OOTB'.

For Emacs user, there're two packages, [easy-hugo](https://github.com/masasam/emacs-easy-hugo) or [ox-hugo](https://ox-hugo.scripter.co/). Here's what I
thought. Easy-hugo is made for Emacs, and ox-hugo is made specifically for
org-mode. Easy-hugo helps you dealing with blog-related file management and configurations,
using some shortcuts to replace redundant CLI works. While, ox-hugo goes a different
direction, which is basically an org-mode exporter. It is the details of
ox-hugo's features design that impressed me. In my opinion, ox-hugo's design match some of the true
philosophies of org-mode.

"Using Org just as a markup like Markdown is a miniscule part of its complete
feature-set." -- [Why ox-hugo?](https://ox-hugo.scripter.co/doc/why-ox-hugo/)


## It is MORE about your thinking than doing {#it-is-more-about-your-thinking-than-doing}


### Design your own structure {#design-your-own-structure}

When it comes to customization, there's no simple HOW TO.
Ox-hugo is well documented in its [website](https://ox-hugo.scripter.co/). It offers you lots of functions, and
sometimes people may become confused about their combination.
If you are familiar with self-configuration, you don't need my saying.
If you are not, maybe you can ask yourself these questions:

-   Am I satisfied with my current workflow? How about in the nearly future?
-   Do I need to use all the functions it offers?
-   Which functions are my actual needs?
-   Can I improve my workflow without installing this package?
-   (configuration time) < (time saved for the future) Really?

It is worthy to dig your mind, and finally find your true desire. Otherwise,
you just jumped into an infinite editor customization loop.


### Here's my thinking {#here-s-my-thinking}

My reason for using it:

-   I'm used to taking notes in org-mode, I wish I could just copy my notes as
    some parts of my posts without any changes in the format.
-   My blog will be maintained in two languages, which means for each post,
    there're 2 files with same file name while placing in different directories.
    To keep them linked, everytime I change one, I should make sure the other is
    also changed. The two files are actually one post, so I wish I could manage
    them together.
-   I write blog in Chinese, but with Emacs' auto word-wrap mode, each line
    creates an unnecessary space when finally rendered to html file. It needs to
    be fixed.
-   If I could organize all my post in just one org file. So that I could easily
    do tags, modified time, and so on.
-   If the only one org file becomes too big, I could create another, and it won't
    affect any previous structures.
-   How about move to another dynamic blog framework, so many automation problems will
    be solved. But can I use evil-mode? org-mode? It may produce more
    configuration time.
-   I already got lots of notes written in org mode, and I'm planning to make them my
    future posts. So with ox-hugo, it will definitely save my time.


## Demo of mine {#demo-of-mine}


### The only one org file {#the-only-one-org-file}

On the top of the org file, I put these declarations at the head as global settings.

```org
 #+HUGO_FRONT_MATTER_FORMAT: yaml
 #+HUGO_BASE_DIR: ~/myBlog/
 #+HUGO_AUTO_SET_LASTMOD: t
```


### Use YASnippet to create blank template {#use-yasnippet-to-create-blank-template}

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


## In the End {#in-the-end}

This article is not about HOW TO, because I believe it is meaningless to write
'do-what-I-say' article in terms of customization with Emacs. My configuration
only works in my case, what matters is the process of reaching it.
