---
layout: post
title: Connect 中间件使用解析
tags: [javascript]
---

### Connect 介绍
 Connect是一款高性能、易插拔、开发中间件简单的中间件框架。正是因为这些特点，它在 Node.js 开发框架中是最流行的框架，到目前为止 Connect 有已多达 [200](https://github.com/senchalabs/connect/wiki) 个中间件插件。并且著名的 Express 框架的基础架构也是依赖 Connect 中间件
 
 有人把 Connect 框架比作层层的过滤网。每个中间件在http处理过程中通过改写request或（和）response的数据、状态，实现了特定的功能。但是我自己认为 Connect 就像一个加工厂，进入某个路由的时候由这个路由所绑定的中间件对路由进行处理和加工，把最后的结果返回出去（出厂）。
 
 
 **我使用的版本的 2.9.0**
 你可以通过 `npm install -g connect@2.9.0` 进行安装（这个版本内置了基础的中间件，如果你使用更高版本是没有内置中间件的） 
 
 
####写一个简单的 WEB 服务器

```javascript
var Connect = require('connect'),
    app = Connect();

app.use(function(req, res){
    res.end('Hello World!');
}).listen(3000);
```
现在访问一下`http://localhost:3000/` 页面上显示 Hello World!

也可以这样创建 WEB 服务器

```javascript
var Connect = require('connect'),
    app = Connect(),
    http = require('http');

app.use(function(req, res){
    res.end('Hello World Hello Node.js!');
});
http.createServer(app).listen(3000)
```
Connect 处理流程<br>
<img src="{{ site.cdn }}/blog/images/javascript/connect-process.png" alt="connect处理流程"/>
<br>

Connect 提供了 WEB 开发的常用基本插件。

- bodyParser	// 请求内容解析中间件
- multipart		// 文件上传中间件
- cookieParser	// cookie 解析中间件
- session		// session 会话中间件
- static		// 静态文件处理中间件
- vhost			// 虚拟二级域名映射中间件
- favicon		// 网页图标中间件
- 等等一些列的中间件

#### 对 Connect 源码分析

#####connect.js
```javascript
/*!
 * Connect
 * Copyright(c) 2010 Sencha Inc.
 * Copyright(c) 2011 TJ Holowaychuk
 * MIT Licensed
 */

/**
 * Module dependencies.
 */
// 引入必须的模块
var EventEmitter = require('events').EventEmitter
  , proto = require('./proto')
  , utils = require('./utils')
  , path = require('path')
  , basename = path.basename
  , fs = require('fs');

// node patches

require('./patch');

// expose createServer() as the module

exports = module.exports = createServer;

/**
 * Framework version.
 */

exports.version = '2.7.11';

/**
 * Expose mime module.
 */

exports.mime = require('./middleware/static').mime;

/**
 * Expose the prototype.
 */

exports.proto = proto;

/**
 * Auto-load middleware getters.
 */

exports.middleware = {};

/**
 * Expose utilities.
 */

exports.utils = utils;

/**
 * Create a new connect server.
 *
 * @return {Function}
 * @api public
 */
// 创建服务 Connect()
function createServer() {
  // 这个函数会给 http.createServer 使用
  function app(req, res, next){ app.handle(req, res, next); }
  utils.merge(app, proto);    // 继承 proto
  utils.merge(app, EventEmitter.prototype);   // 继承 事件
  app.route = '/';    // 定义默认路由
  app.stack = [];     // 定义处理函数队列
  for (var i = 0; i < arguments.length; ++i) {
    app.use(arguments[i]);
  }
  // 返回 app 函数，可以用他创建 WEB 服务，也能把他传递给 http.createServer(app)
  return app;
};

/**
 * Support old `.createServer()` method.
 */
// 兼容老的版本
createServer.createServer = createServer;

/**
 * Auto-load bundled middleware with getters.
 */
// 加载中间件到 Connect 静态属性中
fs.readdirSync(__dirname + '/middleware').forEach(function(filename){
  if (!/\.js$/.test(filename)) return;
  var name = basename(filename, '.js');
  function load(){ return require('./middleware/' + name); }
  exports.middleware.__defineGetter__(name, load);
  exports.__defineGetter__(name, load);
});
```
#####proto.js
```javascript


/*!
 * Connect - HTTPServer
 * Copyright(c) 2010 Sencha Inc.
 * Copyright(c) 2011 TJ Holowaychuk
 * MIT Licensed
 */

/**
 * Module dependencies.
 */

var http = require('http')
  , utils = require('./utils')
  , debug = require('debug')('connect:dispatcher');

// prototype

var app = module.exports = {};

// environment

var env = process.env.NODE_ENV || 'development';

/**
 * Utilize the given middleware `handle` to the given `route`,
 * defaulting to _/_. This "route" is the mount-point for the
 * middleware, when given a value other than _/_ the middleware
 * is only effective when that segment is present in the request's
 * pathname.
 *
 * For example if we were to mount a function at _/admin_, it would
 * be invoked on _/admin_, and _/admin/settings_, however it would
 * not be invoked for _/_, or _/posts_.
 *
 * Examples:
 *
 *      var app = connect();
 *      app.use(connect.favicon());
 *      app.use(connect.logger());
 *      app.use(connect.static(__dirname + '/public'));
 *
 * If we wanted to prefix static files with _/public_, we could
 * "mount" the `static()` middleware:
 *
 *      app.use('/public', connect.static(__dirname + '/public'));
 *
 * This api is chainable, so the following is valid:
 *
 *      connect()
 *        .use(connect.favicon())
 *        .use(connect.logger())
 *        .use(connect.static(__dirname + '/public'))
 *        .listen(3000);
 *
 * @param {String|Function|Server} route, callback or server
 * @param {Function|Server} callback or server
 * @return {Server} for chaining
 * @api public
 */

app.use = function(route, fn){
  // default route to '/'

  if ('string' != typeof route) {
    fn = route;
    route = '/';
  }

  // wrap sub-apps
  // 如果 handle 是一个 Connect 则包装 Connect
  if ('function' == typeof fn.handle) {
    var server = fn;
    fn.route = route;
    fn = function(req, res, next){
      server.handle(req, res, next);
    };
  }

  // wrap vanilla http.Servers
  if (fn instanceof http.Server) {
    fn = fn.listeners('request')[0];
  }

  // strip trailing slash
  /**
   * 取消末尾斜线
   */
  if ('/' == route[route.length - 1]) {
    route = route.slice(0, -1);
  }

  // add the middleware
  debug('use %s %s', route || '/', fn.name || 'anonymous');
  // 相关路由对应处理函数压入堆中
  this.stack.push({ route: route, handle: fn });

  return this;
};

/**
 * Handle server requests, punting them down
 * the middleware stack.
 *
 * @api private
 */
// 执行路由对应的中间件
app.handle = function(req, res, out) {
  var stack = this.stack
    , fqdn = ~req.url.indexOf('://')
    , removed = ''
    , slashAdded = false
    , index = 0;
  // 切换到下一个中间件
  function next(err) {
    var layer, path, status, c;

    if (slashAdded) {
      req.url = req.url.substr(1);
      slashAdded = false;
    }

    req.url = removed + req.url;
    req.originalUrl = req.originalUrl || req.url;
    removed = '';

    // next callback
    layer = stack[index++];

    // all done
    // 所有 url 对应的路由处理完毕 http 头被发送
    if (!layer || res.headerSent) {
      // delegate to parent
      if (out) return out(err);

      // unhandled error
      // 所有错误处理会在 handle 执行完毕
      if (err) {
        // default to 500
        if (res.statusCode < 400) res.statusCode = 500;
        debug('default %s', res.statusCode);

        // respect err.status
        if (err.status) res.statusCode = err.status;

        // production gets a basic error message
        var msg = 'production' == env
          ? http.STATUS_CODES[res.statusCode]
          : err.stack || err.toString();

        // log to stderr in a non-test env
        if ('test' != env) console.error(err.stack || err.toString());
        if (res.headerSent) return req.socket.destroy();
        res.setHeader('Content-Type', 'text/plain');
        res.setHeader('Content-Length', Buffer.byteLength(msg));
        if ('HEAD' == req.method) return res.end();
        res.end(msg);
      } else {
        // 响应路由未找到，打印404
        debug('default 404');
        res.statusCode = 404;
        res.setHeader('Content-Type', 'text/plain');
        if ('HEAD' == req.method) return res.end();
        res.end('Cannot ' + utils.escape(req.method) + ' ' + utils.escape(req.originalUrl));
      }
      return;
    }

    try {
      path = utils.parseUrl(req).pathname;
      if (undefined == path) path = '/';

      // skip this layer if the route doesn't match.
      // 如果当前访问的 path 和路由不匹配则把错误带入下一个 next 继续执行
      if (0 != path.toLowerCase().indexOf(layer.route.toLowerCase())) return next(err);
      // 同上
      c = path[layer.route.length];
      if (c && '/' != c && '.' != c) return next(err);

      // Call the layer handler
      // Trim off the part of the url that matches the route
      // 获取路由外的 url
      removed = layer.route;
      req.url = req.url.substr(removed.length);

      // Ensure leading slash
      // 在 url 后添加 /
      if (!fqdn && '/' != req.url[0]) {
        req.url = '/' + req.url;
        slashAdded = true;
      }

      debug('%s %s : %s', layer.handle.name || 'anonymous', layer.route, req.originalUrl);
      var arity = layer.handle.length;
      // 根据处理函数的参数数量，传递不同的参数
      if (err) {
        if (arity === 4) {
          layer.handle(err, req, res, next);
        } else {
          next(err);
        }
      } else if (arity < 4) {
        layer.handle(req, res, next);
      } else {
        next();
      }
    } catch (e) {
      next(e);
    }
  }
  next();
};

/**
 * Listen for connections.
 *
 * This method takes the same arguments
 * as node's `http.Server#listen()`.
 *
 * HTTP and HTTPS:
 *
 * If you run your application both as HTTP
 * and HTTPS you may wrap them individually,
 * since your Connect "server" is really just
 * a JavaScript `Function`.
 *
 *      var connect = require('connect')
 *        , http = require('http')
 *        , https = require('https');
 *
 *      var app = connect();
 *
 *      http.createServer(app).listen(80);
 *      https.createServer(options, app).listen(443);
 *
 * @return {http.Server}
 * @api public
 */
// 让 connect 拥有自带创建服务
app.listen = function(){
  var server = http.createServer(this);
  return server.listen.apply(server, arguments);
};

```
上面的只是对 Connect 进行一个浅析的解剖，如果再要深入的话，需要对 node.js 封装的 http 模块进行解析。如有错误欢迎斧正。
