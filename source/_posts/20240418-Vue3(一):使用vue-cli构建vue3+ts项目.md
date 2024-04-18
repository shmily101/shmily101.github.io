---
title: Vue3(一):使用vue-cli构建vue3+ts项目
date: 2024-04-18 06:57:23
tags: 
  - Vue
categories:
  - Vue3
---

## 前言

工作之后，除了一些老项目还在使用Vue2之外，搭建的新项目大多都采用vue3+ts，老项目能迁移重构的也都尽量使用了vue3+ts。vue3本身就是用ts编写的，对ts有极高的支持性，vue3改进的双向绑定、语法糖、composition api以及用于逻辑复用的Composition Function等等，和Vue2相比确实提升了不少开发体验。尤其是使用ts后让代码更加规范化了，正确使用ts还可以提升开发效率。这都是后话，我们先从搭建项目开始。

官方文档：https://cli.vuejs.org/zh/guide/creating-a-project.html#vue-create

## 安装vue-cli脚手架

Vue CLI 是官方提供的基于 **Webpack** 的 Vue 工具链，现已处于维护模式。现在官方推荐使用 **create-vue** 来创建基于 **Vite** 的新项目。但目前我还没有深入了解Vite，一般都是采用Vue CLI去创建Webpack项目。之后可以再对vite进行学习。

Vue CLI 的包名称由 vue-cli 改成了 @vue/cli。 如果你已经全局安装了旧版本的 vue-cli (1.x 或 2.x)，你需要先通过 npm uninstall vue-cli -g 或 yarn global remove vue-cli 卸载它。

```
## 安装或升级脚手架
npm install -g @vue/cli
## 确保 vue-cli 版本在 4.5.0 以上
vue -V
## 创建项目
vue create ts-demo
```

## 构建项目

##### (1)

安装完脚手架后，执行：

```
vue create [项目名]
```

去创建一个新的vue项目。

##### (2)

此时会提示我们选取一个preset。vue-cli有默认的vue2和vue3的模版，都是包含了基本的 **Babel + ESLint** 设置。我们也可以选择**“Manually select features”**来手动配置我们需要的特性。默认模板中没有包含ts，因此我们选择“Manually select features”手动配置。

```
Vue CLI v5.0.8
? Please pick a preset: (Use arrow keys)
❯ Default ([Vue 3] babel, eslint)  // 默认vue3模板，包含babel和eslint
  Default ([Vue 2] babel, eslint)   // 默认vue2模板，包含babel和eslint
  Manually select features   // 手动选择特性
```

##### (3)

接下来选择我们所需的选项来生成项目，注意使用 **空格键** 选中，**回车键** 确认。

这里我们将TypeScript、vue-router、vuex和css预处理器、Linter / Formatter选上。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection, and <enter> to proceed)
❯◉ Babel    // 使用babel编译器
 ◉ TypeScript   // 使用TypeScript编写代码
 ◯ Progressive Web App (PWA) Support    // 渐进式WEB应用
 ◉ Router  // 使用vue-router
 ◉ Vuex   // 使用vuex
 ◉ CSS Pre-processors    // 使用css预处理器
 ◉ Linter / Formatter    // 代码规范标准
 ◯ Unit Testing     // 单元测试
 ◯ E2E Testing    // e2e测试
```

##### (4)

选择vue版本。这里选择**3.x**。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, Router, Vuex, CSS Pre-processors, Linter
? Choose a version of Vue.js that you want to start the project with (Use arrow keys)
❯ 3.x 
  2.x 
```

##### (5)

选择是否使用Class风格装饰器。

**Class风格：**

即原本是：`home = new Vue()`创建vue实例

使用装饰器后：`class home extends Vue{}`

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, Router, Vuex, CSS Pre-processors, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Use class-style component syntax? (y/N) 
```

##### (6)

是否需要使用Babel与TypeScript一起用于自动检测的填充。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, Router, Vuex, CSS Pre-processors, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Use class-style component syntax? No
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? (Y/n) 
```

##### (7)

是否使用history作为路由模式。vue是单页应用，使用history需要做一些额外配置防止找不到路径出现404的问题。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, Router, Vuex, CSS Pre-processors, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Use class-style component syntax? No
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? Yes
? Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n) 
```

##### (8)

选择css预编译器。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, TS, Router, Vuex, CSS Pre-processors, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Use class-style component syntax? No
? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? Yes
? Use history mode for router? (Requires proper server setup for index fallback in production) No
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): (Use arrow keys)
❯ Sass/SCSS (with dart-sass) 
  Less 
  Stylus 
```

##### (9)

选择代码格式化检测。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Pick a linter / formatter config: (Use arrow keys)
❯ ESLint with error prevention only  // 只进行报错提醒
  ESLint + Airbnb config   // 不严谨模式
  ESLint + Standard config  // 正常模式
  ESLint + Prettier  // 严格模式

```

##### (10)

选择代码检查时机，分为保存时检查和提交时检查。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Pick a linter / formatter config: Basic
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection, and <enter> to proceed)
❯◉ Lint on save   // 保存时检查
 ◯ Lint and fix on commit     // commit时检查
```

##### (11)

选择一些配置是使用单独的文件配置，还是在**package.json**文件中配置。

```
Vue CLI v5.0.8
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Pick a linter / formatter config: Basic
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, ESLint, etc.? (Use arrow keys)
❯ In dedicated config files 
  In package.json 
```

##### (12)

是否保存上述配置，保存后下一次可直接根据上述配置生成项目。

```
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Choose a version of Vue.js that you want to start the project with 3.x
? Pick a linter / formatter config: Basic
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? (y/N) 
```

这样一个vue3+ts项目就创建完毕了。
