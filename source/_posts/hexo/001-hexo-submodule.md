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

这个submodule是啥东西呢？我们先来介绍一下。

### 1. 什么是 git submodule

Git Submodules 叫作 Git 子模块，是 Git 提供的一种功能，允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。它可以让你将另一个仓库克隆到自己的项目中，同时还保持提交的独立。

> 它非常适合我们程序员在项目管理时遇到的一种情况: 某个工作中的项目需要包含并使用另一个项目。这些包含的项目也许是第三方库，或者你独立开发的，用于多个父项目的库。你想要把它们当做两个独立的项目，同时又想在一个项目中使用另一个。

hexo 中有着丰富的主题可以选择，大部分的使用示例或者教程都是将主题 clone 到 theme 文件夹中来使用。但这样来使用主题会存在如下的问题：

theme 中的主题属于一个独立的 Git 项目，有自己的 .git 项目文件夹，提交 hexo 项目时默认不会提交 theme 的 .git 文件夹，在其他电脑上 clone 后会失去 theme 原本的版本控制功能。

使用自己修改的主题时，每次更改完主题，需要在主题文件夹中提交一次，然后再在 hexo 项目根文件夹中提交一次，会产生两次修改内容一样的提交，不够优雅。

还好万能的 Git 针对这种问题已经有了成熟的解决方案，通过自带的 Git submodules 功能即可优雅的避免以上的问题。


### 2. 如何使用 git submodule ？

#### 2.1 添加子模块：

使用 git submodule add 命令将一个外部仓库添加为子模块。
```
git submodule add <repository-url> <path>
```
例如：
```
git submodule add https://github.com/example/repo.git libs/repo
```
#### 2.2 初始化和更新子模块： 

当你克隆一个包含子模块的仓库时，需要初始化和更新子模块。
```
git submodule update --init  // 初始化子模块
git submodule update --recursive  // 递归更新所有子模块，包括嵌套子模块
```

### 2.3 修改子模块

上述提到的都是在父模块目录中进行的操作。当然你也可以在子模块中进行完全独立的git操作，比如拉取、暂存、提交、合并等等，和在单独的仓库中完全一样。

更新子模块： 当子模块有更新时，你可以进入子模块目录并拉取最新的更改。
```
cd path/to/submodule
git pull origin main
```

提交子模块的更改： 当子模块有更改时，你需要在主项目中提交子模块的引用更新。
```
git add path/to/submodule
git commit -m "Update submodule"
```

更多操作和介绍可见：

{% link 在 hexo 中使用 git submodules 管理主题 ,在 hexo 中使用 git submodules 管理主题 ,https://juejin.cn/post/6844903751908605965 %}

{% link Git 工具 - 子模块 ,Git 工具 - 子模块 ,https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97 %}

## 3. 使用子模块的注意事项
- 版本同步：子模块的版本是独立管理的，需要手动同步子模块的版本。
- 克隆和更新：克隆包含子模块的仓库时，需要额外的步骤来初始化和更新子模块。
- 复杂性：子模块增加了项目的复杂性，特别是处理子模块的更新和冲突的时候。

## 4. 适用场景
Git 子模块非常适合以下场景：

- 第三方库：在项目中包含和使用第三方库，在 Hexo 中使用第三方主题就非常适合使用子模块。
- 共享库：在多个项目中共享和复用一些独立开发的库。
- 插件系统：在项目中包含和管理多个插件，每个插件作为一个独立的子模块。

## 二、 解决报错

submodule 学完了，那为啥会出现这个报错呢？我也没用到`git submodule`呀。

翻了下根目录里的`.git/modules`，出现了`themes/xxx`的文件夹。并且执行`git submodule add <repository-url> themes/xxx`，也会提示`'themes/xxx' already exists in the index`。

由此猜测是 git 自动将克隆的 theme 文件夹识别为了 submodule，但我们并没有任何`.gitmodules`文件配置，因此报了错。

找到问题之后就好解决了，首先删除`.git/modules`，一切从0开始。既然使用 submodule 有这么多好处，那我们就直接使用

```
git submodule add <repository-url> themes/xxx
```

来使用这个主题。这里要注意几个点：

- 如果仅仅是使用主题，**不会做任何自己的修改**，可以采用`--depth=1`浅克隆。它可以克隆指定深度的提交历史记录，而不是整个仓库的完整历史。这可以显著减少克隆时间和磁盘空间的使用，特别是在处理大型仓库时。
  - 一定要注意！！采用浅克隆仅限于不做修改简单使用主题，由于历史记录不完整，使用`--depth=1`无法切换分支、查看历史记录、使用依赖完整历史记录的操作例如 `git bisect` 和 `git blame`等等。
- 可以使用`-b`参数，来指定克隆的子模块分支。

执行完后会看到根目录生成了新的`.gitmodules`文件，文件中包含了刚刚的子模块。使用这个方法引入主题，之后想要更新就变得非常方便了。