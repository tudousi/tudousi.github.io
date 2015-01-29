---
layout: post
title: IE display:inline-block 问题
tags: [htmlcss]
---

display:inline-block 布局时使用挺多的，但是在 IE8 以下，并不好用。可以设置宽度但，是有个问题就是无法成为内联块（不能并排）。

IE8 以下看下面的例子，分析下原因：
<iframe style="width: 100%; height: 100px" src="http://sandbox.runjs.cn/show/v8widfoi" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

问题就是在 IE8 以下，区块元素设置了 display:inline-block 仅仅是触发了 hasLayou，而元素本身还是区块元素。所以他们还是占一整行。（[hasLayout](http://www.blueidea.com/tech/site/2006/3698.asp)）。

我们已经知道问题，那么如何让 IE8 以下的浏览器也能实现 inline-block 捏。
```css
display:inline-block;
*+display:inline;
zoom:1;
```

*+display:inline; 让 IE8 以下的浏览器先转为内联元素，然后使用 zoom:1; 触发 hasLayou。这个兼容性还可以。
