---
title: 设计稿的1px究竟有多大？作为前端应该了解的物理像素、逻辑像素、drp、dpi...
date: 2025-01-15 18:41:23
tags: 
  - css
  - px
  - drp 
categories:
  - css
---

# 前言

我们做 pc 网页开发或者移动端 h5 开发，绕不开的就是一个不同屏幕的适配问题。都知道有几种解决方案：rem 适配、vw 适配...但为什么需要做适配呢？按设计稿中的 10px 写，在不同设备里为什么宽度看起来不一样呢？

电脑“调整系统分辨率”和“缩放”都能调整元素在屏幕中显示的大小，他们有什么区别吗？

{% note no-icon %}
这些问题网上的很多文章也都有讲，但花了一整天的时间翻看了很多资料，发现有的文章存在一些错误，或者讲得比较简单，容易造成误区，看得头晕眼花😵。于是自己写一篇博客，做个记录，详细解释下这些问题。
{% endnote %}

# 一些概念

## 不同的像素

### 设备像素（device pixels, dp）
设备像素，也可以叫作**物理像素**。我们可以理解为显示屏都是由许多个小方块组成的，每个小方块都可以发出不同的光，来一起组成我们的图像。这个“小方块”指的就是物理像素。

{% asset_img dp.png %}
{% asset_img A.png %}

**物理像素并不是固定大小的**。你可以将 1920 x 1080 个物理像素放进16英寸的显示屏里，也可以将这么多物理像素放进27英寸的显示屏里。当然，相同物理像素个数下，27英寸显示屏的单个物理像素比16英寸显示屏的单个物理像素要大得多。

物理像素的密度与大小，在显示器被制造出来的时候就已经固定了，之后是没有办法改变的。

### 设备独立像素（device independent pixels, dip / dips）
设备独立像素，顾名思义，就是独立于设备的像素，又叫**逻辑像素**。我们刚刚提到，物理像素的大小是可以变化的，不同设备都不一样，那么我们在设计或者编码的过程中，用物理像素做单位没有办法保证描述的长度是相同的。

逻辑像素就是用来解决这个问题的，它是一个与设备屏幕无关，在编程和设计领域中使用的抽象概念、虚拟像素。例如，在 CSS 中，我们通常使用 **px** 作为单位来设置元素的宽高和位置，我们希望不同设备的 1px 肉眼看起来长得差不多大。在大多数情况下，你可以将 CSS 像素看作是逻辑像素。但他们{% emp 并不是完全一样的 %}。很多文章将 CSS 像素简单看作是逻辑像素，其实不然。

一个逻辑像素可能由一个物理像素渲染，也可能由多个物理像素渲染。在下文我们会详细解释。

### CSS 像素
就像上面说的，CSS 像素是一个在 CSS 中用来统一样式大小的虚拟像素。CSS 像素的尺寸可以粗略地视为人眼可以舒适地看到的尺寸，既不能小到不得不眯着眼睛看，也不能大到看到像素化。我们平时设计稿中的 px，代码中的 px，都是 CSS 像素。

下面的网址介绍了 CSS 像素大小的定义，可以简单参考了解一下。

> [Absolute Lengths: the cm, mm, Q, in, pt, pc, px units](https://drafts.csswg.org/css-values/#absolute-lengths)：The reference pixel is the visual angle of one pixel on a device with a device pixel density of 96dpi and a distance from the reader of an arm’s length. For a nominal arm’s length of 28 inches, the visual angle is therefore about 0.0213 degrees. For reading at arm’s length, 1px thus corresponds to about 0.26 mm (1/96 inch).
The image below illustrates the effect of viewing distance on the size of a reference pixel: a reading distance of 71 cm (28 inches) results in a reference pixel of 0.26 mm, while a reading distance of 3.5 m (12 feet) results in a reference pixel of 1.3 mm.
{% asset_img pixel1.png %}

刚刚也提到，大多数情况下可以将 CSS 像素看作是设备独立像素，但什么时候它们不相等呢？

**当设备缩放比例为 100% 的时候，1 个 CSS 像素和 1 个设备独立像素的大小是相等的。缩放比例就是 CSS 像素边长 / 设备独立像素边长，例如，当设备缩放比为 125% 的时候，1 个 CSS 像素边长 = 1.25 个独立像素边长。**

这个具体原因，我们下文会讨论。

## 不同的分辨率
### 物理分辨率
有物理像素，那么就有物理分辨率。上文提到，“物理像素的密度与大小，在显示器被制造出来的时候就已经固定了”，这个“密度与大小”就可以用物理分辨率来表示。

物理分辨率代表了显示屏中有多少个物理像素点。比如，我们常用的 1080p 显示器，物理像素就是：1920 x 1080，表示横向一行有 1920 个物理像素点，纵向一列有 1080 个物理像素点。同样，物理分辨率也是在设备出厂时就定了。我们在购买设备时，设备信息介绍的分辨率就是物理分辨率。

{% asset_img buy.png %}
{% asset_img resolution.png %}

### 逻辑分辨率
逻辑分辨率，指的就是**参与渲染的逻辑像素有多少个**。这个是可以在设备中调节的，以我的 Mac 为例，它默认的分辨率是1728 x 1117，如果你想要每一屏展示的内容更多，就可以将分辨率调成 2056 x 1329，这样就会有 2056 x 1329 个逻辑像素参与渲染；如果你想让每一屏展示的内容更大，那就调成 1496 x 967。（至于为什么内容会放大 / 缩小，我们也在后面细讲）
{% asset_img resolution2.png %}

## 指标
### 设备像素比（devicePixelRatio, dpr）
刚刚我们介绍了物理像素、逻辑像素和 CSS 像素，那这几种像素之间有什么关系呢？这就是由设备像素比决定的。我们这里讨论的设备像素比是由`window.devicePixelRatio`得出的，它代表{% emp 当前显示设备的物理像素分辨率与 CSS 像素分辨率之比。 %}此值也可以解释为像素大小的比率：一个 CSS 像素的大小与一个物理像素的大小。简单来说，它告诉浏览器**应使用多少屏幕实际像素来绘制单个 CSS 像素**。（来自 MDN：[Window.devicePixelRatio](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/devicePixelRatio)）

dpr = 物理像素 / CSS 像素，这里的比值指的是边长，比如 dpr = 2 的时候，1 个 CSS 像素将由 2 x 2 个物理像素绘制。因为像素块一般都是正方形。话不多说，上图：
{% asset_img allDpr.png %}

在 iphone4 问世之前，所有设备的 dpr 都是 1，1 个 CSS 像素就只由 1 个物理像素渲染。iphone4 带来了一个新的概念：Retina 屏（视网膜显示屏）。Retina 屏在有限的屏幕空间内增加更多的像素，实现了高分辨率屏幕，极大优化了用户体验。同一个物理面积，Retina 屏用 4 x 4 = 16 个像素点渲染，普通屏幕用 2 x 2 = 4 个像素点渲染，那么Retina 屏的展示显然比普通屏细腻得多。看图感受一下：

{% asset_img dpr.png %}
{% asset_img retina.png %}
