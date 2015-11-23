---
layout:     post
title:      nginx反向代理node服务
summary:    nginx反向代理node服务
categories: node
tags:       [nginx反向代理node, node反向代理]
---

使用nginx做代理可以做负载均衡、静态文件处理、请求日志、压缩等工作，好处还是蛮多的。下面记录下我的搭建过程。

我使用的环境：

* 本地环境Mac+vagrant

* vagrant里面安装的是Ubuntu x64

### 步骤就像下面一步一步

首先在需要在Ubuntu中安装好node和nginx。node我使用的是12的源码安装的，nginx使用apt-get install nginx(安装完毕默认启动)。2个软件都安装完毕后需要对nginx做一些配置

{% highlight javascript %}
cd /etc/nginx/sites-available/
sudo touch yourdomain           # 创建配置文件，这里我使用createthink
sudo vim createthink            # 编辑nginx相关的配置
{% endhighlight %}

我的createthink配置文件

{% highlight javascript %}
// node 运行在本地3000端口中
upstream app_mysite {
    server 127.0.0.1:3000;
    keepalive 8;
}
server {
    listen 0.0.0.0:80;
    server_name mysite.com mysite;
    access_log /var/log/nginx/mysite.log;

    # 请求头传递给node可以添加更多的头，详细请查看nginx文档
    location / {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_set_header X-NginX-Proxy true;

      proxy_pass http://app_mysite/;
      proxy_redirect off;
    }
 }
{% endhighlight %}

设置完成后，你需要启用在文件中的配置

{% highlight javascript %}
cd /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/yourdomain yourdomain
# 我的是这样的
# ln -s /etc/nginx/sites-available/createthink createthink
{% endhighlight %}

重新启动nginx

{% highlight javascript %}
sudo /etc/init.d/nginx restart
{% endhighlight %}

在你的桌面创建一个testServe.js文件

{% highlight javascript %}
var http = require('http');

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World\n');
}).listen(3000, "127.0.0.1");
console.log('Server running at http://127.0.0.1:3000/');
{% endhighlight %}

启动node程序

{% highlight javascript %}
node testServe.js
{% endhighlight %}

接着配置hosts映射

{% highlight javascript %}
cd /etc
sudo vim hosts
# 增加一段对站点域名的映射，我的映射如下
# 192.168.10.10   mysite.com
{% endhighlight %}

保存退出，我们在浏览器访问下 mysite.com

![nginx-proxy-node](/resource/nginx-proxy-node/nginx-proxy-node.png)

截止目前，一个基本的nginx反向代理就完成了。
