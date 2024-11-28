---
title: 优雅地在 hexo 博客中插入图片
tags:
  - hexo
  - images
  - assets
categories:
  - hexo
---
  
想要在 hexo 中插入图片，有两种方式：
- 放在**全局资源文件夹**里，使用绝对路径引用图片；
- 放在**文章资源文件夹**中，使用相对路径引用图片。

{% link 资源文件夹,hexo官方文档,https://baike.baidu.com/item/%E5%8E%9F%E5%9C%B0%E7%AE%97%E6%B3%95 %}

下面我们分别对这两种不同的插入方式进行介绍。

## 绝对路径引入图片

当我们的 Hexo 项目中只有少量图片的时候，使用全局资源文件夹是最简单的方式。我们只需要在`/source`文件夹下面新建一个资源文件夹，比如`/source/images`，将所有的图片存放在这个文件夹下，然后在项目中采用绝对路径进行引用：
```markdown
![](/images/image.jpg)
```
但这种方式仅限于**图片非常少**的时候，当博客的博文越来越多的时候，这种方式非常不利于资源管理。更推荐采用相对路径引入图片的方式。

## 相对路径引入图片

hexo 提供了一个资源文件管理功能，可以通过将 config.yml 文件中的 post_asset_folder 选项设为 true 来打开。
```_config.yml 
post_asset_folder: true
```
>当资源文件管理功能打开后，Hexo将会在你每一次通过 `hexo new [layout] <title>` 命令创建新文章时自动创建一个文件夹。 这个资源文件夹将会有与这个文章文件一样的名字。 将所有与你的文章有关的资源放在这个关联文件夹中之后，你可以通过相对路径来引用它们，这样你就得到了一个更简单而且方便得多的工作流。

比如：
```
hexo new page_name
```
会生成这样的结构：
{% asset_img newPage.png %}

在`page_name`文件夹中放入图片，在`page_name.md`中就能直接使用相对路径比如`![](image.jpg)`引用图片资源了。

如果你使用了子文件夹去整理文章，和我一样的话，**文章资源文件夹需要和文章建立在同一目录下**。比如：
{% asset_img fold.png %}

但这时，直接使用相对路径引入的图片，只能在**博文详情页**展示，在首页或者存档页的预览会不正确。

### 使用标签插件相对路径引入

hexo3 中提供了标签插件，在标签插件中使用相对路径可以自动被解析，图片在文章和首页/归档页中可以同时显示。

```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

例如：
```
{% asset_img example.jpg This is an example image %}
{% asset_img "spaced asset.jpg" "spaced title" %}
```

### 借助 hexo-renderer-marked 使用 markdown 语法通过相对路径引入
> hexo-renderer-marked 3.1.0 引入了一个新的选项，其允许你无需使用 asset_img 标签插件就可以在 markdown 中嵌入图片

只需要在 `_config.yml` 中加入以下配置：
```
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
```
- `prependRoot: true`
  - 将文章根路径添加到文章内的链接之前。此为默认配置。
- `postAsset: true`
  - 在 post_asset_folder 设置为 true 的情况下，在根据 prependRoot 的设置在所有链接开头添加文章根路径之前，先将文章内资源的路径解析为相对于资源目录的路径。

> 启用后，资源图片将会被自动解析为其对应文章的路径。 例如： image.jpg 位置为 /2020/01/02/foo/image.jpg ，这表示它是 /2020/01/02/foo/ 文章的一张资源图片， ![](image.jpg) 将会被解析为 `<img src="/2020/01/02/foo/image.jpg">` 。

这种方式在部署后可以正常显示图片，但编写 blog 的过程中自己预览会有一些问题，详情配置可以参考这篇 blog ，让开发过程中也可以预览：

{% link Hexo插入图片并解决图片的路径问题,Hexo插入图片并解决图片的路径问题,https://www.hwpo.top/posts/d87f7e0c/index.html %}

我因为想要使用 emoji 功能，没有使用 hexo-renderer-marked，而是换成了 hexo-renderer-markdown-it，这个插件本身没有 prependRoot 的功能，只能通过一些其他插件实现上述功能，比如[hexo-image-link](https://github.com/cocowool/hexo-image-link)。