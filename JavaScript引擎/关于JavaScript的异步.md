# 关于JavaScript的异步

## 引子

问题是从浏览器扩展的 `chrome.runtime.onInstalled.addListener(function callback)` 开始的。  
最初的理解是这是一个扩展运行的一个什么周期于是对比**spring**的生命。  
后续学习感觉与生命周期的概念还是不尽相同。  
还是把他当做一个单纯一些的异步函数来研究比较合适。  
这边对所学的spring生命周期及JavaScript异步的原理进行一个记录。  

* 对于[闭包][闭包]先不进行深入学习, 在这篇[《搞懂JavaScript引擎运行原理》][搞懂JavaScript引擎运行原理]文章中简单涉及到了。

## Spring Bean 生命周期

[《Spring 了解Bean的一生(生命周期)》][Spring 了解Bean的一生(生命周期)]

[《Spring Bean的生命周期（非常详细）》][Spring Bean的生命周期（非常详细）]

[《深究Spring中Bean的生命周期》][深究Spring中Bean的生命周期]

主要大概看了了上述几个文章，结合之前的一些记忆，大概是预留的一些借口，接上接口后实现其中的几个方法，将要在哪个生命周期中实现的内容、方法写在对于接口方法中即可。主函数那边应该是按顺序调用。

## JavaScript异步函数

### 发展脉络

> 1. 回调函数  
> 优点：简单、容易理解和部署。  
> 缺点：不利于代码的阅读和维护，各个部分之间高度耦合（ Coupling ），流程会很混乱。
> 1. Promise对象
> ![Promise对象示例图][Promise对象示例图]
> 一个 promise 可能有三种状态：等待（ pending ）、已完成（ fulfilled ）、已拒绝（ rejected ）; 
> resolve，接受一个成功值，传递给绑定的fulfilled回调函数中。主要工作是将当前状态变为 fulfilled 状态，同时调用绑定的 fulfilled 回调函数。  
> reject，接受一个失败信息，传递给绑定的rejected回调函数中。主要工作是将当前状态变为 rejected 状态，同时调用绑定的 rejected 回调函数。  
> then方法返回一个Promise。它有两个参数，分别为 Promise 在成功和失败情况下的回调函数。  
> 语法：
> ![Promise语法示例][Promise语法示例]
> 概括来说 promise 是对异步的执行结果的描述对象。
> 1. Generator  
> Generator 函数是 ES6 提供的一种异步编程解决方案 ，允许函数的暂停和恢复。  
> 异步任务的封装：  
> ![异步任务的封装][异步任务的封装]
> 整个过程类似于，浏览器遇到标识符 * 之后，就明白这个函数是生成器函数，一旦遇到 yield 标识符，就会将以后的函数放入此异步函数之内，待异步返回结果后再进行执行。  

### 异步的实现原理

> Javascript 单线程指的是在一个浏览器进程中只存在一个 Javascript 执行线程，所以任务需要顺序排列等待执行，而***不能像 Java 等多线程语言一样并发执行***。但是这种单线程模型在处理耗时的异步任务是会出现较长时间的线程阻塞，导致后续的任务不能被及时处理。
>
> **浏览器只给javascript的执行分配了一个线程**，因此它是单线程的，并不能并发执行。
> javascript的异步处理方法并不是真正的异步，javascript异步的实现是靠**事件驱动**来实现的。  

#### 关于为什么浏览器不多分配几个线程给JavaScript呢?

[《js-关于异步原理的理解和总结》][js-关于异步原理的理解和总结]一文中举了两个例子验证了JavaScript引擎是单线程运行的以及JavaScript引擎用单线程运行也是有意义的。

> JavaScript引擎是单线程运行的, 浏览器无论在什么时候都只且只有一个线程在运行JavaScript程序.  
> JavaScript引擎用单线程运行也是有意义的, 单线程不必理会线程同步这些复杂的问题,问题得到简化。

同时：

