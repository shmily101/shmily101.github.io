---
title: CSS动画：利用 Transition 组件实现动态高度卡片收起 / 展开过渡动画
date: 2024-12-20 15:01:52
tags:
  - CSS
  - Animation
categories:
  - Animation
---

## 1. 背景介绍

最近在做 AI Power 官网的时候有这样一个需求：首页要做一个常见问题模块，有高度不一的几个问题卡片，长这样：

{% asset_img Q&A.png %}

卡片展开、收起太过生硬，于是想要采用 vue 的 Transition 加一个流畅的动画过渡效果。

## 2. 