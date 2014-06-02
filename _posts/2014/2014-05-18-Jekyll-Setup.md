---
layout: post
tagline:
category: null
tags: []
published: true
title: Jekyll 设置
---

前段时间测试了下 GitBlog，然后这个站点就自动创建出来了，本来也该就没有然后了。

但今天突然抽风，就给设置一下，使得这个 <lwr.github.io> 不要停留在仅仅是一个 fork 的样子。

算了下大概做了这几件事情

  - 去找了下 theme，然后随便选了这个 [mark-reid](http://mark.reid.name)
    还算能看吧

    ```bash
    rake theme:install git="git://github.com/jekyllbootstrap/theme-mark-reid.git"
    ```
  - 配置了下 `_config.yml` 的基本设置
    主要就是 `title`, `author`, `production_url` 这几项
  - 删掉了 sample post, 改了下 `index.md` 删掉无关的内容
    嗯现在看起来有点像是个 blog 了
  - 改了下 `mark-reid` 的 `header` 和 `copyright` 部分, 不要太乱
  - 去 *disqus* 注册个帐号，然后把配置默认使用的 disqus id "jekyllbootstrap" 替换掉，不然评论栏总是会冒出来些奇怪的广告
  - 配置下 *markdown* engine 符合自己的习惯
  - 还要在本地装个 jekyll 服务方便预览网站的效果

    ```bash
    sudo gem install jekyll
    jekyll serve
    ```
    通过 <http://127.0.0.1:4000> 访问生成的静态站点
    不过每次都要打 `Ctrl+c` 然后重启才能刷新好烦，就不能稍微 monitor 一下变动自动重新发布吗

## Markdown Engine
相对来说就是配置 *markdown* 这有点棘手，本身对 ruby 就不熟，那几个 engine 都是头一次听说，我的需求就几样，现在 [haroopad](http://pad.haroopress.com) 都支持的不错

  - GFM（包括表格的支持以及换行的支持)
  - footnotes
  - superscript 等 （这个要求不强烈，可以直接嵌入 html 解决）

反复试了下 *redcarpet* 和 *kramdown* 的各种配置，都不太满意，都有些奇怪的问题不知道怎么解决

由于 *redcarpet* 不支持扩展 (Attribute List) 现在的选择是 *kramdown*

## 最终的配置
修改过的 `_config.yml` 大概是酱紫

```yaml
... ...
markdown: kramdown
# markdown: redcarpet

... ...
JB :
  ... ...
  comments :
    provider : disqus
    disqus :
      short_name : SoloCompany
  ... ...

... ...
redcarpet:
  extensions:
    - hard_wrap
    # - no_intra_emphasis
    # - autolink
    - strikethrough
    - fenced_code_blocks
    - tables
    - superscript

kramdown:
  input: GFM
  auto_ids: true
  footnote_nr: 1
  entity_output: as_char
  toc_levels: 1..6
  parse_block_html: true
  coderay:
    coderay_wrap: div
    coderay_line_numbers: inline
    coderay_line_numbers_start: 1
    coderay_tab_width: 4
    coderay_bold_every: 10
    coderay_css: style


```
