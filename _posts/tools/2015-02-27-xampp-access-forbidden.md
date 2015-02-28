---
layout: post
title: XAMPP 访问 Access forbidden 问题
tags: [tools]
---

当安装好 XAMPP 用 IP 进行访问的时候会出现以下的提示

```javascript
Access forbidden!
--------------------------------------------------------------------------------

New XAMPP security concept:

Access to the requested object is only available from the local network.

This setting can be configured in the file "httpd-xampp.conf".
```

```javascript
根据提示，修改 httpd-xampp.conf 即可
#
# New XAMPP security concept
#
<LocationMatch "^/(?i:(?:xampp|security|licenses|phpmyadmin|webalizer|server-status|server-info))">
    Require local
  ErrorDocument 403 /error/XAMPP_FORBIDDEN.html.var
</LocationMatch>

修改为

#
# New XAMPP security concept
#
<LocationMatch "^/(?i:(?:xampp|security|licenses|phpmyadmin|webalizer|server-status|server-info))">
    #Require local
    Options All
    AllowOverride All
    Order deny,allow
    Allow from all
  ErrorDocument 403 /error/XAMPP_FORBIDDEN.html.var
</LocationMatch>
```

如果出现

```javascript
Access forbidden!  You don’t have permission to access the requested object. It is either read-protected or not readable by the server.
```
这时可以找到apache的httpd.conf文件，找出<Directory />
修改成如下：

```javascript
<Directory />
    Options All
    AllowOverride All
    Order deny,allow
    Allow from all
</Directory>
// mac执行 sudo chmod -R 777 /Applications/XAMPP/xamppfiles/htdocs
```


重启服务即可