> [一个浏览器通常由以下几个常驻的线程](https://zhuanlan.zhihu.com/p/64736448):
>
> * 渲染引擎线程：顾名思义，该线程负责页面的渲染
> * JS引擎线程：负责JS的解析和执行
> * 定时触发器线程：处理定时事件，比如setTimeout, setInterval
> * 事件触发线程：处理DOM事件
> * 异步http请求线程：处理http请求  
>
> 渲染线程和JS引擎线程是不能同时进行的。渲染线程在执行任务的时候，JS引擎线程会被挂起。因为JS可以操作DOM，若在渲染中JS处理了DOM，浏览器可能就不知所措了。  
>浏览器在运行时只开启了一个JS引擎线程来解析和执行JS。那为什么只有一个引擎呢？如果***同时有两个线程去操作DOM，浏览器是不是又要不知所措了***。  
>所以，虽然JavaScript是单线程的，可是浏览器内部不是单线程的。一些I/O操作、定时器的计时和事件监听（click, keydown...）等都是由浏览器提供的其他线程来完成的。

#### 浏览器端对JavaScript的异步处理

原文[《Javascript 异步实现机制》](https://www.cnblogs.com/tianheila/p/6420587.html)中，从浏览器与NodeJS中分别分析总结了JavaScript的异步实现机制。

> 但凡 ***“即是单线程又是异步 ”*** 的语言都有一个共同的特点：它们是 event-driven 的，所以 Javascript 异步的实现也与其事件机制关系密切。

安装该篇文章的介绍，浏览器实现了两个重要的API

* 定时器 `setTimeout(function callback(),time)` 

  上文说到浏览器只有一个JavaScript执行线程，所以若由JavaScript执行线程来进行计时的话，那么一定会出现执行线程阻塞。所以这部是有另外一个线程进行计时的，然后在计时完成后将回调函数推入JavaScript的***事件队列***中。
  关于事件队列下文详细分析。  
  另，定时器可以说是“至少执行那么长时间后再执行回调函数”主要是因为，事件队列是在当前栈空了之后，即主线程空闲之后才会访问事件队列进行执行。

* AJAX请求

  这里的话是实现了一个

> 突然明悟一个事，React是一个基于nodejs的。虽然是前端，但是他也是属于，启动nodejs服务器，然后将数据在nodejs层组装好，或者说不是数据，是HTML页面框架，解析JavaScript语言，生成HTML，然后发往前端。  所以我们在访问一个前后端分离的框架的时候，由浏览器，向nodejs服务器发出请求，返回HTML页面。  
> 数据是怎么走的呢。。。应该是nodejs会把页面构造那部分的js处理掉，一些关于数据请求的，异步加载的js一并发给浏览器，让浏览器对后端服务器进行请求，再由浏览器对动态页面进行处理。

[闭包]:https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures

[搞懂JavaScript引擎运行原理]:https://segmentfault.com/a/1190000019530109

[Spring 了解Bean的一生(生命周期)]:https://blog.csdn.net/w_linux/article/details/80086950?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4

[Spring Bean的生命周期（非常详细）]:https://www.cnblogs.com/zrtqsk/p/3735273.html

[深究Spring中Bean的生命周期]:https://www.cnblogs.com/javazhiyin/p/10905294.html

[Promise对象示例图]:https://pics4.baidu.com/feed/72f082025aafa40ffd6105ccde94cb4b79f01975.jpeg?token=ca08273304b97bbb1f079bc3c4fd60d2&s=0C98ED1209D86CC80EDDC5DE0000D0B1

[Promise语法示例]:https://pics3.baidu.com/feed/dc54564e9258d10962760abda7a804bb6d814d19.jpeg?token=3d55d771aa3792b50b2603d9662fe9f7&s=59A83C72953044231C75E8DE0000C0B3

[异步任务的封装]:https://pics1.baidu.com/feed/a044ad345982b2b751bdbddb445d03eb77099b80.jpeg?token=528b67eb6f61d06dcbf8d5787c676dc2&s=4D00ED1201D84DC8187C01DA000050B2

[js-关于异步原理的理解和总结]:https://blog.csdn.net/qdq2014/article/details/72383725?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1
