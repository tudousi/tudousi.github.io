---
layout:     post
title:      javascript面向对象
summary:    从面向对象特点、javascript 如何面向对象、各种面向对象实现分析、面向对象的组件设计四个方面来看javascript 面向对象编程
categories: javascript
tags:       [javascript oo]
---

开头要啥bb的话。土豆不是装逼之人，也不反对装逼，但是逼乎不要太过分了。以前的[文章](http://createthink.net/javascript/2014/12/31/object-extend/)有点老了内容涵盖也挺少的就重新写一篇吧。

> 我是不懂面向对象编程，我也不懂原型链，我只是切图的。那我这个切图的就出来说说javascript oo 这些事吧

所涵盖内容：

1、面向对象特点

2、javascript 如何面向对象

3、各种面向对象实现分析

4、面向对象的组件设计

<div style="background:#ccc;height:2px;margin:10px;0;text-align:center;"></div>

### 1、面向对象特点：
抽象、继承、封装、多态，面向对象的特性大家应该都知道，思想很明显一切都是对象的思想来编程，对象有自己的方法和属性，对象也有扩展的方法（接口），面向对象前3中特性在javascript中都适用，
唯独多态性在javascript是比较蹩脚的，但是也可以实现。关于面向对象的文章太多太多了，总之面向对象编程就是用生活中习惯的方式写出计算机所能识别的代码。

### 2、javascript 如何面向对象

内容较多啊，建议泡杯咖啡、茶或者其他什么的边喝边看，我也选了一首节奏感比较强的歌，也可以边听歌边看
<embed src="http://www.xiami.com/widget/0_1771155385/singlePlayer.swf" type="application/x-shockwave-flash" width="257" height="33" wmode="transparent">

先从基础开始说起，那就是对象在javascript所存在的形态，它有2种存在的形态。1：对象字面量创建的对象 2、构造函数所创建的对象。看下面代码就知道了
{% highlight javascript %}
// 1对象字面量形态
var obj1 = {};
// 2构造函数形态
function Obj2() {}
var obj2 = new Obj2();
// 创建对象完毕后，就可以给对象添加属性和方法了。
obj1.name = 'tudou';
obj1.speak = function() {
	console.log("it's fuck oo");
}
obj1.speak();
{% endhighlight %}

上面的例子只是javascript中对象的书写形式，但是对象方法或者属性是具有继承性的，比如你继承你父亲的财产肯定要比你自己去赚取来的快且方便。下面就来看看javascript如何继承以及各个框架或者库是如何实现继承的吧。

### 3、各种面向对象实现分析

javascript实现继承最核心的就是原型链继承，知道了原型链继承后，其他的继承也就比较好理解。

先来创建一个电脑的类，根据这个电脑类可以派生出不同类型的电脑对象。然后根据这个例子来分析原型链继承。

{% highlight javascript %}
/**
* Computer	电脑类，它有构造函数用于创建电脑的基本信息，还有电脑的基本通用方法。比如执行命令、获取品牌名称、获取内存大小等
* brand 	电脑品牌
* ram 		内存
* cpu 		cpu型号
* disk 		硬盘大小
****/
function Computer(brand, ram, cpu, disk){
	this.brand = brand;
	this.ram = ram;
	this.cpu = cpu;
	this.disk = disk;
	// 执行命令
	this.runCommand = function(command) {
		console.log(command)
	};
	// 返回品牌名称
	this.getBrand = function() {
		return this.brand;
	};
	// 返回内存大小
	this.getRam = function() {
		return this.ram;
	};
	console.log('你好，欢迎使用' + brand + '电脑');
}
// 给用户推送消息
Computer.prototype.msg = function(msg) {
	console.log(msg);
}
// 创建一台联想电脑
var computer = new Computer('lenovo', '8', 'i7', '250');
computer.msg('你的内存大小为：' + computer.getRam() + 'G');
/***
---console.log---
// 你的内存大小为：8G
// 你好，欢迎使用lenovo电脑
****/
{% endhighlight %}

大家注意到了吗？上面的getBrand和getRam方法我写在了Computer的构造函数中，而msg我是写在了原型链中。

> 什么是原型链：每个函数都有一个对象属性prototype(原型)，对象的继承原理可以理解成就是一个一个prototype(原型)串联起来的。
所以大家也就叫原型链了吧。prototype有3个基本属性：1、constructor指向函数的构造函数。 2、\_\_proto\_\_指向该函数的父级prototype。 3、用户自定义的属性

来观察一下computer这个类所创建出来的对象在浏览器中是什么样子的。

![javascriptoo](/resource/javascriptoo/1.png)

恩，大家应该注意到了吧，只有绿色箭头的msg函数没有在computer对象的实例中，它是存在了Computer函数的prototype属性中。

恩，上面的代码是面向对象了，也有很好的封装性，但是万一有天我需要创建dell、acer、hp等电脑该怎么办？不可能都在这个一个类里添加修改类方法吧。所以需要基于这个Computer类来创建不同品牌的电脑类。

下面修改下代码不直接使用基类Computer来创建联想电脑，而是基于继承机制，重新创建一个专属于联想的电脑类，代码如下

{% highlight javascript %}
/**
* Computer	电脑类，它有构造函数用于创建电脑的基本信息，还有电脑的基本通用方法。比如执行命令、获取品牌名称、获取内存大小等
* brand 	电脑品牌
* ram 		内存
* cpu 		cpu型号
* disk 		硬盘大小
****/
function Computer(brand, ram, cpu, disk){
	this.brand = brand;
	this.ram = ram;
	this.cpu = cpu;
	this.disk = disk;
	// 执行命令
	this.runCommand = function(command) {
		console.log(command)
	};
	// 返回品牌名称
	this.getBrand = function() {
		return this.brand;
	};
	// 返回内存大小
	this.getRam = function() {
		return this.ram;
	};
	console.log('你好，欢迎使用' + brand + '电脑');
}
// 给用户推送消息
Computer.prototype.msg = function(msg) {
	console.log(msg);
}

// 创建一台联想电脑
// 现在我需要一台联想电脑，那么不需要自己重新开始创建电脑的所以属性和方法，只需要继承Computer就行了
function Lenovo(ram, cpu, disk) {
	Computer.call(this, 'lenovo', ram, cpu, disk);
}
Lenovo.prototype = Computer.prototype;
var lenovo = new Lenovo('8', 'i7', '500');
lenovo.msg('你的内存大小为：' + lenovo.getRam() + 'G');
{% endhighlight %}

在前面这个例子中Lenovo类实现了继承Computer类。当你创建Lenovo对象实例后去调用Lenovo对象不存在的方法时，他会根据原型链一直向上查找直到原型链为null为止。如下图

因为 Lenovo.prototype = Computer.prototype 这句代码，让Lenovo的原型链指向了Computer的原型链，所以它们才有了继承关系。

![javascriptoo](/resource/javascriptoo/2.png)

关于继承的好坏或者是否调用构造函数继承等可以去看以前的[文章](http://createthink.net/javascript/2014/12/31/object-extend/)

现在我们来看下各种库或者框架是如何实现继承的，那些优点和缺点。

首先来看看js大神John Resig的[继承](http://ejohn.org/blog/simple-javascript-inheritance/)方法，用john的继承方法重写的电脑类
{% highlight javascript %}
var Computer = Class.extend({
	init: function(brand, ram, cpu, disk) {
		this.brand = brand;
		this.ram = ram;
		this.cpu = cpu;
		this.disk = disk;
		console.log('你好，欢迎使用' + brand + '电脑');
	},
	// 执行命令
	runCommand: function(command) {
		console.log(command)
	},
	// 返回品牌名称
	getBrand: function() {
		return this.brand;
	},
	// 返回内存大小
	getRam: function() {
		return this.ram;
	},
	msg: function(msg) {
		console.log(msg);
	}
});

var Lenovo = Computer.extend({
	init: function(ram, cpu, disk) {
		this.\_super('lenovo', ram, cpu, disk);
	}
});

var lenovo = new Lenovo('8', 'i7', '500');
lenovo.msg('你的内存大小为：' + lenovo.getRam() + 'G');
{% endhighlight %}

分析他的实现思路。
{% highlight javascript %}
// Inspired by base2 and Prototype
(function() {
    var initializing = false,
        fnTest = /xyz/.test(function() {
            xyz;
        }) ? /\b_super\b/ : /.*/;

    // The base Class implementation (does nothing)
    // 基类提供全局Class方法
    this.Class = function() {};

    // Create a new Class that inherits from this class
    // 提供Class扩展方法，这里只能是单继承
    Class.extend = function(prop) {
        // 保存父级类的原型
        var _super = this.prototype;

        // Instantiate a base class (but only create the instance,
        // don't run the init constructor)
        initializing = true;
        // 创建父类对象用于继承，这里并不会调用init构造函数
        var prototype = new this();
        // 创建完毕父类对象后可以调用init构造函数
        initializing = false;

        // Copy the properties over onto the new prototype
        // 拷贝所有属性到创建好的父类对象中
        for (var name in prop) {
            // Check if we're overwriting an existing function
            // 这里就是拷贝传递进来的对象的。
            // 如果是属性直接拷贝
            // 如果是函数分2种处理。如果是普通函数也是直接拷贝
            // 如果是函数中调用_super方法，则需要进行代理
            prototype[name] = typeof prop[name] == "function" &&
                typeof _super[name] == "function" && fnTest.test(prop[name]) ?
                (function(name, fn) {
                    // 如果函数中有_super方法返回的是代理函数
                    return function() {
                        // 保存当前对象的_super函数
                        var tmp = this._super;

                        // Add a new ._super() method that is the same method
                        // but on the super-class
                        // 这里把父类的_super方法拷贝到当前对象中
                        this._super = _super[name];

                        // The method only need to be bound temporarily, so we
                        // remove it when we're done executing
                        // 调用父类的方法
                        var ret = fn.apply(this, arguments);
                        // 调用完毕后，归还子类的方法
                        this._super = tmp;

                        return ret;
                    };
                })(name, prop[name]) :
                prop[name];
        }

        // The dummy class constructor
        // 子类的模板对象。每个子类都是基于它创建出来的，就是父类的prototype会附加到这个类上，也就是一个代理类
        function Class() {
            // All construction is actually done in the init method
            if (!initializing && this.init)
                this.init.apply(this, arguments);
        }

        // Populate our constructed prototype object
        // 继承父类
        Class.prototype = prototype;

        // Enforce the constructor to be what we expect
        // 修正构造函数
        Class.prototype.constructor = Class;

        // And make this class extendable
        // 让子类也拥有exend方法
        Class.extend = arguments.callee;

        return Class;
    };
})();
{% endhighlight %}

他这个Class.extend只能实现一对一继承，比如
{% highlight javascript %}
var Computer = Class.extend({/*xxxxx*/});
var Lenovo = Computer.extend({/*xxxx*/});
// 无法实现多属性继承比如
var Computer = Class.extend(Class1, {/*自定义属性*/}, Base1 /*基类*/);
{% endhighlight %}

看完John Resig大神的继承方法之后，再来看下linkedin的继承模型[Fiber](https://github.com/linkedin/Fiber/blob/master/src/fiber.js)

看下他这个继承模型都提供了那些功能。

1、extend方法。公有和私有方法。

2、访问父类原型链，在你传递的构造函数中，Fiber会传给你base参数通过base参数可以调用父类原型上的方法。

3、Mixin一种接口的扩展方法。

4、decorate

5、proxy

分析代码如下
{% highlight javascript %}
(function() {
    /*global exports, global, define, module */
	// 兼容模块化方法
    (function(root, factory) {
        if (typeof exports === 'object') {
            // Node. Does not work with strict CommonJS, but
            // only CommonJS-like environments that support module.exports,
            // like Node.
            module.exports = factory(this);
        } else if (typeof define === 'function' && define.amd) {
            // AMD. Register as an anonymous module.
            define(function() {
                return factory(root);
            });
        } else {
            // Browser globals (root is window)
            root.Fiber = factory(root);
        }
    }(this, function(global) {

        // Baseline setup
        // --------------

        // Stores whether the object is being initialized. i.e., whether
        // to run the `init` function, or not.
        var initializing = false,

            // Keep a few prototype references around - for speed access,
            // and saving bytes in the minified version.
            ArrayProto = Array.prototype,

            // Save the previous value of `Fiber`.
            // 保存以前的Fiber值
            previousFiber = global.Fiber;

        // Helper function to copy properties from one object to the other.
        // 复制函数，浅拷贝
        function copy(from, to) {
            var name;
            for (name in from) {
                if (from.hasOwnProperty(name)) {
                    to[name] = from[name];
                }
            }
        }

        // The base `Fiber` implementation.
        // 基类
        function Fiber() {}

        // ###Extend
        //
        // Returns a subclass.
        // extend继承方法
        Fiber.extend = function(fn) {
            // Keep a reference to the current prototye.
            // 当前对象原型的引用
            var parent = this.prototype,

                // Invoke the function which will return an object literal used to
                // define the prototype. Additionally, pass in the parent prototype,
                // which will allow instances to use it.
                // 调用传递进来的构造函数，并且给构造函数传入当前的原型，得到一个对象，该对象中所涉及的函数都持有对parent的调用权限
                properties = fn(parent),

                // Stores the constructor's prototype.
                // 保存构造函数原型
                proto;

            // The constructor function for a subclass.
            // 子类构造函数
            function child() {
                if (!initializing) {
                    // Custom initialization is done in the `init` method.
                    this.init.apply(this, arguments);
                    // Prevent subsequent calls to `init`. Note: although a `delete
                    // this.init` would remove the `init` function from the instance, it
                    // would still exist in its super class' prototype.  Therefore,
                    // explicitly set `init` to `void 0` to obtain the `undefined`
                    // primitive value (in case the global's `undefined` property has
                    // been re-assigned).
                    this.init = void 0;
                }
            }

            // Instantiate a base class (but only create the instance, without
            // running `init`). And, make every `constructor` instance an instance
            // of `this` and of `constructor`.
            // 这里跟john大神的原理一样，不调用init只是创建父类的对象实例
            initializing = true;
            // 实例化父类对象实例(当前类)，然后子类继承它
            proto = child.prototype = new this;

            initializing = false;

            // Add default `init` function, which a class may override; it should
            // call the super class' `init` function (if it exists);
            // 如果继承对象没有提供默认的init时则创建默认的init函数调用父类的init方法
            proto.init = function() {
                if (typeof parent.init === 'function') {
                    parent.init.apply(this, arguments);
                }
            };

            // Copy the properties over onto the new prototype.
            // 用户自定义方法拷贝到proto原型中
            copy(properties, proto);

            // Enforce the constructor to be what we expect.
            // 修正原型的构造函数为child，跟john那个extend里面的Class一样哈，只不过这里将属性和构造函数分开来了
            proto.constructor = child;

            // Keep a reference to the parent prototype.
            // (Note: currently used by decorators and mixins, so that the parent
            // can be inferred).
            // 保存父类原型的引用
            child.__base__ = parent;

            // Make this class extendable, this can be overridden by providing a
            // custom extend method on the proto.
            // 子类可以重写extend方法。如果不重写就是用Fiber的extend
            child.extend = child.prototype.extend || Fiber.extend;

            // 返回子类对象，现在你可以是用他了 :)
            return child;
        };

        // Utilities
        // ---------

        // ###Proxy
        //
        // Returns a proxy object for accessing base methods with a given context.
        //
        // - `base`: the instance' parent class prototype.
        // - `instance`: a Fiber class instance.
        //
        // Overloads:
        //
        // - `Fiber.proxy( instance )`
        // - `Fiber.proxy( base, instance )`
        //
        // 返回基于base上下文的代理对象，就是说instace的执行环境是基于base的this的
        Fiber.proxy = function(base, instance) {
            var name,
                iface = {},
                wrap;

            // If there's only 1 argument specified, then it is the instance,
            // thus infer `base` from its constructor.
            // 如果传入一个参数，那么base执行环境就从他的构造函数中拿到__base__(因为上面保存了父类的原型)，这里就是修正base
            if (arguments.length === 1) {
                instance = base;
                base = instance.constructor.__base__;
            }

            // Returns a function which calls another function with `instance` as
            // the context.
            // 包装用户传递引来的函数，保证他的执行环境在__base__中
            wrap = function(fn) {
                return function() {
                    // 保证执行环境
                    return base[fn].apply(instance, arguments);
                };
            };

            // For each function in `base`, create a wrapped version.
            // 如果父类对象有这个方法，就进行包装，包装后的函数放在iface中
            for (name in base) {
                if (base.hasOwnProperty(name) && typeof base[name] === 'function') {
                    iface[name] = wrap(name);
                }
            }
            // 返回包装后的对象
            return iface;
        };

        // ###Decorate
        //
        // Decorate an instance with given decorator(s).
        //
        // - `instance`: a Fiber class instance.
        // - `decorator[s]`: the argument list of decorator functions.
        //
        // Note: when a decorator is executed, the argument passed in is the super
        // class' prototype, and the context (i.e. the `this` binding) is the
        // instance.
        //
        //  *Example usage:*
        //
        //     function Decorator( base ) {
        //       // this === obj
        //       return {
        //         greet: function() {
        //           console.log('hi!');
        //         }
        //       };
        //     }
        //
        //     var obj = new Bar(); // Some instance of a Fiber class
        //     Fiber.decorate(obj, Decorator);
        //     obj.greet(); // hi!
        //
        Fiber.decorate = function(instance /*, decorator[s] */ ) {
            var i,
                // Get the base prototype.
                // 获取到父类的原型对象
                base = instance.constructor.__base__,
                // Get all the decorators in the arguments.
                // 取得包装函数
                decorators = ArrayProto.slice.call(arguments, 1),
                len = decorators.length;

            for (i = 0; i < len; i++) {
                // 先调用传递进来的包装函数，会给包装函数传递base(父类原型)，这里包装里面的对象持有对base的访问权限
                // 接着将包装好的对象拷贝给传入的obj(instance)就是第一个参数中
                copy(decorators[i].call(instance, base), instance);
            }
        };

        // ###Mixin
        //
        // Add functionality to a Fiber definition
        //
        // - `definition`: a Fiber class definition.
        // - `mixin[s]`: the argument list of mixins.
        //
        // Note: when a mixing is executed, the argument passed in is the super
        // class' prototype (i.e., the base)
        //
        // Overloads:
        //
        // - `Fiber.mixin( definition, mix_1 )`
        // - `Fiber.mixin( definition, mix_1, ..., mix_n )`
        //
        // *Example usage:*
        //
        //     var Definition = Fiber.extend(function(base) {
        //       return {
        //         method1: function(){}
        //       }
        //     });
        //
        //     function Mixin(base) {
        //       return {
        //         method2: function(){}
        //       }
        //     }
        //
        //     Fiber.mixin(Definition, Mixin);
        //     var obj = new Definition();
        //     obj.method2();
        //
        // 跟decorate一样一样的。也是持有base的访问权限，然后拷贝到传入的第一个参数中
        Fiber.mixin = function(definition /*, mixin[s] */ ) {
            var i,
                // Get the base prototype.
                base = definition.__base__,
                // Get all the mixins in the arguments.
                mixins = ArrayProto.slice.call(arguments, 1),
                len = mixins.length;

            for (i = 0; i < len; i++) {
                copy(mixins[i](base), definition.prototype);
            }
        };

        // ###noConflict
        //
        // Run Fiber.js in *noConflict* mode, returning the `fiber` variable to
        // its previous owner. Returns a reference to the Fiber object.
        // 还原以前定义的Fiber值
        Fiber.noConflict = function() {
            global.Fiber = previousFiber;
            return Fiber;
        };

        return Fiber;
    }));
}());
{% endhighlight %}

Fiber他这个继承模型还是比较不错的，继承都包装在了函数中，可扩展的东西就多了，比如私有方法。但是他也是单继承，不过他有mixin接口可以很好的让类实现某些接口但不需要继承。

<b class="green">jquery、kissy、dojo等库/框架由于时间关系，留着后面点分析吧。</b>


### 4.面向对象的组件设计

这就面向对象简单吧，可能你心里会骂：土豆你在逗我吗？这种代码我早就知道了，但是并没有卵用啊。我猜到了，所以举个例子，让面向对象有卵用起来。

先思考一下我们要写一个组件，组件要写成什么呀？路有千万种，怎么走完全看你自己喜欢，但是总好好坏之分，但是并无对错。

先说说我对组件的理解：

* 核心层

* UI层

* 组件层

* 逻辑层

核心层：看名知意了，就是整个框架的底层，它提供了最底层的支撑，比如说 jQuery、underscore、zepto这种库。UI层：它是整组件的底层，它定义结构和样式。组件层：各种组件的封装，依赖，嵌套等等。但不涉及任何逻辑的东西。因为组件就需要有足够的复用性。逻辑层：根据使用场景不同，可能组件会跟着业务逻辑做出对应的修改，这时组件一般就不具备复用性

请看下面代码，一个组件库面向对象的基本思路，这里我写伪代码了哈。

{% highlight javascript %}
// 对象继承基
var Class = function(){};
// 抽象组件通用的方法形成基类。jquery ui 是典型的代表
var UiBase = Class.extend({
	el: '#box',					// 组件元素
	tamplate: '<div></div>',	// 组件模板
	init: function(){},			// 提供组件初始化方法
	render: function(){},		// 提供组件渲染方法
	destory: function(){},		// 组件销毁方法
});
// 然后我们需要一个搜索框的组件，可以这样
var Search = Class.extend(UiBase, {
	el: '',
	teamplate: '<input>',
	init: function(){},
	render: function(){},
	getDate(): function(){},	// ajax获取数据
	filter: function(){}		// 根据业务过滤数据
});
// 调用可以这样
new Search({
	el: '#search'
});
// 这样就够了吗？不是的组件一个很重要的就是模块通信，所以这里我们要创建一个事件类，让组件基类继承事件对象，这模块和模块就能解耦了。
// 伪代码大概是这样
var Event = {
	add: function(){},
	on: function(){},
	once: function(){},
	fire: function(){},
	remove: function(){}
}
Class.extend(UiBase, Event);
// 你也可以在扩展出model、view等

new Search({el:'#search'}).fire('reset', {opts:opts});	// 通知search组件事件发生，并传递参数
{% endhighlight %}

这里我偷懒的全些的伪代码，只是提供一种思想的思想。

总结：学习了javascript对象的基本形态，javascript对象如何继承，javascript对象继承的各种实现方法，组件化的一种思路。最后写代码重要的时思想，不要别说怎么怎么样，你自己思考过吗？为何要这样？你问过自己吗？盲目跟风，只会让你的思想变成癌症，思想变得僵化。
