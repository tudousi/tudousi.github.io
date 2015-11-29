---
layout:     post
title:      重写UMeditor图片上传功能
summary:    UMeditor编辑器上传图片功能只能支持php、asp、.net和jsp。但是项目需要node版本的所以我写了个node版本的。
categories: node
tags:       [UMeditor node 图片上传, UMeditor node]
---

这个是基于Express 4.x 和 multer 文件上传模块，给有需要的朋友参考哈。代码如下

{% highlight javascript %}
// routes/upload.js
var express = require('express');
var multer = require('multer');
var fs = require('fs');
var router = express.Router();

// 其他信息配置
var uploadDir = 'static/upload/';               // 文件上传路径
// 如果今天为目录名，不存在则要创建
var today = new Date();
var staticUrl = 'http://static.mysite.com/';    // 静态资源服务器地址

today = '' + today.getFullYear() + (today.getMonth()+1) + today.getDate();
uploadDir += today;
fs.exists(uploadDir, function(exists) {
    if(!exists) {
        fs.mkdir(uploadDir);
    }
});

// 配置文件上传
var storage = multer.diskStorage({
    destination: function(req, file, cb) {
        cb(null, uploadDir)
    },
    filename: function(req, file, cb) {
        // 取得文件后缀
        var extName = file.originalname.split('.');
        extName = extName[1];
        // 用上传的文件命名文件
        cb(null, getRandomName(12) + (+new Date()) + '.' + extName);
    }
})
// 创建上传文件函数
var uploadConfig = multer({
    storage: storage
});

// 上传文件
router.post('/uploadPackageImg', uploadConfig.array('upfile'), function(req, res, next) {
    /**
        { fieldname: 'upfile',
          originalname: '1FD6D2C6-A7F3-4651-901F-323E25965F2C.png',
          encoding: '7bit',
          mimetype: 'image/png',
          destination: 'static/upload/20151129',
          filename: 'pxnhyuyjuhzt1448812168507.png',
          path: 'static/upload/20151129/pxnhyuyjuhzt1448812168507.png',
          size: 58430 }
    */
    var file = req.files[0];
    var type = file.filename.split('.');
    var virtualDir = file.destination.split('/');          // 需要修正路径，因为我用nginx代理到static了，则需要剔除掉static目录
    virtualDir.shift();
    virtualDir = virtualDir.join('/');
    var json = {
        originalName: file.originalname,
        name: file.filename,
        url: staticUrl + virtualDir + '/' + file.filename,
        size: file.size,
        type: '.' + type[1],
        state: 'SUCCESS'
    };
    res.send(JSON.stringify(json));
});


function getRandomName(n) {
    var chars = ['a','b','c','d','e','f','g','h','i','j','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z'];
    var length = 26;
    var res = "";
     for(var i = 0; i < n ; i ++) {
         var id = Math.ceil(Math.random() * length);
         res += chars[id];
     }
     return res;
}

module.exports = router;
{% endhighlight %}


app.js调用代码，绑定路由。


{% highlight javascript %}
// 完整调用路径是：www.yourdomain.com/upload/uploadPackageImg
var edit = require('./routes/upload');            // 编辑
app.use('/upload', edit);
{% endhighlight %}


UMeditor编辑器配置如下
{% highlight javascript %}
// 在141行处
,imageUrl:'/upload/uploadPackageImg'           //图片上传提交地址
,imagePath:''                     //图片修正地址，引用了fixedImagePath,如有特殊需求，可自行配置
{% endhighlight %}

完成后的效果

![uploadimg](/resource/ue-upload/uploadimg.gif)

最后如果有啥配置有问题的可以Email给我哈。
