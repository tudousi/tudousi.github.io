---
layout:     post
title:      express 4.0 - 概括（part1）
summary:    Express 是一个简洁而灵活的 node.js Web应用框架, 提供一系列强大特性帮助你创建各种Web应用。Express 不对 node.js 已有的特性进行二次抽象，我们只是在它之上扩展了Web应用所需的功能。丰富的HTTP工具以及来自Connect框架的中间件随取随用，创建强健、友好的API变得快速又简单
categories: node
tags:       [node发送邮件,node email]
---

### 本文所涵盖的内容：
1. Express 3.0 和 Express 4.0 区别
2. Express 简介
3. 创建一个Express项目（使用官方生成器和自定义2种方式）

<span class="green">如果下文不特别制定Express 3.0, 那么Express就表示Express 4.0</span>

### Express 3.0 和 Express 4.0 区别
嘿嘿自己去[Express](https://github.com/strongloop/express/wiki/Migrating-from-3.x-to-4.x)看

但是还是说点什么，不然这段太少了。

Express 把一些中间件都抽离出来了，这样做是可以更方便的版本管理和单独修复bug。
举个例子，比如我们在Express 3.0中要解析body和cookies是这样使用的
{% highlight javascript %}
var express = require('express');
//...省略其他代码
app.use(express.bodyParser());
app.use(express.cookieParser());
{% endhighlight %}
但是我们到了Express 4.0中就要这样
{% highlight javascript %}
var express = require('express');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
//...省略其他代码
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
{% endhighlight %}
还有一个重大更改的地方就是Routes，Express 4.0中路由变得像一个小型的Express程序一样，他提供了.use .get .param 和 route API。后面再详细讲解

### Express 简介
> Express 是一个简洁而灵活的 node.js Web应用框架, 提供一系列强大特性帮助你创建各种Web应用。Express 不对 node.js 已有的特性进行二次抽象，我们只是在它之上扩展了Web应用所需的功能。丰富的HTTP工具以及来自Connect框架的中间件随取随用，创建强健、友好的API变得快速又简单

上面的说话太官方了,简单来说如果你想做一个网站使用Express就对了，它功能全面绝对可以满足你。

Express基础功能：

1. 路由系统

2. 模板系统

3. 中间件（Express的灵魂）

4. 静态资源服务

5. 错误处理

### 创建一个Express项目（使用官方生成器）
Eexpress 4 和 3自带的生成工具稍微有些变化了。在4中需要安装express-generator@4包
{% highlight javascript %}
npm install -g express-generator@4   // 安装Express生成工具包
{% endhighlight %}
创建工程，express还有些参数可选具体请执行命令观看 express -h

1. 如果你的控制台所在的目录是你项目的目录，你可以直接执行 express [options] 创建工程文件

2. 如果你想创建一个新文件夹，并且新的工程在这个文件夹内你可以使用 express ./project [options] 创建工程
{% highlight javascript %}
// 方式1 exress [options]
// 方式2 express ./project [options]
express -e    //我使用这个
{% endhighlight %}

安装Express所需要的模块
{% highlight javascript %}
npm install
{% endhighlight %}

启动Express项目
{% highlight javascript %}
npm start
{% endhighlight %}
等待一会Express所需要依赖的包文件就安装好了。执行 npm start 来启动Express程序，接着访问浏览器 http://localhost:3000/ 就可以看到网页渲染出了默认的欢迎界面了

在我们平时写node程序时，启动node程序不都是用node xxx.js这里为什么使用npm start，这里提一下npm的一些知识，秘密就在于package.json文件中的scripts字段，看看官方的[文档](https://docs.npmjs.com/misc/scripts)。npm 会在项目的 package.json 文件中寻找 scripts 区域，其中包括npm test和npm start等命令。其实npm test和npm start是npm run test和npm run start的简写。事实上，你可以使用npm run来运行scripts里的任何条目。

### 创建一个Express项目（自定义方式）
首先在你的工程目录创建2个文件

* package.json

* app.js

package.json 内容如下
{% highlight javascript %}
// package.json
{
    "name": "my-express",
    "main": "app.js",
    "dependencies": {
        "express": "~4.0.0"
    }
}
{% endhighlight %}

在当前工程执行 npm install 安装Express模块

app.js 内容如下
{% highlight javascript %}
var express = require('express');
var app     = express();
var port    =   process.env.PORT || 3000;

// 定义路由
// 创建一个默认的欢迎路由
app.get('/welcome', function(req, res) {
    res.send('hello, welcome!');  
});

// 在这里创建其他路由

// 开启服务
app.listen(port);
console.log('start on port ' + port);
{% endhighlight %}

执行命令 node app.js 开启服务，然后在浏览器访问 http://localhost:3000/welcome 可以看到欢迎文字。到此我们对Express安装和创建基本完成了。

总结：

Express 3.0已经是被淘汰了，大家不要使用了。现在网络上基本都是Express 4所以我们也要跟进。介绍了Express 4创建工程的2种方法和Express 3和Express 4的一些区别。

下一章内容：

* 对Express 4的组成和模块进行大致的了解（并不是源码分析），只是了解用到的一些主要的模块。
* 对Express 4的路由进行学些，创建一个RESTful API 服务来体验下新版的Routes。
