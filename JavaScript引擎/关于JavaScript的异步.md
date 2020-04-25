# 关于JavaScript的异步

## 引子

问题是从浏览器扩展的`chrome.runtime.onInstalled.addListener(function callback)`开始的。  
最初的理解是这是一个扩展运行的一个什么周期于是对比**spring**的生命。  
后续学习感觉与生命周期的概念还是不尽相同。  
还是把他当做一个单纯一些的异步函数来研究比较合适。  
这边对所学的spring生命周期及JavaScript异步的原理进行一个记录。  

- 对于闭包先不进行深入学习。

## Spring Bean 生命周期

[Spring 了解Bean的一生(生命周期)](https://blog.csdn.net/w_linux/article/details/80086950?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-4)

[Spring Bean的生命周期（非常详细）](https://www.cnblogs.com/zrtqsk/p/3735273.html)

[深究Spring中Bean的生命周期](https://www.cnblogs.com/javazhiyin/p/10905294.html)

主要大概看了了上述几个文章，结合之前的一些记忆，大概是预留的一些借口，接上接口后实现其中的几个方法，将要在哪个生命周期中实现的内容、方法写在对于接口方法中即可。主函数那边应该是按顺序调用。

## JavaScript异步函数

### 发展脉络

> 1. 回调函数  
> 
> 优点：简单、容易理解和部署。  
> 缺点：不利于代码的阅读和维护，各个部分之间高度耦合（ Coupling ），流程会很混乱。
> 
> 1. Promise对象
> 
> ![Promise对象示例图](https://pics4.baidu.com/feed/72f082025aafa40ffd6105ccde94cb4b79f01975.jpeg?token=ca08273304b97bbb1f079bc3c4fd60d2&s=0C98ED1209D86CC80EDDC5DE0000D0B1)
> 一个 promise 可能有三种状态：等待（ pending ）、已完成（ fulfilled ）、已拒绝（ rejected ）;  
> resolve，接受一个成功值，传递给绑定的fulfilled回调函数中。主要工作是将当前状态变为 fulfilled 状态，同时调用绑定的 fulfilled 回调函数。  
> reject，接受一个失败信息，传递给绑定的rejected回调函数中。主要工作是将当前状态变为 rejected 状态，同时调用绑定的 rejected 回调函数。  
> then方法返回一个Promise。它有两个参数，分别为 Promise 在成功和失败情况下的回调函数。  
> 语法：
> ！[Promise语法示例](https://pics3.baidu.com/feed/dc54564e9258d10962760abda7a804bb6d814d19.jpeg?token=3d55d771aa3792b50b2603d9662fe9f7&s=59A83C72953044231C75E8DE0000C0B3)
> 概括来说 promise 是对异步的执行结果的描述对象。
> 
> 1. Generator
> 
> Generator 函数是 ES6 提供的一种异步编程解决方案 ，允许函数的暂停和恢复。  
> 异步任务的封装：  
> ![异步任务的封装](https://pics1.baidu.com/feed/a044ad345982b2b751bdbddb445d03eb77099b80.jpeg?token=528b67eb6f61d06dcbf8d5787c676dc2&s=4D00ED1201D84DC8187C01DA000050B2)
> 整个过程类似于，浏览器遇到标识符 * 之后，就明白这个函数是生成器函数，一旦遇到 yield 标识符，就会将以后的函数放入此异步函数之内，待异步返回结果后再进行执行。  

### 异步的实现原理

>Javascript 单线程指的是在一个浏览器进程中只存在一个 Javascript 执行线程，所以任务需要顺序排列等待执行，而***不能像 Java 等多线程语言一样并发执行***。但是这种单线程模型在处理耗时的异步任务是会出现较长时间的线程阻塞，导致后续的任务不能被及时处理。

**浏览器只给javascript的执行分配了一个线程**，因此它是单线程的，并不能并发执行。
javascript的异步处理方法并不是真正的异步，javascript异步的实现是靠**事件驱动**来实现的。  

