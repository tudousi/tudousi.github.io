---
layout:     post
title:      Node与PHP通信，打造一个ICON转换工具
summary:    使用Node进程通信去调用PHP程序完成一个图片转换工具。
categories: node
tags:       [node和php通信,icon转换工具]
---

### 本文所涵盖的内容
* PHP把图片转换为.ico格式
* Node调用PHP协作完成图片转换

所需要的环境 1、PHP&nbsp;&nbsp;2、Node

### PHP命令行调用
在你的文件夹下面新建一个demo.php并输入如下内容
{% highlight php %}
<?php
print('hello world!');
?>
{% endhighlight %}
将控制台中得路径切换到当前demo.php所在目录并且执行命令 <b>php demo.php</b>，你将会看到输出 hello world!。

### PHP图片格式转换
要用PHP将图片转换为.ico格式我们需要使用到一个库[php-ico](https://github.com/chrisbliss18/php-ico)。将这个库下载下来并且解压，得到解压文件<b>class-php-ico.php</b>将他放在我们的demo.php目录中，然后我们新建一个icon.php文件，代码如下
{% highlight php %}
<?php
require( dirname( __FILE__ ) . '/class-php-ico.php' );  // 调用ico库

$source = dirname( __FILE__ ) . '/tudou.png';           // 待转换的图片路径
$destination = dirname( __FILE__ ) . '/tudou.ico';      // 转化后的ico图片路径

$ico_lib = new PHP_ICO( $source, array(array(64,64)) ); // 转换图片
$ico_lib->save_ico( $destination );                     // 存储图片
?>
{% endhighlight %}
我们在icon.php文件夹中执行命令 <b>php icon.php</b>后可以在该目录看到一个生成好的.ico文件了。像下面图片一样，是不是很酷。<br>
![转换后ico图片](/resource/node-php-ico/img1.png)

### Node调用PHP完成图片转换
我们完成了PHP得图片转换，接下来我们就得考虑如何使用Node调用PHP完成这样的工作了。

Node为我们提供了调用外部进程的命令，那就是 <b>child_process</b>

这里就简单说下<b>child_process</b>里的一个函数<b>exec</b>，他的作用是在 shell 中执行一个命令并缓冲输出。第一个参数是字符串代表你要执行的shell命令，第二个参数是一个回调函数，当命令结束或者发生错误是调用。

回调函数有3个参数，第一个是Error对象实例，如果不包含错误将为null，第二个参数是shell执行完的输出信息，第三个参数是命令错误输出信息。

这里我们创建icon.js文件，引入child_process然后调用exec去调用shell执行我们的PHP代码进行图片格式的转换
{% highlight javascript %}
// icon.js
var exec = require('child_process').exec;
exec('php icon.php', function(err, stdout, stderr){
    console.log(stdout);
});
{% endhighlight %}
在控制台中运行 <b>node icon.js</b> 非常酷的一张.ico图片就出来了。

我们再对PHP程序进行一下改造让Node回调函数能够获取到我们转换后.ico图片的路径，这样我们的程序才有实用价值。把PHP代码增加一行打印出转换后的.ico路径
{% highlight php %}
<?php
require( dirname( __FILE__ ) . '/class-php-ico.php' );  // 调用ico库

$source = dirname( __FILE__ ) . '/tudou.png';           // 待转换的图片路径
$destination = dirname( __FILE__ ) . '/tudou.ico';      // 转化后的ico图片路径

$ico_lib = new PHP_ICO( $source, array(array(64,64)) ); // 转换图片
$ico_lib->save_ico( $destination );                     // 存储图片
// 新增代码 打印出.ico路径，这样Node回调函数就能捕获了
echo $destination;
?>
{% endhighlight %}

再次执行<b>node icon.js</b>，能看到控制台输出了.ico的完整路径。我们可以将他移动到我们的Apache目录等其他地方，供我们使用了。怎么样是不是很酷。
{% highlight javascript %}
tudoudeMacBook-Pro:php tudou$ node icon.js
/Users/tudou/Desktop/php/tudou.ico
tudoudeMacBook-Pro:php tudou$
{% endhighlight %}

### 总结：

<span class="red">我们为什么要费尽周折的这样去调用PHP程序完成图片格式转换捏？</span>

其实在Node中，对图片的操作很有限。而PHP经过这么多年的发展，已经有许多对图片操作的库我们可以调用，当Node某些方面不足时我们就能通过和其他程序和合作完成我们想要的功能。你是不是也想到了更多的注意 :)
