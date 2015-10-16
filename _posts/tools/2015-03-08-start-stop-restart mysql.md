---
layout:     post
title:      启动，停止，重启mysql
summary:    start,stop,restart mysql 在 Linux 或者 OSX 命令行工具中
categories: tools
tags:       [start mysql mac linux]
---


{% highlight javascript %}
Linux 中
/etc/init.d/mysqld start
/etc/init.d/mysqld stop
/etc/init.d/mysqld restart
//其他Linux提供命令服务的启动方法
service mysqld start
service mysqld stop
service mysqld restart
或者
service mysql start
service mysql stop
service mysql restart

OSX 中
sudo /usr/local/mysql/support-files/mysql.server start
sudo /usr/local/mysql/support-files/mysql.server stop
sudo /usr/local/mysql/support-files/mysql.server restart
{% endhighlight %}
