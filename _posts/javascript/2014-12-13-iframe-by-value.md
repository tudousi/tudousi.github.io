---
layout: post
title: 页面中多 iframe 传值
tags: [javascript]
---

### 关于 iframe 传值
> 如果一个页面中有多个 iframe 如何做到互相调用函数和传值。js 默认自带 只能子页面调用和修改父页面的函数或变量。没有 iframe 之间的调用，那么就要想一些办法

#### 办法1：

当 iframe 页面加载的时候在 window 保存在父页面的变量中

父页面：

```javascript
var iframeObj = {};
```
子页面（iframe）

```javascript
window.onload = function(){
    top.window.iframeObj['iframe1'] = window;
};
```
如果我们要在一个 iframe 中调用另外一个 iframe 的时候可以这样做

```javascript
var iframe2 = top.window.iframeObj['iframe2'];
iframe2.info();
```
#### 实例
<iframe  frameborder="0" scrolling="no" src="/resource/iframebyvalue/demo1.html" width="600" height="200"></iframe>
 
<br>
#### 方法2
在子页面（iframe）里面监听 hash 的改变，通过 hash 来达到传值的目的

子页面（iframe）

```javascript
var hash = location.hash;
window.onload = function(){
    setInterval(function(){
        var tmpHash = location.hash;
        if(hash != tmpHash && tmpHash != ''){
            hash = tmpHash;
            var call = hash.slice(1, hash.length);
            try{
                eval(call+"()");
            }catch(e){

            }
        }
    }, 100);
};
function callInfo(){
    var iframe4 = top.window.document
    iframe4 = iframe4.getElementById('iframe4')['contentDocument'];
    iframe4.location.hash = '#callInfoRun';
}
function callInfoRun(){
    alert('我是页面 3 的 callInfo 方法');
}
```
#### 实例
<iframe  frameborder="0" scrolling="no" src="/resource/iframebyvalue/demo2.html" width="600" height="200"></iframe>
 
 测试代码下载 [点击下载](/resource/iframebyvalue/iframe.zip)
 
 * 如果你还有其他的方法请加 QQ 群讨论吧 239147101
 