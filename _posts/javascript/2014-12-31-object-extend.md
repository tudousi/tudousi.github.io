---
layout:     post
title:      javascript 对象继承基础
summary:    JavaScript 是基于原型的面向对象语言，并不像 JAVA、C++ 这种基于对象的语言。如果要理解 JavaScript 中的对象，理解原型 (prototype) 和构造函数 (constructor) 是非常重要的。
categories: javascript
tags:       [javascript继承,javascript对象]
---

### JavaScript 对象基础和概念
> JavaScript 是基于原型的面向对象语言，并不像 JAVA、C++ 这种基于对象的语言。如果要理解 JavaScript 中的对象，理解原型 (prototype) 和构造函数 (constructor) 是非常重要的。

* 构造函数 (constructor)
* 原型对象 (prototype)

JavaScript 中的构造函数不同于其他基于对象的语言，它是一个函数，比如： function Class(){} 这就是构造函数，可以看下 js 中的构造函数和其他语言的构造函数的区别

{% highlight javascript %}
// javascript
function Person(){}
{% endhighlight %}

{% highlight java %}
// java
class Person {
    public Person(){}
}
{% endhighlight %}

{% highlight c++ %}
// c++
class Person {
    public:
        Person(){}
}
{% endhighlight %}

{% highlight php %}
// php
class Person {
    function __construct(){}
}
{% endhighlight %}

好了扯得有点远，js 中的构造函数 (constructor) 是用来初始化对象的。

所有的构造函 (constructor) 数都有一个 prototype 属性，它就是原型。js 就是靠他实现继承的。

我们现在把构造函数拿来分析下:<br>
<img src="/resource/javascript-extend/javascript-extend-1.png" />

* 编号 1 就是每个构造函数的原型(prototype)
* 编号 2 每个原型中都有一个属性 construct(构造函数)
* 编号 3 只要是对象实例就会有一个 \_\_proto\_\_ 属性它指向构造函数所继承的原型的引用(prototype)。


#### 原型链 (prototype chain)
\_\_proto\_\_ 是原型链实现的关键，每个对象实例都包含一个 \_\_proto\_\_ 属性，它指向构造函数 (constructor) 属性的引用。<br>
<img src="/resource/javascript-extend/javascript-extend-2.png" />

下面的代码证明了 p 的对象实例属性 \_\_proto\_\_ 是指向构造函数 Person 的原型 (prototype)

{% highlight javascript %}
function Person(){}
var p = new Person();
console.log(p.__proto__ === Person.prototype);  // true
{% endhighlight %}

#### 函数 (function) 对象的原型链
在JavaScript中，函数（function）是一个特殊的对象，所有函数都是构造函数Function的实例，所以，函数的原型链与new操作符实例化对象的原型链会不同，先看下面代码：

{% highlight javascript %}
function Person() {}
alert(Person.__proto__ === Object.prototype); // false
alert(Person.__proto__ === Function.prototype); // true
{% endhighlight %}
在 js 中函数 (function) 是一个特殊的对象，所有的函数 (function) 都是构造函数 (Function) 的实例。

对于上面的 Person 函数现在来看下它的原型链是怎么样的

Person -> \_\_proto\_\_ -> Function.prototype -> \_\_proto\_\_ -> Object.prototype -> \_\_proto\_\_ -> null

javascript 对象结构图<br>
<img src="/resource/javascript-extend/javascript-extend-3.png" />


#### javascript 对象实例

* 一个基本类
这里的 this 指向的是 new 出来的对象实例，所以构造函数会给对象实例添加一系列初始化的属性

{% highlight javascript %}
// 版本1 构造函数初始化属性
function Person(name, age){
    this.name = name;
    this.age = age;
    this.talk = function(){
        console.log('hello my name is:' + this.name);
    };
}
var td = new Person('tudou', 22);
td.talk();
// 版本2 在原型是添加属性和方法
function Person(name, age){
    this.name = name;
    this.age = age;
}
Person.prototype.talk = function(){
    console.log('hello my name is:' + this.name);
};
var td = new Person('tudou', 22);
td.talk();
{% endhighlight %}

