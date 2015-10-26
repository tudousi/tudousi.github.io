---
layout:     post
title:      express 4.0 - RESTFul API（part2）
summary:    上一部分介绍了express的一些基础东西，这部分来写一个实例RESTFul API来深入学习Express
categories: node
tags:       [express,restful]
---

### 介绍
在[上一部分](http://createthink.net/node/2015/10/21/express-part1/)介绍了express和学习了如何建立一个express项目。这部分就来完成一个restful API来深入学习express。

我们这部分要学到到的有mongodb数据库操作，使用chrome插件Postman测试api接口和express新的路由系统。

假设我们是一个咖啡种植者，我们会对我们仓库的咖啡豆进行[CRUD](http://baike.baidu.com/link?url=5wp0Jm5Ba_Fou1e2pn4Lyt6j9mCsRm1FYk_joMFXyGzBWJX4de4KBBr9XCVA98rTb9DeQODKqIeaHDNwMo7Gu_)。

我们的程序url不使用传统的url语义对咖啡豆进行CRUD，这里使用HTTP的动词对接口进行定义（GET、POST、PUT、DELETE）。

我们的程序所有交互数据都统一使用JSON。

### 安装环境
确保环境都是正常的：1、mongodb 2、node

mongodb：mac 下使用brew install mongodb安装，使用brew info mongodb查看启动命令。如果是Win直接官方[下载安装包](https://www.mongodb.org/downloads)运行即可。

### 第一步
首先搭建express项目，创建一个文件夹，里面在创建如下文件夹和文件。

* app

* ---models

* ------coffeeBeans.js        // 咖啡豆数据模型

* package.json              // 程序配置文件

* serve.js                  // 程序胶水和路由

package.json文件内容如下
{% highlight javascript %}
{
    "name": "node-restful-api",
    "main": "server.js",
    "dependencies": {
        "express": "~4.0.0",
        "mongoose": "~3.6.13",
        "body-parser": "~1.0.1"
    }
}
{% endhighlight %}
在当前目录下执行 <b>npm install</b> 安装程序所要依赖的包。

### 创建一个程序的基础
express需要引入到我们的serve.js文件中来，因为需要靠他搭建http服务。还需要接受到客户端的请求和返回给客户端请求，那么就要依赖body-parse这个解析的模块。

serve.js基础代码如下
{% highlight javascript %}
var express    = require('express');                // 引入 express
var app        = express();                         // 创建express程序
var bodyParser = require('body-parser');            // 解析请求数据
app.use(bodyParser.urlencoded({ extended: true })); // 解析提交的数据from方式
app.use(bodyParser.json());                         // 解析提交的数据json方式
var port = process.env.PORT || 3000;                // 设置程序启动端口
// 创建一个路由实例，这是express 4.0新增
var router = express.Router();

// 绑定路由用来测试当然程序是否工作正常
router.get('/', function(req, res) {
    res.json({ message: 'hello,world!' });   
});
// 如何还有路由可以绑定在这里。

// 注册路由，我们最后的路由实例都会交给app.use去注册
// 注意我们现在的url地址为 http://localhost:3000/api
app.use('/api', router);
// 开启服务监听端口
app.listen(port);
console.log('程序正启动在 ' + port + ' 端口。');
{% endhighlight %}

在当然目录使用 <b>node serve.js</b> 控制台则会提示 程序正启动在 3000 端口。表示启动成功。

接着打开chrome插件Postman输入 http://localhost:3000/api 点击Send会看到如下信息
![图片转换](/resource/node-express-part2/img1.png)

做的很好，很简单。继续往下吧。

### 第二部份
尽量简短点说，因为我发现我现在还没洗澡和洗衣服。ok。接着我们来定义数据库的模型。

这里对mongodb数据库的操作我使用mongoose这个库，当然你也可以使用更原始的mongodb库，mongoose库只是对mongodb进行了orm的包装哈。

mongodb数据库就像一个仓库。而咖啡豆就是仓库里放置的物品，我们需要把咖啡豆定义成一个[模型](http://mongoosejs.com/docs/api.html#model-js)。他有大小颜色和产品，并且它还能增加、删除、修改和查询数量。

在定义之前做点准备工作，就是使用node去连接mongodb数据库。代码如下
{% highlight javascript %}
var mongoose   = require('mongoose');       // 引入mongoose数据库包
mongoose.connect('mongodb://localhost:27017/coffee');  // 连接到数据库
{% endhighlight %}

在这里使用 <b>node serve.js</b> 启动下服务，如果没有报错就可以继续

定义coffeeBeans数据库模型，需要实例化mongoose.Schema对象创建一个新模型，然后使用mongoose.model进行模型注册。

app/models/coffeeBeans.js代码如下
{% highlight javascript %}
var mongoose     = require('mongoose');     // 引入mongoose库
var Schema       = mongoose.Schema;         // 获得Schema对象

var coffeeBeans   = new Schema({
    weight: Number,                         // 字段类型为数字
    color: {type: String, default: 'puce'}  // 字段类型为字符串，默认值为puce
});

module.exports = mongoose.model('CoffeeBean', coffeeBeans);     // 定义和导出模型
{% endhighlight %}

在server.js中引入定义好的模型。目前所有代码如下
{% highlight javascript %}
var express    = require('express');                // 引入 express
var mongoose   = require('mongoose');
var app        = express();                         // 创建express程序
var bodyParser = require('body-parser');            // 解析请求数据
var CoffeeBean     = require('./app/models/coffeeBeans');  // 调用模型

mongoose.connect('mongodb://localhost:27017/coffee');

app.use(bodyParser.urlencoded({ extended: true })); // 解析提交的数据from方式
app.use(bodyParser.json());                         // 解析提交的数据json方式
var port = process.env.PORT || 3000;                // 设置程序启动端口
// 创建一个路由实例，这是express 4.0新增
var router = express.Router();

// 绑定路由用来测试当然程序是否工作正常
router.get('/', function(req, res) {
    res.json({ message: 'hello,world!' });
});
// 如何还有路由可以绑定在这里。

// 注册路由，我们最后的路由实例都会交给app.use去注册
// 注意我们现在的url地址为 http://localhost:3000/api
app.use('/api', router);
// 开启服务监听端口
app.listen(port);
console.log('程序正启动在 ' + port + ' 端口。');
{% endhighlight %}

### 第三部分
有了数据库模型，在来看看我们如何定义路由和我们想要的路由是怎么样的，和他们的分工

|       路由HTTP         |      动词      |       描述          |
| :-------------------  | :------------- | :----------------- |
| /api/coffee           |       GET      | 获取仓库所有的咖啡豆  |
| /api/coffee           |       POST     | 增加咖啡豆库存       |
| /api/coffee/:c_id     |       POST     | 获取指定库存的咖啡    |
| /api/coffee/:c_id     |       PUT      | 更新咖啡豆           |
| /api/coffee/:c_id     |       DELETE   | 删除咖啡豆           |

这里值有一个路由地址，所有的操作都包含在了HTTP动词中了，简单优雅。

现在把所有的路由都创建起来让它们可以被访问到，操作和数据后面再来填充。代码如下
{% highlight javascript %}
var express    = require('express');                // 引入 express
var mongoose   = require('mongoose');
var app        = express();                         // 创建express程序
var bodyParser = require('body-parser');            // 解析请求数据
var CoffeeBean     = require('./app/models/coffeeBeans');  // 调用模型

mongoose.connect('mongodb://localhost:27017/coffee');

app.use(bodyParser.urlencoded({ extended: true })); // 解析提交的数据from方式
app.use(bodyParser.json());                         // 解析提交的数据json方式
var port = process.env.PORT || 3000;                // 设置程序启动端口
// 创建一个路由实例，这是express 4.0新增
var router = express.Router();

router.route('/coffee')
    .get(function(req, res) {
        // 获取所有咖啡豆
        res.json({message: '获取所有咖啡豆'});
    })
    .post(function(req, res) {
        // 新增咖啡豆库存
        res.json({message: '新增咖啡豆库存'});
    })
    .put(function(req, res) {
        // 修改咖啡豆
        res.json({message: '修改咖啡豆'});
    })
    .delete(function(req, res) {
        // 删除咖啡豆
        res.json({message: '删除咖啡豆'});
    });

// 注册路由，我们最后的路由实例都会交给app.use去注册
// 注意我们现在的url地址为 http://localhost:3000/api
app.use('/api', router);
// 开启服务监听端口
app.listen(port);
console.log('程序正启动在 ' + port + ' 端口。');
{% endhighlight %}

### 第四部分
读写数据库。编写业务逻辑让程序丰满起来。

先来填充psot新增操作，代码如下
{% highlight javascript %}
.post(function(req, res) {
    // 新增咖啡豆库存

    // 创建咖啡豆模型实例
    var coffee = new CoffeeBean({
        weight: req.body.weight,        // 保存从客户端获取的重量
        color: req.body.color           // 保存从客户端获取的颜色
    });
    coffee.save(function(err) {
        if(err) {
            res.send(err);
        } else {
            res.json({'message': '咖啡豆已入库存！'});
        }
    });
})
{% endhighlight %}

用Postman测试下，已经成功增加了一颗咖啡豆。请注意要使用画红框的提交方式。
![图片转换](/resource/node-express-part2/img2.png)
不错，现在程序流程正常，接着来编写get请求获取所有咖啡豆，get代码如下。没有什么特别的就是使用了mongoose模型的find方法
{% highlight javascript %}
.get(function(req, res) {
    // 获取所有咖啡豆
    CoffeeBean.find(function(err, coffee) {
        if(err) {
            res.send(err);
        } else {
            res.json(coffee);
        }
    });
})
{% endhighlight %}
返回数据如下
![图片转换](/resource/node-express-part2/img3.png)

非常好，添加获取都有了。那么跟着就来编写获取单个咖啡豆的代码

还记得刚刚编写的
router.route('/coffee').get().post().put().delete()代码吗？后面的所有处理函数都是只针对 '/coffe' 路由地址的，如果想要根据咖啡豆的id获取到某个咖啡豆，需要使用这样的格式 '/coffee/:c_id'，这个c_id是客户端传递过来的id参数。

所以需要重新绑定一个新的路由地址针对 '/coffee/:c_id' 这样的路由进行绑定方法。下面是完整的代码
{% highlight javascript %}
var express    = require('express');                // 引入 express
var mongoose   = require('mongoose');
var app        = express();                         // 创建express程序
var bodyParser = require('body-parser');            // 解析请求数据
var CoffeeBean     = require('./app/models/coffeeBeans');  // 调用模型

mongoose.connect('mongodb://localhost:27017/coffee');

app.use(bodyParser.urlencoded({ extended: true })); // 解析提交的数据from方式
app.use(bodyParser.json());                         // 解析提交的数据json方式
var port = process.env.PORT || 3000;                // 设置程序启动端口
// 创建一个路由实例，这是express 4.0新增
var router = express.Router();

router.route('/coffee')
    .get(function(req, res) {
        // 获取所有咖啡豆
        CoffeeBean.find(function(err, coffee) {
            if(err) {
                res.send(err);
            } else {
                res.json(coffee);
            }
        });
    })
    .post(function(req, res) {
        // 新增咖啡豆库存
        // 创建咖啡豆模型实例
        var coffee = new CoffeeBean({
            weight: req.body.weight,        // 保存从客户端获取的重量
            color: req.body.color           // 保存从客户端获取的颜色
        });
        coffee.save(function(err) {
            if(err) {
                res.send(err);
            } else {
                res.json({'message': '咖啡豆已入库存！'});
            }
        });
    })
    .put(function(req, res) {
        // 修改咖啡豆
        res.json({message: '修改咖啡豆'});
    })
    .delete(function(req, res) {
        // 删除咖啡豆
        res.json({message: '删除咖啡豆'});
    });
// 绑定新路由，针对 http://localhost:3000/api/coffee/c_id这样的地址
router.route('/coffee/:c_id')
    .get(function(req, res) {
        // 获取get传递的参数，然后使用mongoose模型的findById方法查找mongodb指定的_id数据
        CoffeeBean.findById(req.params.c_id, function(err, coffee) {
            if(err) {
                res.send(err);
            } else {
                res.json(coffee);
            }
        });
    })
// 注册路由，我们最后的路由实例都会交给app.use去注册
// 注意我们现在的url地址为 http://localhost:3000/api
app.use('/api', router);
// 开启服务监听端口
app.listen(port);
console.log('程序正启动在 ' + port + ' 端口。');
{% endhighlight %}

ok，ok 继续编写修改咖啡豆属性的代码，代码如下
{% highlight javascript %}
// ...
router.route('/coffee/:c_id')
    .get(function(req, res) {
        // ...
    })
    .put(function(req, res) {
        CoffeeBean.findById(req.params.c_id, function(err, coffee) {
            if(err) {
                res.send(err);
            } else {
                if(req.body.weight) coffee.weight = req.body.weight;
                if(req.body.color) coffee.color = req.body.color;
                console.log(req.body.weight, req.body.color)
                coffee.save(function(err) {
                    res.json({'message': '咖啡豆修改成功！'});
                });
            }
        });
    });
// ...
{% endhighlight %}

省略掉了其他的代码，继续用Postman测试下。可以看到颜色已经修改成了Brown。<span class="green">注意提交格式</span>
![图片转换](/resource/node-express-part2/img4.png)

好了，好了，请坚持一下，就要完成这个作品了。接着来编写删除咖啡豆的代码，代码如下
{% highlight javascript %}
// ...
router.route('/coffee/:c_id')
    .get(function(req, res) {
        // ...
    })
    .put(function(req, res) {
        // ...
    })
    .delete(function(req, res) {
        // 调用了mongoose模型的删除方法删除了指定_id值得数据
        CoffeeBean.remove({_id: req.params.c_id}, function(err, coffee) {
            if(err) {
                res.send(err);
            } else {
                res.json({'message': '咖啡豆删除成功！'});
            }
        });
    });
// ...
{% endhighlight %}

记得启动服务测试一下。已经查不到数据很好，就是我想要的结果。
![图片转换](/resource/node-express-part2/img5.png)

### 关于express路由
在[官方文档](http://www.expressjs.com.cn/4x/api.html#router)上可以看到路由是单独的一个功能提了出来。它就像一个express对象一样，可以单独被实例化。

var router = express.Router(); 这样就能创建一个路由实例。

router支持HTTP动词和链式进行绑定处理函数，比如 router.get().post().put().delete()。

router也支持对路由参数进行校验，比如router.param('c_id', callback) 像这样就可以在callback中对c_id做一些合法性和校验。

最后需要把我们自定义的router绑定到express程序上，app.use('/api', router) 像这样。那么这个路由地址的根地址都是基于/api的。

### 总结
切了一天图了，我现在脑子不是很清醒，如果有错误和不对的请留言告知我，谢了。:)

我们自定义了一个express程序。它会处理咖啡豆的一些CRUD，数据库使用了[mongodb](https://www.mongodb.org/)，对数据库的操作使用了[mongoose](http://mongoosejs.com/)库。并且使用了express 4.0灵活的路由系统创建了一个RESTFul API的基础版本。

关于这个程序也可以继续扩充，发现了吗？它还身份验证功能。我们的程序不是谁都能使用的，只有经过身份验证才能正常调用接口。
推荐一个非常好用的身份验证库[Passport](http://passportjs.org/)。好了本部分结束。
