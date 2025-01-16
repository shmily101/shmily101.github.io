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

{% span blue, 物理像素并不是固定大小的 %}。你可以将 1920 x 1080 个物理像素放进16英寸的显示屏里，也可以将这么多物理像素放进27英寸的显示屏里。当然，相同物理像素个数下，27英寸显示屏的单个物理像素比16英寸显示屏的单个物理像素要大得多。

物理像素的密度与大小，在显示器被制造出来的时候就已经固定了，之后是没有办法改变的。

### 设备独立像素（device independent pixels, dip / dips）
设备独立像素，顾名思义，就是独立于设备的像素，又叫**逻辑像素**。我们刚刚提到，物理像素的大小是可以变化的，不同设备都不一样，那么我们在设计或者编码的过程中，用物理像素做单位没有办法保证描述的长度是相同的。

逻辑像素就是用来解决这个问题的，它是一个与设备屏幕无关，在编程和设计领域中使用的抽象概念、虚拟像素。例如，在 CSS 中，我们通常使用 **px** 作为单位来设置元素的宽高和位置，我们希望不同设备的 1px 肉眼看起来长得差不多大。在大多数情况下，你可以将 CSS 像素看作是逻辑像素。但他们{% emp 并不是完全一样的 %}。很多文章将 CSS 像素简单看作是逻辑像素，其实不然。

### CSS 像素
就像上面说的，CSS 像素是一个在 CSS 中用来统一样式大小的虚拟像素。CSS 像素的尺寸可以粗略地视为人眼可以舒适地看到的尺寸，既不能小到不得不眯着眼睛看，也不能大到看到像素化。不用去咨询眼科医生“舒适地看到”的定义。

下面的网址介绍了 CSS 像素大小的定义，可以简单参考了解一下。

> [Absolute Lengths: the cm, mm, Q, in, pt, pc, px units](https://drafts.csswg.org/css-values/#absolute-lengths)：The reference pixel is the visual angle of one pixel on a device with a device pixel density of 96dpi and a distance from the reader of an arm’s length. For a nominal arm’s length of 28 inches, the visual angle is therefore about 0.0213 degrees. For reading at arm’s length, 1px thus corresponds to about 0.26 mm (1/96 inch).
The image below illustrates the effect of viewing distance on the size of a reference pixel: a reading distance of 71 cm (28 inches) results in a reference pixel of 0.26 mm, while a reading distance of 3.5 m (12 feet) results in a reference pixel of 1.3 mm.
{% asset_img pixel1.png %}

刚刚也提到，大多数情况下可以将 CSS 像素看作是设备独立像素，但什么时候它们不相等呢？

{% p blue, 当设备缩放比例为 100% 的时候，1 个 CSS 像素和 1 个设备独立像素的大小是相等的。缩放比例就是 CSS 像素边长 / 设备独立像素边长，例如，当设备缩放比为 125% 的时候，1 个 CSS 像素边长 = 1.25 个独立像素边长。 %}

这个具体原理，我们下文会讨论。
