---
title: 【问题记录】scrollerTigger在nuxt3中由于切换路由导致的动画偏移问题
date: 2024-11-27 11:25:52
tags:
  - JavaScript
  - GSAP
  - Animation
  - Nuxt3
categories:
  - GSAP
  - Animation
---

最近在使用 nuxt3 + gsap 编写动画的过程中，发现会有这样的现象：使用 scrollerTigger 进行滚动条的动画控制，在开发过程中动画一切正常，但当我跳转到另一个页面再回到当前动画页的时候，动画的 start 和 end 位置会有概率发生一些偏移，导致动画效果出现问题，但刷新后又会回归正常。

我猜测也许是因为路由切换的过程中，scrollerTigger 的实例动画未被销毁，再次进入页面的时候 scrollerTigger 动画没有及时更新。但在 `onBeforeUnmounted` 钩子里对动画进行销毁，像这样：

```js
onMounted(() => {
  triggerFn()
})

onBeforeUnmounted(() => {
  ScrollTrigger.getAll().forEach(trigger => trigger.kill());
})
```

```js
function triggerFn() {
  const triggerList = document.querySelectorAll(".fade-trigger")
  triggerList.forEach((item) => {
    const hook = item.getAttribute("data-hook") || "70%"
    gsap.timeline({
      scrollTrigger: {
        trigger: item,
        start: "top " + hook,
        toggleClass: "active",
        markers: false,
      },
    })
  })
}
```

虽然在切换之前对页面的动画进行了销毁，但仍然没有效果，还是会出现错乱的情况。猜测是路由切换时，在 onMounted 中挂载后页面布局又发生了变化，导致`start/end`没有正确计算。

网上有一些和我遇到相同问题的帖子：

> [Nuxt 3 + GSAP ScrollTrigger start/end position problem after route navigation](https://gsap.com/community/forums/topic/33486-nuxt-3-gsap-scrolltrigger-startend-position-problem-after-route-navigation/)：If the start/end values aren't calculated correctly, that means the layout must have shifted AFTER the window's "load" event which would make sense if you're using a framework that's dynamically loading in new routes. So just make sure you call ScrollTrigger.refresh() AFTER you're done shifting around the layout. 

说明需要确保在布局变化渲染完毕后调用`ScrollTrigger.refresh()`刷新动画布局。参照[ScrollTrigger.refresh() 官方文档](https://gsap.com/docs/v3/Plugins/ScrollTrigger/static.refresh())，在`onMounted`中使用 `ScrollTrigger.refresh(true)`，即可保证让页面布局后及时 refresh 动画。

{% note primary %}
`ScrollTrigger`官方文档：
{% asset_img scrollTigger.png %}
{% endnote %}