---
title: hexo 与 submodule
tags:
  - hexo
  - git
  - submodule
categories:
  - hexo
---

今天想要给博客换个主题，本来以为简单clone一下 + 改个配置就好了，结果引出一堆坑 :cry:

## 一、git submodule

删除旧主题之后，我按照新主题的教程，使用了`git clone`将主题克隆到`themes`文件夹里，配置好`_config.yml`文件，在本地跑也没问题，`gitflow`部署却报错了：

```
No url found for submodule path 'themes/xxx' in .gitmodules
```


