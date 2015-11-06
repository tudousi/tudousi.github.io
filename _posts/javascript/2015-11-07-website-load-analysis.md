---
layout:     post
title:      网络加载的自我反思
summary:    双11，恩，电商们也是打的火热。不理他们，反正也没钱买，就来分析下他们页面如何优化的吧。重要的是自我思考，我们该怎么做。
categories: javascript
tags:       [analysis,website analysis]
---

> 今年的电商大战那是一个热闹，从实名举报到口水大战互相sibi好不热闹。。对于我这种穷人只能呵呵笑笑，继续工作哈。买不起总的干点什么吧？我就想到了分析他们如何处理和优化网站加载速度的。先说我是切图的，技术不牛x，更不是大神。下文只是我自己的理解，如果误导你了来成都土豆请你吃饭。

下文只要我说到"分析"，您可以理解成"扯淡" :P

这里我就只拿了天猫和京东来分析。

[天猫主会场](https://www.tmall.com/wow/act/14700/pre20151111?spm=875.7789098.2015002.1.FKsh8b&acm=lb-zebra-17931-286929.1003.2.522430&aldid=0YTKbFQP&scm=1003.2.lb-zebra-17931-286929.ITEM_1444720109175_522430&pos=1)

[京东主会场](http://sale.jd.com/act/M7J62hoy1vugS.html?cpdad=1DLSUE)

如果你打开天猫的主会场，你会惊叹。"哇，太快了"。对的天猫1s左右就能"load"完毕。为啥"load"打上引号捏？因为他是懒加载。默认进来只是展示首屏的资源。

如果打开京东的主会场，你会感叹。"什么鬼，我网络又出问题了吗？"。其实不是，只要是京东的图片资源加载很有问题。

京东的网站因为图片请求资源问题导致整个网站长达恐怖的1m时间整个网页还没load完毕。:S 整个人都不好了。

下面来分析对比他们2家网站。

1. 加载速度

<b>从加载首屏来看，京东大概是2s-3s能正常展示首屏，天猫是1s。</b>

![京东DomLoad](/resource/load-analysis/jd-domcontentloaded.png) 京东DOMContentLoaded速度
![天猫DomLoad](/resource/load-analysis/tm-domcontentloaded.png) 天猫DOMContentLoaded速度

明显京东比天猫慢了一半，为什么？不妨打开京东和天猫的源代码看一下。京东到body结尾是4062行代码，天猫则是1146行代码。明白了吧。肯定不能全部归咎于代码多少，还涉及到资源的加载。

接着来看看load(window.onload)加载速度。天猫是1.8s左右，京东，因为图片资源问题1m+开外，有时候可能根本不会触发。所以如果你在京东工作，想要写这样的代码<b>window.onload</b>肯定是不好使的。

2. 分析完基本的加载。可以再来分析他们的资源加载的策略

先上天猫的，资源加载如下图

![tm](/resource/load-analysis/tm-res-load.png)

总是感觉很屌，截止到DOMContentLoaded所有的资源加载总数加起来也不过250kb（我加过），主要加载的资源就是：首页html、一个reset css文件、kissy js文件、index js文件、af_v1 js、gccpm js和必须得图片资源。这是这些资源都是当前网页必要的东西。后面的所有资源都是通过刚刚那个kissy里面的getScript函数动态创建标签加载的。

可以再看下面的图

![tm](/resource/load-analysis/tm-res-load-1.png)

右边的timeline红框处是DOM加载完后通过kissy又加载了很多js。这些资源加载是不会影响页面展示的。所以天猫控制的很好。

图片资源，天猫也都是懒加载，图片也控制的很好，图片不大超过30kb的都很少。CDN也相当给力每张图片的加载时间基本不超过50ms。


3. 再来看看京东的。先上个DOMContentLoaded和load的图

![tm](/resource/load-analysis/jd-res-load.png)

:S 写不动了。睡觉明天有空再填坑。

总结：

先把总结写了，不然又忘了。想要网页加载快体验好，我理解就是资源的控制。首先html代码要控制压缩和gzip。但是最重要的是资源的加载，同步加载的资源能少则少。反正就是首屏的资源从加载到渲染最好不要好过3s是最好的感觉。

其他的资源或者代码就让DOMContentLoaded后去加载吧。

图片资源一定一定要控制越小越好很重要。

在这个发展这么快用户耐心越来越少的年代。体验很重要。

自我反思。在我的工作中就没处理好这些图片资源。有些png图片大到800kb 3s才加载完毕，加上渲染都4s开外了。我会加班处理好的。虽然做不到想他们那样好，但是起码做到我的最好吧，哈哈。


、、
