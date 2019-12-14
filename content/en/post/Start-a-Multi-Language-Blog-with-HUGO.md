---
title: "Start a Multi Language Blog with Hugo"
subtitle: "And host it on Github Pages"
date: 2019-12-12T11:56:10+11:00
lastmod: 2019-12-14T11:56:10+11:00
tags: ["blog", "Hugo"]
draft: false
---
Hugo is a handy framework for generating static website, which suits for normal
blog. You can easily write blogs with various markdown languages and organize
them with lots of features. This article will show you how I create this blog with it. I will focus on:
1. Installation and Deployment
2. Multi-Language Setting
<!--more-->

# Why Hugo? 
Fast. Flexible. Open Source. Go... Plenty of reasons for trying it. Actually, I
don't care. For me, the key point is that it works well with Emacs, org-mode.
But this article won't mention any of Emacs. Before I integrate Hugo with Emacs,
what I did is just using Hugo like a normal person. And it's just soooo easy and
convenient to use.

So, the purpose of this article is just to show you how to use Hugo with just a
little knowledge about command line.

# Before start
Three things to ensure a smooth experience:
1. Basic knowledge of shell 
2. Ability to check official document
3. Basic usage of Github 

Operating System: `macOS`

# Installation and Deployment

## Install 
Run the commands below. Checkout document for details: [Quick Start](https://gohugo.io/getting-started/quick-start/)
```shell
$ brew install hugo    # Homebrew
$ hugo version         # Verify, in my case v0.60.1
$ hugo new site <projectName> # Create project directory
$ cd <projectName>     # Go to the new directory 
$ git init             # Version control
```
Then make it a Github repository. No further instruction here.

## Add theme 
In my case, I choose [Beautiful Hugo](https://themes.gohugo.io/beautifulhugo/)
as my theme. 

Notice that in my configurations below, some of them maybe required specifically by this theme. 

Install the theme:
```shell
$ git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo
# use theme's example config file as a start point
$ cat themes/beautifulhugo/exampleSite/config.toml >> config.toml 
```
Before you modify the configuration now, you may need to explore the
files under `<project>/themes/beautifulhugo/exampleSite`. It helps you to
understand the whole structure.

Play around with your `config.toml`. Refer to document for more details:
[Configuration](https://gohugo.io/getting-started/configuration/). 

****Note: you need to set `baseURL` right. In my case, the value is in a format
like this `"https://<username>.github.io/<projectName>/"`**

## Test basic usage
```shell
$ hugo new post/testing-post.md # Create a new post
```
The new file will locate under `<project>/content/post/`.

****BeautifulHugo specification: to make some functions work, you must use the
words `page` and `post` as the directory.**

Now start the server to check the layout:
```bash 
$ hugo server -D # Start the Hugo server locally
```
Go to <http://localhost:1313/> (or http://localhost:1313/projectName/ in my
case) to check the blog. Usually it look wired, so keep modifying `config.toml` until you're happy (Please make your life easy. No need to be perfect).

If everything works fine, you can now start to publish your blog on
the Internet. Let's deploy it on Github pages.

## Deploy the blog
There're lots of ways of doing it. In my case, I choose to deploy the blog on
project page and use `gh-pages` branch. The official document describes it quite
comprehensively:
- [General Description](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
- [Specific to my
case](https://gohugo.io/hosting-and-deployment/hosting-on-github/#deployment-of-project-pages-from-your-gh-pages-branch) (use `origin` to replace `upstream`)

Must follow the document **carefully**, and you will finally finish setting up
the gh-pages and get a script named `publish_to_ghpages.sh` to automate your
future publishing.

****Notice that in my case, it needs to use `origin` to replace `upstream` in the command
provided in document.** As the master branch of my remote repo is named `origin`.

## Simple workflow
After it's successfully deployed, go to the github page of this repo, you shall
see 2 branches. `gh-pages` branch is treated as the published site and `master`
branch is your working branch. By doing so, the source files and rendered files
are separated, which helps maintenance and version control.

For the workflow of writing and posting an article to your blog:
1. run `hugo new post/Article-Name.md`
2. write the article
3. run `hugo server -D` and check the final product
4. before publishing, in the article's front matter, set `draft = false`
5. in master branch, commit the new changes
6. run `./publish_to_ghpages.sh` (the shell script you created)

Done.

# Multi Language Setting
Beautiful Hugo provides some functions to support multi languages, and so does
other themes. Here I introduce my settings: 
- English as default
- Simplified Chinese as secondary 
- Different main menu for each language
- An option for switching language, placed in main menu, top right corner. (Supported by Beautiful Hugo)
- For each article, an option for switching language, placed under the title. (Supported by Beautiful Hugo) 

The files needed to be changed:
- `config.toml`
- `nav.html`
- structure under `content/`
- `single.html`, `terms.html`, `post_preview.html`

## Modify `config.toml`
Details in official document: [Multilingual](https://gohugo.io/content-management/multilingual/)

Here's the excerpt of my `config.toml`, it sets the languages and their main
menu.
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

## Modify `nav.html`
`nav.html` is a partial template. For more details, go to: [Partial
Templates](https://gohugo.io/templates/partials/). Partial template can be placed in
`layouts/partials/` or `themes/<themename>/layouts/partials/`. 

`nav.html` is a template provided by Beautiful Hugo, which sets the format of
main menu. It works by default, but in my case, since baseURL is set as
`<username>.github.io/<projectName>`, there's one place need to be changed.

To modify the templates provided by the theme, I recommend to do this:
```shell
# make a copy of theme's nav.html and place it in your own place
$ cp themes/beautifulhugo/layouts/partials/nav.html layouts/partials/nav.html
```
Then modify the `nav.html` under `layouts/partials/`. Hugo engine will read your
template instead of the theme's. (More details about [Hugo's Lookup
Order](https://gohugo.io/templates/lookup-order/) )

Here's the excerpt of my `nav.html`. In my case, the baseURL is
`muscaestar.github.io/myBlog/`, so I'll add `/myBlog/` to the original code. See
line 39, 48.
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

## Modify structure under `content/`
The file structure must match with the `contentDir` variable specified in
`config.toml`. In my case, the structure is set as below:
```
content/      content/      
└── en/       └── zh-CN/       
    ├── page/     ├── page/ 
    └── post/     └── post/ 
```

## Modify `single.html`, `terms.html`, `post_preview.html` (Updated)
The problem comes with the variable `.Site.LanguagePrefix`, for some reasons, it
doesn't return complete baseURL. The url of tags will be affected by this problem. The solution is to use `.Site.Language.Lang`.
Here're the details:
```shell
$ cp themes/beautifulhugo/layouts/_default/single.html layouts/_default/single.html
$ cp themes/beautifulhugo/layouts/_default/terms.html layouts/_default/terms.html
$ cp themes/beautifulhugo/layouts/partials/post_preview.html layouts/partials/post_preview.html 
```
Then use `.Site.Language.Lang` to replace `.Site.LanguagePrefix` in the
following files:
- layouts/_default/single.html
- layouts/_default/terms.html
- layouts/partials/post_preview.html 

# In the End
So far, a multi language blog is successfully built. It already has a pretty
easy workflow for blogging, and it's actually enough for daily use. 
For explorers, there're still plenty of advanced functionalities of
Hugo, and for me, I'll start working on the integration of Hugo and Emacs, org-mode.




