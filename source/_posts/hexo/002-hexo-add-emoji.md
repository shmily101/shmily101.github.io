---
title: hexo 博客添加 emoji 表情
date: 2024-09-03 17:25:52
tags:
  - hexo
  - emoji
categories:
  - hexo
---

博客中只有文字也太单调了，想在文章中添加 emoji ，发现直接添加无法解析。原来是 hexo 默认使用的`hexo-renderer-marked`不支持插件扩展，所以不支持 emoji 解析。我们可以将`hexo-renderer-marked`更换为支持插件扩展的`hexo-renderer-markdown-it`，这个支持插件配置，可以使用`markwon-it-emoji`插件来支持 emoji 。

`hexo-renderer-markdown-it`拥有更强大的功能：

{% note info %}
Main Features
- Support for Markdown, GFM and CommonMark
- Extensive configuration
- Faster than the default renderer | hexo-renderer-marked
- Safe ID for headings
- Anchors for headings with ID
- Footnotes
- `<sub>` H2O
- `<sup>` x2
- `<ins>` Inserted
{% endnote %}

## 1. 安装步骤

首先卸载`hexo-renderer-marked`，并安装`hexo-renderer-markdown-it`：
```
npm un hexo-renderer-marked --save
npm i hexo-renderer-markdown-it --save
```

接下来安装`markdown-it-emoji`插件：
```
npm install markdown-it-emoji --save
```

## 2. 编辑配置文件

在`_config.yml`中添加：
```
markdown:
  preset: 'default'
  render:
    html: true
    xhtmlOut: false
    langPrefix: 'language-'
    breaks: true
    linkify: true
    typographer: true
    quotes: '“”‘’'
  enable_rules:
  disable_rules:
  plugins:
    - markdown-it-emoji  ## add emoji
  anchors:
    level: 2
    collisionSuffix: ''
    permalink: false
    permalinkClass: 'header-anchor'
    permalinkSide: 'left'
    permalinkSymbol: '¶'
    case: 0
    separator: '-'
  images:
    lazyload: false
    prepend_root: false
    post_asset: false
  inline: false  # https://markdown-it.github.io/markdown-it/#MarkdownIt.renderInline
```
这里是`hexo-renderer-markdown-it`的配置，具体每一项的配置参考[hexo-renderer-markdown-it](https://github.com/hexojs/hexo-renderer-markdown-it)

{% note primary %}
记得在`plugins`中添加`markdown-it-emoji`！
{% endnote %}

## 3. 使用方法
想要添加Emoji时，在[emoji 列表](https://gist.github.com/rxaviers/7360908)中查询表情对应的码值，复制即可