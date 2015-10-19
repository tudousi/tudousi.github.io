---
layout:     post
title:      使用node发送email
summary:    使用node发送email，我们使用Nodemailer模块。他是一个简单快捷发送email的模块
categories: node
tags:       [node发送邮件,node email]
---

## 使用node发送email，我们使用[Nodemailer](https://www.npmjs.com/package/nodemailer)模块。他是一个简单快捷发送email的模块


所涵盖内容：

1. 发送邮件
2. 发送抄送和密送
3. 发送附件

### 初始化项目

创建一个文件夹，有如下文件。

* server.js
* package.json

package.json 内容如下
{% highlight javascript %}
{
    "name": "send-email-serve",
    "dependencies": {
        "nodemailer": "^1.8.0",
        "nodemailer-wellknown": "^0.1.7"
    }
}
{% endhighlight %}

在这个目录我们执行命令 npm install 去安装依赖的模块。
nodemailer模块是我们要发送email所用到的模块，nodemailer-wellknown模块包含各个邮件服务器配置模块，后面我们会用到的。

### 引入模块
把我们需要依赖的模块先调用进来
{% highlight javascript %}
//（server.js）
var nodemailer = require('nodemailer');
var wellknown = require('nodemailer-wellknown');
{% endhighlight %}

### 配置邮件服务器
这里就是用QQ作为例子吧，这里user就填写你的QQ账号，pass填写QQ密码。这里的wellknown会创建一个邮件服务器的[配置](https://github.com/andris9/nodemailer-wellknown/blob/master/services.json)。
{% highlight javascript %}
// server.js
var config = wellknown('qq');
config.service = 'qq';
config.auth = {
    user: 'user',
    pass: 'pass',
};
{% endhighlight %}

### 创建SMTP传输协议
传入我们的配置文件对象，会得到一个SMTP对象
{% highlight javascript %}
// server.js
var transporter = nodemailer.createTransport(config);
{% endhighlight %}

### 配置发送邮件的信息
这里比较有意思，你要发送所有的邮件相关信息都会在这里配置。我们来看看。
{% highlight javascript %}
// server.js
var mailOptions = {
    from: 'easyform ✔ <41871879@qq.com>',       // 发件人地址
    to: 'easyform_oops@163.com',                // 接受者地址列表
    subject: 'Hi,i\'m tudou',                   // 电子邮件的主题
    html: '<b>Hello, World! i\'m tudou </b>',   // html body
};
{% endhighlight %}
这里需要注意的时from的地址必须和user地址相同，我们这里就准备给easyform_oops@163.com发送一份邮件。这里我是用的时html格式，你也可以是用普通文本格式(text:'')

### 发送邮件
关键的就来了，我们这里调用了sendMail方法发送了一份邮件，第一个参数是我们上面配置好的邮件属性，第二个就是回调函数。
{% highlight javascript %}
// server.js
transporter.sendMail(mailOptions, function(error, info) {
    if (error) {
        return console.log(err);
    }
    console.log('Message sent: ' + info.response);
});
{% endhighlight %}
关于回调函数中得info信息，大家可以看[这里](https://github.com/andris9/Nodemailer#sending-mail)

### 发送邮件
在你当前代码所在的目录执行命令 node server.js 如果你的配置没有问题，将会看到如下信息
{% highlight javascript %}
Message sent: 250 Ok: queued as
{% endhighlight %}
这就代表你的邮件发送成功。

### 抄送和密送
下面我们来看下抄送和密送如何发送，其实很简单，只需要你在mailOptions配置好相关参数即可，在mailOptions配置中增加如下2个属性。
{% highlight javascript %}
var mailOptions = {
    // ......
    cc: ['email address'],  // 抄送，接收该邮件的副本的收件人
    bcc: ['email address'], // 密送，收件人不出现在邮件头中
}
{% endhighlight %}
他们也可以定义多个和不通的格式，具体请看[Address Formatting](https://github.com/andris9/Nodemailer#address-formatting)

### 附件
你想要发送附件，天啊，简单到发指。通用也是修改mailOptions配置。
{% highlight javascript %}
var mailOptions = {
    // ......
    attachments: [{
        path: 'C:/coupon-4.png',
    }]
}
{% endhighlight %}
这里我使用了最简单的参数发送了c盘coupon-4.png这样一个图片附件。他们的调用格式还有很多种。具体请看[attachments](https://github.com/andris9/Nodemailer#attachments)

### 总结
使用nodemailer发送邮件还是蛮简单的，我们只不过调用了几个模块而已，不过如果你希望发送大量邮件官方推荐使用[nodemailer-smtp-pool](https://github.com/andris9/nodemailer-smtp-pool)这个库，不过我想说一句163我测试发个几十封，就把我给干掉了。这个还是要根据每个邮箱服务器不同而定。
