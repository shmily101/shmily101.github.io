---
title: JS引擎（一）：“解释”与“编译”
tags:
  - JavaScript
  - Browser
  - Engine
categories:
  - JS引擎
---
## 解释型语言

JavaScript一直被称为解释型语言，即不经过编译的过程，直接在运行时由解释器将js源代码一句句翻译执行。但最近接触到JavaScript其实引入了**JIT（Just in Time）即时编译**，它会对代码进行一些优化，也可以做到生成并存储代码的编译版本。

此外，JavaScript存在**变量提升**的特性——在作用域内任何var声明的变量都会被提升到顶部并且赋值为undefined。

如果JS是一个纯解释型语言，仅通过解释器逐行翻译源代码执行是怎样提前获取到作用域内部的变量声明，并提升到作用域顶部的呢？

要解释这个问题，就需要了解JS代码的处理过程。

### 机器语言 & 汇编语言 & 高级语言

当我们编写了一段JavaScript代码并试图运行它的时候，我们就产生了一个目标：告诉计算机要做什么。同时也产生了一个问题：计算机并不能读懂我们的语言。机器运行采用**机器语言**，它仅由“0”与“1”组成，即**用二进制代码表示的计算机能直接识别和执行的一种机器指指令系统令的集合**。

在我们常用的PC机中，有一个芯片，就是我们常说的**CPU（Central Processing Unit，中央处理单元）**可以执行机器指令，进行运算。但是由于硬件设计和内部结构的不同，每一种CPU需要用不同的电平脉冲来控制。因此每一种CPU都有自己的机器指令集，也就是机器语言。不同系统的机器语言不互通，例如在WIN上，“关机”的机器语言是“10011101010”，在MACOS上，“关机”的机器语言是“10011101101”（只是假设，不代表两个系统真实的机器语言），机器语言不具备跨设备使用的能力。

早期的程序设计均使用机器语言。程序员们将用0, 1数字编成的程序代码打在纸带或卡片上，1打孔，0不打孔，再将程序通过纸带机或卡片机输入计算机，进行运算。但直接书写机器语言显然是很不友好的，随便一个操作都要写一长串“1001101010111...”，复杂又难读。

由此产生了**汇编语言**——定义一些指令与“0”和“1”二进制编码串互相映射，相当于助记符，让二进制编码变成人容易理解的样子，比如“inc eax”这条指令的机器码为40，即“0100 0000”等等。在执行时，机器还是会把指令转化为二进制去执行，只是借助指令的方式让人们更易理解。

虽然汇编语言让我们不必使用一长串“0”“1”来表示操作了，但使用汇编，一个简单操作需要的指令编码还是非常多。因此，Java、C++、Python、JavaScript等**高级语言**产生了。它们更接近自然语言，让我们的编码过程变得简单。但高级语言是方便我们理解了，计算机要如何理解呢？

### 解释型与编译型

将高级语言翻译为机器语言，通常有两种方式：**解释**与**编译**。

解释采用解释器，它通常在代码运行之前不需要做额外的操作，只需要在代码运行时**逐行**将代码翻译成机器语言即可。

而编译则是在代码运行之前，**一次性**生成代码的翻译产物，代码运行时只执行这份翻译后的产物。

根据解释和编译不同的特性，可以看出**解释型语言**和**编译型语言**有以下几个区别：

- 解释型不会产生中间产物，编译型会生成一份“中间产物”；
- 因为编译型语言在编译时会生成翻译后的可执行文件，在代码运行时只需要执行这份可执行文件即可，所以编译型语言可以脱离开发环境运行，不需要拥有源代码；
- 由于各系统的机器语言都不相同，编译型语言一般不具有跨平台性（**当然，也有一些编译型语言通过一些改造可以做到跨平台，比如JAVA，就是通过JAVA虚拟机将编译后的字节码转化为不同系统的机器码，但这些就不再赘述**）。而解释型语言可以做到“一次编写，处处运行”，在不同系统上通过解释器，可以将代码翻译为适配系统的机器语言。

### 各有千秋
#### 解释型语言的优势 & 劣势

解释器可以快速启动和运行。解释型语言不需要在代码运行前做额外的处理工作，只需翻译第一行，然后运行即可。

正因为如此，对于 JavaScript 这样的程序来说，解释器似乎是天作之合。对于web开发人员来说，能够**快速开始并运行代码**是非常重要的。这也是浏览器最初使用 JavaScript 解释器的原因。我们期望代码可以快速变为可视化的页面，比如调整样式时，如果每改动一个css调整都需要重新编译整个代码，这会是一个非常痛苦的过程。

但使用解释器的弊端在于，你需要多次运行相同的代码。例如下面的循环，你就必须一遍又一遍地对循环中的代码`sum += i`进行相同的10次翻译。

```js
for(let i = 0; i < 10; i ++) {
  sum += i
}
```

因此，**减少解释器的重复劳动**也是解释型语言进行优化的重点，之后我们的JIT便可以很好地提升这一部分。

#### 编译型语言的优势 & 劣势
编译器的取舍则恰恰相反。

由于编译器必须在开始时进行编译，因此启动时间会稍长一些。但循环中的代码运行速度更快，因为它不需要在每次通过循环时重复翻译。

另一个区别是，编译器有更多时间查看代码并对其进行编辑，从而使代码运行得更快。这些编辑称为**优化**。

解释器是在运行时进行工作的，因此在翻译阶段不可能花太多时间来找出这些优化。

### JavaScript引擎的演化

JavaScript 创建于 1995 年。一开始，它的设计初衷并没有要求高性能，因此运行速度并不是很快。

但在2008年，“性能之战”开始了。多种浏览器都添加了即时编译器（也称为 JIT）。在 JavaScript 运行时，JIT 可以发现模式，并根据这些模式加快代码的运行速度。JIT 的引入让 JavaScript 性能出现转折点——**JS 的执行速度提高了 10 倍**。

那么JS是如何在浏览器中运行的呢？JIT又是如何工作的？我们将在之后的文章中进一步探索。