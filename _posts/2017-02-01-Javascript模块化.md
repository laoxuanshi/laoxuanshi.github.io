---
layout: post
title: Javascript模块化
categories: [前端开发]
description: some word here
keywords: Javascript模块化
---

### AMD规范
代表是require.js，
require()异步加载模块，它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。<br>
![](/images/web/w15.png)
<br>

define方法用于定义模块，此模块必须返回一个对象，供其他模块调用
<br>
![](/images/web/w16.png)
<br>
require.js提供了一个优化工具optimizer，当模块部署完毕以后，可以用这个工具将多个模块合并在一个文件中，减少HTTP请求数。


### CommonJS
代表是Node.js。
在每个模块内部，module变量代表当前模块。它的exports属性是对外的接口，通过module.exports将模块的接口暴露出去。其他文件通过 require() 来导入其他模块的输出到当前模块作用域中。实际上就是读取module.exports变量。
### AMD和CommonJS对比
- CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。
- AMD规范则是异步加载模块，允许指定回调函数，在回调函数中执行操作。
- 由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以CommonJS规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范。

### ES6的模块化

使用export关键字将任意变量、函数或者类公开给其他模块。一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。
其他 JS 文件就可以通过import命令加载这个模块。
import和export命令只能在模块的顶层，不能在代码块之中。

### CommonJS和ES6模块化的对比
- CommonJS 模块输出的是一个值的拷贝：一旦输出一个值，模块内部的变化就影响不到获取的这个值。
ES6 模块输出的是值的引用：JS 引擎对脚本静态分析的时候，遇到import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。原始值变了，import加载的值也会跟着变
- CommonJS 模块是运行时生成，ES6 模块是编译时生成。
第二个差异是因为 CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

