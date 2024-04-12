---
title: JS引擎（一）：探索V8编译流水线
---

## 解释型语言

JavaScript一直被称为解释型语言，即不经过编译的过程，直接在运行时由解释器将js源代码一句句翻译执行。但最近接触到JavaScript其实引入了JIT（Just in Time）即时编译，它会对代码进行一些优化，也可以做到生成并存储代码的编译版本。

此外，JavaScript存在变量提升的特性——在作用域内任何var声明的变量都会被提升到顶部并且赋值为undefined。

如果JS是一个纯解释型语言，仅通过解释器逐行翻译源代码执行是怎样提前获取到作用域内部的变量声明，并提升到作用域顶部的呢？

抱着这样的疑问，我开始了解JS引擎（以nodeV8为例）对JS代码的处理过程。

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)
