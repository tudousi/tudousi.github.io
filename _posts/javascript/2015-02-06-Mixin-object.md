---
layout: post
title: 使用Mixin方法进行对象扩展
tags: [javascript]
---

javascript 中共享函数可以通过继承prototype来实现，或者是类继承。

但是有的时候我们不想把函数定义到类中让所有的类都共享，或者每个子类都实现一次功能函数。我们可以使用Mixin（混入、掺元）把功能函数复制到想要的类中。

Mixin类，该类可以定义一些通用的方法，那些类需要就直接调用我们的复制（aument）函数进行复制

```javascript
// Minxin
var Minxin = function(){};
Minxin.prototype = {
    serialize: function(){
        var output = [];
        for(var key in this){
            if(typeof this[key] !== 'function'){
                output.push(key + ': ' + this[key]);
            }
        }
        return output.join(',');
    },
    getName: function(){
        return 'jiangzhu';
    },
    getAge: function(){
        return 22;
    }
}
```

aument 函数，这个函数负责复制Mixin中的方法到指定的类中.
这个函数接受2个以上是参数。
第一个参数是要复制到的类，第二个参数是被复制类(Mixin)
默认是复制参数二(givingClass)的所有函数，如果传递第三个参数则复制指定的函数

```javascript
// Augment 
function augment(receivingClass, givingClass) {
    if(arguments[2]){   // 如果传递3个或以上的参数则复制它们
        for(var i = 2, len = arguments.length; i < len; i++) {
            var argValue = arguments[i];
            if(givingClass.prototype[argValue]) {
                receivingClass.prototype[argValue] = givingClass.prototype[argValue];
            }
        }
    } else {
        for(var methodName in givingClass.prototype) {
            if(!receivingClass.prototype[methodName]) {
                receivingClass.prototype[methodName] = givingClass.prototype[methodName];
            }
        }
    }
}
```
我们申明一个Author用作测试

```javascript
// Author 类
function Author(name, books){
        this.name = name;
        this.books = books;
}
```

复制Mixin的方法到Author的实例对象中,调用复制的方法。

```javascript
augment(Author, Minxin, 'serialize');
var author = new Author('Ross Harmes', ['js','c#']);
debugger;
console.log(author.serialize());
```





