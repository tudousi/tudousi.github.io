---
layout: post
title: javascript 强制实现指定接口
tags: [javascript]
---

### 接口在写面对对象的程序作用非常重要。接口是把对象的共同方法向上抽取得到的一个接口类，他们代表了对象共有的方法，所以子类对象必须实现指定的接口才能正常工作。


#### javascript 中没有 interface 和 implements 关键字来定义和实现接口,所以如果要让某个类实现指定接口需要写一些额外的代码来实现。 

先来写接口类，所有定义的接口都通过该类实例化出一个接口。

```javascript
var Interface = function(name, methods) {
	if(arguments.length !== 2) {
		throw Error('调用接口参数不正确！');
	}
	this.name = name;
	this.methods = [];
	for(var i = 0, len = methods.length; i < len; i++) {
		if(typeof methods[i] !== 'string') {
			throw Error('接口方法必须为字符串！');
		}
		this.methods.push(methods[i]);
	}
};
// 保证接口实现
Interface.ensureImplements = function(object) {
	if(arguments.length < 2) {
		throw Error('ensureImplements 函数必须要2个以上参数！');
	}
	// 遍历实现的接口
	for(var i = 1, len = arguments.length; i < len; i++) {
		var interface = arguments[i];
		// 如果实现接口函数不是Interface的对象不通过
		if(interface.constructor !== Interface) {
			throw Error('函数不是接口对象！');
		}
		for(var j = 0, methodLen = interface.methods.length; j < methodLen; j++) {
			var method = interface.methods[j];		// 获取实现接口
			if(!object[method] || typeof object[method] !== 'function') {	// 检查接口是否有方法且是函数
				throw Error('对象 ' + method + ' 接口方法没有被实现！');
			}
		}
	}
}
```

紧接着我们定义2个接口对象

```javascript
// 实现2个接口对象
var Composite = new Interface('Composite', ['add', 'remove', 'getChild']);
var FormItem = new Interface('FormItem', ['save']);
```

然后定义我们自己的实现类

```javascript
//具体类
var CompositeForm = function(id, method, action) {};
CompositeForm.prototype = {
	add: function(){console.log('add');},
	remove: function(){console.log('remove');},
	getChild: function(){console.log('getChild');},
	save: function(){console.log('save');}
}
```

让我们自定义实现 Composite 和 FormItem 接口，并且检查自定义是否实现了接口的所有方法，只要有一个方法没有被实现将会抛出错误异常

```javascript
var comObject = new CompositeForm()
Interface.ensureImplements(comObject, Composite, FormItem);
```
以上工作都做完了，可以调用我们自己的类方法了。

```javascript
comObject.add();
```







