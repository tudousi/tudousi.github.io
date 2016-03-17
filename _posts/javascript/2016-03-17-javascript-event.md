---
layout:     post
title:      javascript事件
summary:    web程序中事件是一种通信机制，鼠标事件和键盘事件是我们直观感受到的事件。javascript中事件有非常多中，常用的有键盘事件、鼠标事件、触摸事件和自定义事件，这里来了解一下事件流概念。
categories: javascript
tags:       [event 事件]
---

> web程序中事件是一种通信机制，鼠标事件和键盘事件是我们直观感受到的事件。javascript中[事件](https://developer.mozilla.org/zh-CN/docs/Web/API/Event)有非常多中，常用的有键盘事件、鼠标事件、触摸事件和自定义事件，这里来了解一下事件流概念。

关于javascript事件机制的概念可以参考阮一峰的[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

### 事件流(event flow)

在了解了事件机制的基本概念后，再来了解一下事件流。想象一下当用户点击了一个页面上的元素，事件是怎么样被触发的呢？因为网页中的元素可以被嵌套和叠加，所以事件不能只在当前元素触发事件。它要触发的肯定是这个元素在DOM树中所对应的这条路径上所有元素的对应事件。像这样的事件发生的路径和方法我们可以叫它 **事件流(event flow)**


### 事件发生时有3个阶段

1.  捕获阶段(Captrue Phase)

    在这个阶段的事件是从Window一直向下传递直到事件到达目标(点击的元素)的父元素(这个阶段所涉及的元素的事件被执行)

2.  目标阶段(Target Phase)

    当前事件处于目标(点击的元素)元素上，也就是target等于currentTarget时就是目标阶段。此后事件将从这个元素向父元素开始冒泡进。(在目标元素上的捕获和冒泡都会被执行)

3.  冒泡阶段(Bubbling Phase)

    从目标阶段元素的父元素开始依次开始向上冒泡事件，直到到达Window时结束。(这个阶段所涉及的元素的事件被执行)

![phase](/resource/event/phase.png)
<p class="tac">事件3个阶段</p>


### 为了对这3个阶段更进一步的理解，来看一下下面的例子

<a class="jsbin-embed" href="http://jsbin.com/yanubi/45/embed?html,js,output">event phase demo</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.11"></script>

如果你在非ie8-浏览器中点击e3元素会在控制台输出如下内容

```javascript
e1->捕获事件被执行 是否在目标阶段-> false
e2->捕获事件被执行 是否在目标阶段-> false
e3->不捕获事件被执行 是否在目标阶段-> true
e3->捕获事件被执行 是否在目标阶段-> true
e2->不捕获事件被执行 是否在目标阶段-> false
e1->不捕获事件被执行 是否在目标阶段-> false
```

从输出可以看出来，前面1、2、3是绑定在捕获阶段所执行的代码，而4、5、6是绑定在冒泡阶段所执行的代码，其中3、4事件也是同时处于目标阶段的。还有一个小点就是**ie8-是不支持事件捕获类型的**，所以你所绑定的捕获事件会在处于目标阶段执行。


### DOM0级是啥样？

还有一个地方值得注意的就是使用DOM0级给元素绑定事件会怎么样？这里我再写一个DEMO来展示一下。



<a class="jsbin-embed" href="http://jsbin.com/xodecuj/embed">event phase on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.11"></script>

点击e2元素，输出如下

```javascript
e1->捕获事件被执行 是否在目标阶段-> false
e2->捕获事件被执行 是否在目标阶段-> false
e3->不捕获事件被执行 是否在目标阶段-> true
e3->捕获事件被执行 是否在目标阶段-> true
e2->不捕获事件被执行 是否在目标阶段-> false
DOM0级绑定事件执行 e2
e1->不捕获事件被执行 是否在目标阶段-> false
```

从例子输出可以看得出来，DOM0级绑定并不会对DOM2级的绑定在执行上造成冲突或覆盖什么的。那么他们有什么不同？这里我列举了几个不同的地方

1. 没有事件捕获，它是直接从当前元素开始冒泡
2. 没有当前元素(currentTarget)，只有目标元素(target)，这就意味着它无法完成事件代理的工作

<a class="jsbin-embed" href="http://jsbin.com/newuhi/embed">event phase 2 on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.11"></script>

上面是传统DOM0级绑定方法。这种方法现在已经就不用了。


### 事件终止

w3c标准提供了`stopPropagation`函数来终止事件捕获和冒泡，注意处于目标的捕获和非捕获的事件都会被执行。另外ie使用`window.event.cancelBubble = true;`来终止事件冒泡。注意使用DOM0级方式绑定的事件同样可以终止事件的捕获和冒泡


### 参考文档

[https://developer.mozilla.org/zh-CN/docs/Web/API/Event](https://developer.mozilla.org/zh-CN/docs/Web/API/Event)

[https://www.w3.org/TR/DOM-Level-3-Events/#event-flow](https://www.w3.org/TR/DOM-Level-3-Events/#event-flow)

### 其他

接下来会了解一下事件的类型、事件参数以及自定义事件。还会再分析一下JQuery是如何对事件进行处理的。 毕竟土豆也还很菜有啥问题和错误请告诉土豆哦 :)
