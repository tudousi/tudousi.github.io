---
layout: post
title: javascript 实现接口类
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


接口和工厂模式的实例
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>factory1</title>
</head>
<body>
	
<script>
	// 类继承
	function extend(subClass, superClass) {
		function F(){};
		F.prototype = superClass.prototype;
		subClass.prototype = new F();
		subClass.prototype.constructor = subClass;		// 修正构造函数
		subClass.superclass = superClass.prototype;		// 间接调用父类
	}
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
	// 检测对象是否有实现指定的接口
	function addForm(formInstance) {
		// 实现接口
		Interface.ensureImplements(formInstance, Composite, FormItem);
	}
	/* old
	// 创建新的实例交给工厂对象
	var BicycleFactory = {
		createBicycle: function(model){
			var bicycle;
			switch(model){
				case 'The Speedster':
					bicycle = new Speedster();
					break;
				case 'The Lowrider':
					bicycle = new Lowrider();
					break;
				case 'The Comfort Cruiser':
				default:
					bicycle = new ComfortCruiser();
			}
			// 对单车类进行检查，是否实现了所有的 Bicycle 接口
			Interface.ensureImplements(bicycle, Bicycle);
			// 创建一辆单车的时候需要组装
			//bicycle.assemble();
			// 出厂还需要清洗
			//bicycle.wash();
			// 出厂
			return bicycle;
		}
	};
	*/
	// 自行车基类
	var BicycleShop = function(){};
	BicycleShop.prototype = {
		// 接口返回自行车实例，他们都能互相使用，因为都实现类 Bicycle接口
		sellBicycle: function(model){
			var bicycle = this.createBicycle(model);

			bicycle.assemble();
			bicycle.wash();
			return bicycle;
		},
		createBicycle: function(model) {
			throw Error('生产单车基类没有实现生产单车方法！');
		}
	};

	// 生产厂商自定义直行车生产方法(美国生产厂商)
	var AcmeBicycleShop = function(){};
	// 美国的生产厂商继承自行车厂商
	extend(AcmeBicycleShop, BicycleShop);
	// 美国生产厂商生产单车的方法
	AcmeBicycleShop.prototype.createBicycle = function(model) {
		var bicycle;
		switch(model){
			case 'The Speedster':
				bicycle = new Speedster();
				break;
			case 'The Lowrider':
				bicycle = new Lowrider();
				break;
			case 'The Comfort Cruiser':
			default:
				bicycle = new ComfortCruiser();
		}
		// 对单车类进行检查，是否实现了所有的 Bicycle 接口
		Interface.ensureImplements(bicycle, Bicycle);
		// 创建一辆单车的时候需要组装
		bicycle.assemble();
		// 出厂还需要清洗
		bicycle.wash();
		// 出厂
		return bicycle;
	};

	// 通用的生产厂商
	var GeneralProductsBicycleShop = function(){};
	extend(GeneralProductsBicycleShop, BicycleShop);
	// 生产的方法不同
	GeneralProductsBicycleShop.prototype.createBicycle = function(model){
		var bicycle;
		switch(model){
			case 'The Speedster':
				bicycle = new GeneralProductsSpeedster();
				break;
			case 'The Lowrider':
				bicycle = new GeneralProductsLowrider();
				break;
			case 'The Comfort Cruiser':
			default:
				bicycle = new GeneralProductsComfortCruiser();
		}
		// 对单车类进行检查，是否实现了所有的 Bicycle 接口
		Interface.ensureImplements(bicycle, Bicycle);
		// 创建一辆单车的时候需要组装
		bicycle.assemble();
		// 出厂还需要清洗
		bicycle.wash();
		// 出厂
		return bicycle;
	};




	// 定义自行车接口类
	var Bicycle = new Interface('Bicycle', ['assemble', 'wash', 'ride', 'repair'])

	// 定义 Speedster 单车类
	var Speedster = function(){};
	// 实现 Bicycle(单车) 接口
	Speedster.prototype = {
		// 组装
		assemble: function(){console.log('组装！');},
		wash: function(){console.log('清洗！');},
		ride: function(){console.log('骑车！');},
		repair: function(){console.log('修理！');}
	};

	// 定义自行车接口类
	var Bicycle = new Interface('Bicycle', ['assemble', 'wash', 'ride', 'repair'])

	// 定义 Speedster 单车类
	var GeneralProductsSpeedster = function(){};
	// 实现 Bicycle(单车) 接口
	GeneralProductsSpeedster.prototype = {
		// 组装
		assemble: function(){console.log('组装！');},
		wash: function(){console.log('清洗！');},
		ride: function(){console.log('骑车！');},
		repair: function(){console.log('修理！');}
	};


	// 生产一个单车
	var californiaCruisers = new AcmeBicycleShop();
	// 创建指定型号的单车
	yourNewBike = californiaCruisers.createBicycle('The Speedster');
	// 用户拿到手就可以骑行。
	yourNewBike.ride();
	// 单车坏了，可以进行修理
	yourNewBike.repair();

	// 生产一个单车
	var californiaCruisers = new GeneralProductsBicycleShop();
	// 创建指定型号的单车
	yourNewBike = californiaCruisers.createBicycle('The Speedster');
	// 用户拿到手就可以骑行。
	yourNewBike.ride();
	// 单车坏了，可以进行修理
	yourNewBike.repair();


	/*
	// 老的生产单车到够买和使用方法
	// 创建一个单车商店
	var californiaCruisers = new BicycleShop();
	// 创建指定型号的单车
	yourNewBike = californiaCruisers.sellBicycle('The Speedster');
	// 用户拿到手就可以骑行。
	yourNewBike.ride();
	// 单车坏了，可以进行修理
	yourNewBike.repair();
	*/

	/*
	// 实现2个接口对象
	var Composite = new Interface('Composite', ['add', 'remove', 'getChild']);
	var FormItem = new Interface('FormItem', ['save']);
	//具体类
	var CompositeForm = function(id, method, action) {};
	CompositeForm.prototype = {
		add: function(){console.log('add');},
		remove: function(){console.log('remove');},
		getChild: function(){console.log('getChild');},
		save: function(){console.log('save');}
	}
	// 实例化类，开始检测接口
	var comObject = new CompositeForm()
	addForm(comObject);
	comObject.add();
	*/
</script>
</body>
</html>

```



