---
title: 微调 hugo-theme-next 主题
author: ''
date: '2020-02-19'
slug: tweak-hugo-theme-next
categories: []
tags: []
toc: yes
---

hugo 主题选用的是[兰陵子](http://lanlingzi.cn/)的 [hugo-theme-next](https://github.com/xtfly/hugo-theme-next)，这个主题简洁实用，许多人性化的小细节让人觉得很贴心：搜索框、文章目录、归档、分类、标签、pagination、语法高亮、数学公式的支持、阅读时间、back-to-top button……

为了进一步了解这一主题，使主题的设置更加符合自己的使用习惯，对主题进行了下述微调。

<!--more-->

## 根据 [issues 页](https://github.com/xtfly/hugo-theme-next/issues)进行修改

- 去掉 revolvermaps

删掉 `/themes/next/layouts/partials/sidebar/stats.html`， 在`/themes/next/layouts/partials/sidebar.html`中删掉`{{ partial "sidebar/stats.html" . }}`，在 config.toml 中删掉 `RevolverMapId = "xxx"`。

- [禁止博客打开后的自动刷新](https://github.com/xtfly/hugo-theme-next/issues/15)

> 所有的页面动画效果在js/motion.js中，若不想让右边的再刷一次，可以把279行注释掉，菜单动画效果是254行的menu函数，侧面栏是283行的sidebar函数。

这里将 254-288 行都注释掉了，类似的问题见 [主题的文章显示时的动画怎么去掉](https://github.com/iissnan/hexo-theme-next/issues/441)。

- [修改代码块样式](https://github.com/xtfly/hugo-theme-next/issues/11)

将 `/themes/next/static/css/main.css` 中 `.highlight pre` 的 `padding: 10px 0` 改为 `padding: 0`，可将黑框的宽度设为0（否则横拉条的颜色为白色，黑色边框有宽度，显得不协调）。

## 修改 config.toml

添加 `ignoreFiles = ["\\.Rmd$", "\\.Rmarkdown$", "_files$", "_cache$"]` [^1]。

[^1]: https://bookdown.org/yihui/blogdown/configuration.html

添加 `footnotereturnlinkcontents = "↩"` ，这样点击脚注中的`↩`便可以返回到所链接的内容。

修改 `[[Params.Socials]]` 中的 Icon [^2]，可改为 E-mail，如：

[^2]:[怎样把config.toml中的参数指定为本地图片，比如微信、qq的图标？](https://d.cosx.org/d/419789-blogdown/2)

```toml
[[Params.Socials]]
  Name = "E-mail"
  Icon = "envelope"
  URL = "mailto:your_address@xxx.com" 
```

## 支持 math expressions [^3]

为了确保 `*.md` 和 `*.Rmd` 文件中的数学公式都能够正确显示，没有采用主题提供的 [support latex](https://github.com/xtfly/hugo-theme-next/commit/ae17f677721cbea04c55783de79b1b63be237410)，而是在 `/themes/next/layouts/partials/script.html` 中添加：

```html
<script src="//yihui.org/js/math-code.js"></script>
<!--<script async
src="//cdn.bootcss.com/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML">
</script>-->
<script async src="//mathjax.rstudio.com/latest/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```

其中，`<script src="//yihui.org/js/math-code.js"></script>`可确保 plain Markdown posts （`*.md`格式）中用一对 backticks 括起来的公式不会以代码的形式显示[^4]。

[^3]: https://bookdown.org/yihui/blogdown/templates.html#how-to
[^4]:https://bookdown.org/yihui/blogdown/output-format.html#fnref17

## 支持语法高亮

主题提供了 [Syntax Highlighter](https://github.com/xtfly/hugo-theme-next/commit/4529c5cc44a057af54bace7d58331f95fd0d5c49)，但这里使用 Highlight.js 进行语法高亮。删除 `/themes/next/static/css/syntax.css`，在 `/themes/layouts/partials/head.html` 中将 `<link href="/css/syntax.css" rel="stylesheet" type="text/css" />` 替换为  `<link href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/monokai.min.css" rel="stylesheet">` [^5]。在 `/themes/next/layouts/partials/script.html` 中添加 [^6]

```html
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/highlight.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/languages/r.min.js"></script>

<script>
hljs.configure({languages:[]});
hljs.initHighlightingOnLoad();
</script>
```

[^5]: [Hugo中添加代码高亮支持](https://note.qidong.name/2017/06/24/hugo-highlight/)
[^6]: https://bookdown.org/yihui/blogdown/templates.html#how-to

## 添加 Disqus 评论

参照 [Hexo搭建博客系列：（六）Hexo 添加 Disqus 评论](https://www.jianshu.com/p/d68de067ea74) 注册 disqus，Website Name 为 Chengying Jiang，short name 为 chengying-jiang。在 config.toml 中添加`disqusShortname = "chengying-jiang"`，并修改 `baseurl` [^7]。

在 `/themes/next/layouts/_default/single.html` 中添加`{{ template "_internal/disqus.html" . }}` [^8]，删除`{{ partial "widgets/comment.html" .}}`及对应文件。

[^7]: [Hugo 网站的 Disqus 加载问题](https://dapengde.com/archives/19317)
[^8]: https://bookdown.org/yihui/blogdown/templates.html#how-to

## 双语网站

主题支持 `i18n`, 但似乎在双语网站的支持上走的不太远（也可能是自己没找到设置的地方）。

在 config.toml 中添加 [^9]：

```toml
defaultContentLanguage = "zh"
defaultContentLanguageInSubdir = true

[languages]
  [languages.zh]
    title =  "成英的小站"
    Subtitle = "云散月明 天容海色"
  [languages.en]
    title = "Chengying's blog"

[[languages.zh.menu.main]]
  Name = "首页"
  Pre = "home"
  URL = "/zh/"
  Weight = 1
[[languages.en.menu.main]]
  Name = "Home"
  Pre = "home"
  URL = "/en/"
  Weight = 1
[[languages.zh.menu.main]]
  Name = "归档"
  Pre = "archive"
  URL = "/zh/post/"
  Weight = 2
[[languages.en.menu.main]]
  Name = "Archives"
  Pre = "archive"
  URL = "/en/post/"
  Weight = 2
[[languages.zh.menu.main]]
  Name = "关于"
  Pre = "user"
  URL = "/zh/about"
  Weight = 3
[[languages.en.menu.main]]
  Name = "About"
  Pre = "user"
  URL = "/en/about/"
  Weight = 3
[[languages.zh.menu.main]]
  name = "EN"
  Pre = "fas fa-language"
  url = "/en/"
  weight = 4
[[languages.en.menu.main]]
  name = "中文"
  Pre = "fas fa-language"
  url = "/zh/"
  weight = 4
```

[^9]: [Hugo 网站 academic 主题的多语言界面](https://www.pzhao.org/zh/post/hugo-multilingual/)

`defaultContentLanguageInSubdir = true` 会将 `/content/` 中默认语言（`zh`）的文件重定向到 `/public/zh/`，而 `/content/post/` 下的默认语言（`zh`）文件则会重定向到 `/public/zh/post/`。

为了避免中英文页面、分类、标签等混在一起，通过下述判断语句将 state.html、terms.html、category.html、tags.html 中的 `/` 选择性地改为 `/zh/` 或 `/en/`。

```
{{ if (eq .Site.Language.Lang "zh") }}
...
{{ else }}
...
{{ end }}
```

对于中英文搜索框的配置，沿袭同样的思路，将search.js中的 `var path = "/" + search_path` 改为 `var path = "/zh/" + search_path` 并将该文件命名为 search_zh.js，同理得到 search_en.js。在 `/layouts/partials/script.html` 中将 `<script type="text/javascript" src="/js/search.js"></script>` 替换为：

```html
{{ if (eq .Site.Language.Lang "zh") }}
<script type="text/javascript" src="/js/search_zh.js"></script>
{{ else }}
<script type="text/javascript" src="/js/search_en.js"></script>
{{ end }}
```

## 其他改动

- 删除 config.toml 中的冗余代码。
- 将 `/themes/next/layouts/partials/footer.html` 里的开始时间改为 2018。
- 替换 `/themes/next/static/img/` 下的 author.jpg，并删除 favicon.ico、apple-touch-icon.png。
- 将正文部分的页面宽度设置为 800px，相应地将整体宽度改为 1060px [^10]。

[^10]: [change the width of page](https://github.com/xtfly/hugo-theme-next/commit/eed2fcd89a87327e4eb02ff771170d9d79083a4d)

## 进一步完善

- `*.md` 文件中使用 `#` 标记的一级标题没能显示在 toc 中。
- 打开搜索框，默认没有光标闪烁。
- toc 中过长的内容显示不出。
- toc 的动态缩放效果。
- 为英文博客指定 sidebar 中的 author。
- 订阅中英文 rss 时展示全文。
- 添加license。
- [百度链接提交与统计](https://note.qidong.name/2017/06/29/baidu_push/) 或 Google Analytics [^11]。
- GitHub Edit button [^12].

[^11]: https://bookdown.org/yihui/blogdown/templates.html#how-to
[^12]: https://bookdown.org/yihui/blogdown/templates.html#how-to