基本类讲完了，来看下在 js 中如何实现继承的，看下面代码：

{% highlight javascript %}
function Person(name, age){
    this.name = name;
    this.age = age;
    console.log('Person constructor');
}
Person.prototype.talk = function(){
    console.log('hello my name is:' + this.name);
};
function Programmer(){
    // 调用父类构造函数来初始化 Programmer 的属性
    Person.apply(this, arguments);
    console.log('Programmer constructor');
}
// 简单粗暴的把 Programmer 对象的原型属性指向 Person 的原型
Programmer.prototype = Person.prototype;
Programmer.prototype.talk = function(){
    console.log('i age is:' + this.age);
};
var p = new Person('tudou', 22);
var td = new Programmer('tudou', 22);
p.talk();   // i age is:22
td.talk();  // i age is:22
{% endhighlight %}

上面代码是一种暴力继承，这样做不是最佳方法，作为一个君子，此方法不可用。有一个坏处：Programmer.talk 会覆盖 Person.talk。

针对上面的优化继承方案

{% highlight javascript %}
// 原型继承修改为
Programmer.prototype = new Person();
{% endhighlight %}
这样做的好处就是 Programmer.prototype 继承的是一个 Person 对象的实例，修改 Programmer.prototype 并不会影响到 Perons

{% highlight javascript %}
// 优化继承
function Person(name, age){
    this.name = name;
    this.age = age;
    console.log('Person constructor');
}
Person.prototype.talk = function(){
    console.log('hello my name is:' + this.name);
};
function Programmer(){
    // 调用父类构造函数来初始化 Programmer 的属性
    Person.apply(this, arguments);
    console.log('Programmer constructor');
}
// 优化原型继承
Programmer.prototype = new Person();
Programmer.prototype.talk = function(){
    console.log('i age is:' + this.age);
};
var p = new Person('tudou', 22);
var td = new Programmer('tudou', 22);
p.talk();   // hello my name is:tudou
td.talk();  // i age is:22
{% endhighlight %}

下面的继承其实没有什么问题，但是给 Programmer.prototype 赋值的时候会实例化 Person ，如果我们只想继承 Perons.prototype 就的使用一个空的对象作为代理，在把 Programmer.prototype 指向这个代理对象

{% highlight javascript %}
function Person(){
    console.log('Person constructor');
}
Person.prototype.talk = function(){
    console.log('hello my name is tudou');
};
function Programmer(){
    console.log('Programmer constructor');
}
// 再次优化继承
function Empey(){}
Empey.prototype = Person.prototype;
Programmer.prototype = new Empey();
Programmer.prototype.talk = function(){
    console.log('i age is 22');
};
var p = new Person();
var td = new Programmer();
p.talk();
td.talk();
{% endhighlight %}
通过 Empty 这一层对象实例作为了中转，在修改 Programmer.prototype 的属性的时候就不会影响到 Person.prototype 了

写一个继承方法

{% highlight javascript %}
(function(){
    function Person(){
        console.log('Person constructor');
    }
    Person.prototype.talk = function(){
        console.log('hello my name is tudou');
    };
    function Programmer(){
        console.log('Programmer constructor');
    }
    // 使用 extend 方法继承
    Programmer.prototype = extend(Person.prototype);
    var p = new Person();
    var td = new Programmer();
    p.talk();   // hello my name is tudou
    td.talk();  // hello my name is tudou
})();

function extend(extendObj){
    var tmpObject = function(){};
    tmpObject.prototype = extendObj;
    var res = new tmpObject();
    tmpObject.prototype = null;
    return res;
}
{% endhighlight %}

总结：
    每个对象都有__proto__属性它是一个对象，它指向父对象的prototype属性。父对象的prototype属性有constructor和__proto__，其中constructor指向其构造函数，而__proto__则再次指向其父对象的prototype属性。一直到Object
