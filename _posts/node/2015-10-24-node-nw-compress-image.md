---
layout:     post
title:      使用nw.js完成一个PC端的图片压缩程序
summary:    nw.js原名Node Webkit，是一个跨平台创建桌面应用程序框架。使用语言是HTML、CSS、javascript、node
categories: node
tags:       [nw,nw.js]
---

> nw.js原名Node Webkit，是一个跨平台创建桌面应用程序框架。使用语言是HTML、CSS、javascript、node

文章所涵盖内容

* nw.js 基本使用方法

* node 压缩图片

* 打包（没法讲啊，我用osx打包后程序无法正常使用，如果你们是Win可以试试啊。）

开始前来看看完成后的效果
![图片转换](/resource/node-image-compress/demo.gif)

遗憾的是图片只压缩了4kb，压缩效率还是有点不满意呀！

### 第一步，安装基础环境
创建一个文件夹nw，在nw文件夹中创建2个文件夹css和js，再创建2个文件index.html和package.json。

package.json 内容如下
{% highlight javascript %}
{
    "name": "compress-images",
    "version": "0.0.1",
    "main": "index.html",
    "dependencies": {
        "imagemin": "~3.2.2",
        "gulp-rename": "~1.2.2"
    },
    "window": {
        "title": "图片压缩工具",
        "toolbar": true,
        "width": 300,
        "height": 300
    }
}
{% endhighlight %}
* main 是程序起始页面
* dependencies 是我们程序所要依赖的包，这里就是图片压缩的包
* window 是程序的属性

安装包

* <b>npm install -g nw</b> 安装nw的包，安装模式为全局的，这样我们可以在控制台中调用。还有就是这个安装过程要耐心和多试几次。哎，你懂

* <b>npm install</b> 安装我们package所依赖的包

### 第二步，搭建界面
先把html的布局代码写好，html代码如下
{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图片压缩工具</title>
    <link rel="stylesheet" href="./css/base.css">
</head>
<body>
    <div class="file-box" id="file-box">
        <p>拖动文件到这里开始压缩</p>
    </div>
    <script src="./js/app.js"></script>
</body>
</html>
{% endhighlight %}

html中引入和base.css和app.js，同时要创建他们。

base.css代码如下，其中haveFile是拖动文件后高亮的样式
{% highlight css %}
* {
    margin: 0;
    padding: 0;
}
.file-box {
    width: 200px;
    height: 200px;
    margin: 0 auto;
    margin-top: 50px;
    text-align: center;
    line-height: 200px;
    border-radius: 20px;
    border: 2px dashed black;
}
.file-box.haveFile {
    background: rgba(145, 183, 246, 0.5);
}
{% endhighlight %}

js代码先不写，我们让程序跑起来。你的控制台应该在当前目录中，在控制台中执行 <b>nw</b> ，系统会自动打开程序，就像下图
![图片转换](/resource/node-image-compress/demo1.png)

### 第三步，编写拖动和压缩相关的代码

先来完成拖动的代码，并且得到拖动的图片文件。代码如下
{% highlight javascript %}
window.onload = function() {
    var fileBox = document.getElementById('file-box');

    // 拖动进入目标
    fileBox.ondragenter = function() {
        fileBox.className = 'file-box haveFile';
    };
    // 拖动离开目标
    fileBox.ondragleave = function() {
        fileBox.className = 'file-box';
    };
    // 拖动中，这里必须阻止事件
    fileBox.ondragover = function(e) {
        e.preventDefault();
        return true;
    };
    // 拖动完成
    fileBox.ondrop = function(e) {
        e.stopPropagation();
        e.preventDefault();
        var files = e.dataTransfer.files;
        console.log(files);
        fileBox.className = 'file-box';
        return true;
    }
}
{% endhighlight %}
这里获取了ID为file-box的元素并且给他增加了HTML5拖动事件。当拖动文件在file-box元素上时，file-box会高亮背景。然后再拖动完成时，输出了拖动进来的文件

这里可以执行 <b>nw</b> 命令拖动一个图片试试效果。程序右上角有调试工具

接着增加一个处理图片压缩的函数，在这个函数里进行检测文件是否是图片，和图片的压缩<b class="red">我省略了代码，只展示不同的代码</b>
{% highlight javascript %}
window.onload = function() {
    // ...
    // 拖动完成
    fileBox.ondrop = function(e) {
        e.stopPropagation();
        e.preventDefault();
        var files = e.dataTransfer.files;
        handleImage(files);
        fileBox.className = 'file-box';
        return true;
    }
    // 处理图片
    function handleImage(files) {
        // 检查拖动进来的是否都是图片
        if(!checkImage(files)) {
            return false;
        }
        zip(files);
    }
    function checkImage(files) {
        for(var i = 0; i < files.length; i++) {
            if(!/(gif)|(jpg)|(png)|(svg)|(jpeg)/.test(files[i].type)) {
                return false;
            }
        }
        return true;
    }
}
{% endhighlight %}

要压缩图片需要把图片压缩库加载进来，然后调用他进行压缩。

在window.onload上面调用imagemin和gulp-rename库。

* imagemin压缩图片

* gulp-rename 重命名文件

代码如下
{% highlight javascript %}
var Imagemin = require('imagemin');
var rename = require('gulp-rename');
window.onload = function() {
    // ...
}
{% endhighlight %}

接着来实现zip函数，代码如下
{% highlight javascript %}
window.onload = function() {
  // ...
  function zip(files) {
      for(var i = 0; i < files.length; i++) {
          var name = (files[i].name).split('.');
          var extName = name.pop();
          var dir = (files[i].path).split('/');

          dir.pop();
          dir = dir.join('/');
          name = name.join('.') + '_compress_.' + extName;

          new Imagemin()
              .src(files[i].path)
              .use(rename(name))
              .dest(dir)
              .use(Imagemin.jpegtran({progressive: true}))
              .run(function (err, files) {
                  console.log(files);
                  // => {path: 'build/images/foo.jpg', contents: <Buffer 89 50 4e ...>}
              });
      }
  }
  // ...  
};
{% endhighlight %}
他会对拖动进来的图片压缩，压缩后会当然目录生成新图片并且重命名为 filename + _compress_.jpg

再次的执行我们熟悉的 <b>nw</b> 命令，拖动一张图片去试试吧，非常有意思。

关于打包：其实土豆是打包成功了的，但是打出来的包无法正常使用。如果大家感兴趣可以去看看[nw-builder](https://github.com/nwjs/nw-builder)这个打包程序，如果你打包的程序没问题不妨告诉我哈。

总结：

我们使用了node的图形压缩库和nw创建一个桌面程序，这个程序说实话并没有实用价值，都是研究性的。当然如果感兴趣的同学可以继续研究学习下去，打造更具实用性的桌面程序，比如有道词典。祝大家好运 :)

如今移动端有像React Native这种框架，桌面端由nw.js这种框架，现在javascript要一统宇宙的感觉呀！！！
