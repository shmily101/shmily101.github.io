---
title: JS引擎（二）：探索V8编译流水线
date: 2024-06-13 17:25:52
tags:
  - JavaScript
  - Browser
  - Engine
categories:
  - JS引擎
---

前一篇我们说明了JS是一个解释型语言，但为了提高运行速度，我们引入了JIT即时编译，减少JS运行时重复代码的翻译。那么JS究竟是怎么被引擎所执行的？JIT又是在哪个环节加入的呢？

## 了解V8
### JS引擎
JS引擎也可以称为虚拟机，会模拟计算机的CPU、堆栈、寄存器等，还具有自己的一套指令集系统，负责解析运行JavaScript代码。我们前文提到的JS代码解释翻译都是在JS引擎中实现的。浏览器之所以可以运行JS代码，也是因为在浏览器中存在JS解析引擎。

> 不同的浏览器为了提升速度，会不断升级它们的JS引擎。比如mozilla基金会研发的浏览器FireFox，就在 2017 年重写了浏览器的引擎（称为 Quantum）以确保 Firefox 能够与其他主流浏览器竞争。

### V8是什么
V8引擎是一个由Google开发的开源Javascript引擎，目前用在**Chrome浏览器**和**Node.js**中。

Chrome的市场占有率高达60%，而Node.js是JS后端编程的事实标准。国内的众多浏览器，其实都是基于Chromium浏览器开发，而Chromium相当于开源版本的Chrome，自然也是基于V8引擎的。

## V8是怎么执行JS代码的？
V8混合了编译执行和解释执行两种执行方式，使执行JS的过程既可以获得编译执行的**快执行速度**，又可以获得解释执行的**快启动速度**。

执行JS代码时，会遇到以下几个阶段：
![](V8执行JavaScript流程图.jpg)
- 准备运行环境
- **Parse**：通过**解析器**，将JS源代码组织成**抽象语法树(AST)**
- **Ignition**：通过**解释器**，将抽象语法树转化为**字节码**
- **TurboFan**：通过**编译器**，将字节码优化**编译为机器码**执行并输出结果。这也是JIT的核心。

明白这几个阶段具体都做了什么之后，我们了解到的一些js特性，比如变量提升、函数作用域的生成、执行上下文的创建等等，都会得到更深层次的理解。

### 准备运行环境
上面我们提到V8引擎运行在chrome浏览器和Node.js中，这里的chrome浏览器和Node.js就是**宿主环境**。V8运行在宿主环境中，实际上V8只提供了**js核心**以及**垃圾回收机制**，并非是一个完整的系统。V8需要有宿主环境提供基础功能部件，包括**全局执行上下文、事件循环系统，堆空间和栈空间**。


在js代码开始执行之前，V8已经根据不同的宿主环境准备好了代码运行所需要的环境，包括：**堆空间和栈空间、全局执行上下文、全局作用域、内置的内建函数、宿主环境提供的扩展函数和对象，还有消息循环系统。**