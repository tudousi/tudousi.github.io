---
layout:     post
title:      SVN 命令索引
summary:    SVN 命令索引
categories: tools
tags:       [SVN命令]
---

> SVN 命令介绍
命令索引

* [svn checkout](#svncheckout)
* [svn add](#svnadd)
* [svn blam](#svnblam)
* [svn cat](#svncat)
* [svn changelist](#svnchangelist)
* [svn cleanup](#svncleanup)
* [svn commit](#svncommit)
* [svn copy](#svncopy)
* [svn delete](#svndelete)
* [svn diff](#svndiff)
* [svn export](#svnexport)
* [svn import](#svnimport)
* [svn info](#svninfo)
* [svn lock](#svnlock)
* [svn log](#svnlog)
* [svn merge](#svnmerge)
* [svn mergeinfo](#svnmergeinfo)
* [svn mkdir](#svnmkdir)
* [svn move](#svnmove)
* [svn propdel](#svnpropdel)
* [svn propedit](#svnpropedit)
* [svn propget](#svnpropget)
* [svn proplist](#svnproplist)
* [svn propset](#svnpropset)
* [svn resolve](#svnresolve)
* [svn resolved](#svnresolved)
* [svn revert](#svnrevert)
* [svn status](#svnstatus)
* [svn switch](#svnswitch)
* [svn unlock](#svnunlock)
* [svn update](#svnupdate)


<h4 id="svncheckout">svn checkout 从版本库签出工作副本</h4>

{% highlight javascript %}
checkout (co): 从版本库签出工作副本。
使用: checkout URL[@REV]... [PATH]

  如果指定 REV，那么它确定了从 URL 首先查找的版本。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --force                  : 强制操作运行
  --ignore-externals       : 忽略外部项目

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.取出一个工作拷贝到mine目录：<br>
$ svn checkout file:///tmp/repos/test mine

2.检出两个目录到两个单独的工作拷贝：<br>
$ svn checkout file:///tmp/repos/test file:///tmp/repos/quiz

3.检出两个目录到两个单独的工作拷贝，但是将两个目录都放到working-copies：<br>
$ svn checkout file:///tmp/repos/test file:///tmp/repos/quiz working-copies

<h4 id="svnadd">svn add 添加到版本库</h4>

{% highlight javascript %}
add: 把文件和目录纳入版本控制，通过调度加到版本库。它们会在下一次提交时加入。
用法: add 路径...

有效选项:
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --force                  : 强制操作运行
  --no-ignore              : 忽略默认值和 svn:ignore 属性
  --auto-props             : 启用自动属性
  --no-auto-props          : 关闭自动属性
  --parents                : 增加中间父目录

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.添加一个文件到工作拷贝:<br>
$ svn add foo.c

2.当添加一个目录，svn add缺省的行为方式是递归的：<br>
$ svn add testdir

3.你可以只添加一个目录而不包括其内容：<br>
$ svn add –non-recursive otherdir<br>
$ svn add -N otherdir

4.通常情况下，命令svn add *会忽略所有已经在版本控制之下的目录，有时候，你会希望添加所有工作拷贝的未版本化文件，包括那些隐藏在深处的文件，可以使用svn add的–force递归到版本化的目录下：<br>
$ svn add * –force

<h4 id="svnblam">svn blam 追溯内容</h4>

{% highlight javascript %}
blame (praise, annotate, ann): 输出指定文件或URL的追溯内容，包含版本和作者信息。

用法: blame 目标[@版本]...
      如果指定了版本，将从指定的版本开始查找。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -v [--verbose]           : 打印附加信息
  -g [--use-merge-history] : 从合并历史使用/显示额外信息
  --incremental            : 给予适合串联的输出
  --xml                    : 输出为 XML
  -x [--extensions] ARG    : 缺省: “-u”。当 Subversion 调用外部比较程序时，ARG 直接传给它。但是当
                             Subversion 使用缺省的内置比较实现，或者正
                             显示追溯时, ARG 可以是:
                                -u (--unified):
                                   输出三行统一上下文。
                                -b (--ignore-space-change):
                                   忽略空白数量的修改。
                                -w (--ignore-all-space):
                                   忽略所有的空白。
                                --ignore-eol-style:
                                   忽略行尾样式的改变。                            -p (--show-c-function):
                                   在比较输出中显示 C 函数名称。
  --force                  : 强制操作运行

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.如果你希望在测试版本库看到blame标记的readme.txt源代码：<br>
$ svn blame http://svn.red-bean.com/repos/test/readme.txt

<h4 id="svncat">svn cat 输出文件或内容</h4>

{% highlight javascript %}
cat: 输出指定文件或URL的内容。
用法: cat 目标[@版本]...

      如果指定了版本，将从指定的版本开始查找。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}`
常用操作<br>
1.如果你希望不检出而察看版本库的readme.txt的内容：<br>
$ svn cat foo.c

<h4 id="svnchangelist">svn changelist 耦合文件与修改列表</h4>

{% highlight javascript %}
changelist (cl): 耦合(或解耦)文件与修改列表 CLNAME。
使用: 1. changelist CLNAME TARGET...
      2. changelist --remove TARGET...

有效选项:
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --remove                 : 删除修改列表耦合
  --targets ARG            : 传递文件 ARG 内容为附件参数
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}

<h4 id="svncleanup">svn cleanup 递归清理工作副本</h4>

{% highlight javascript %}
cleanup: 递归清理工作副本，删除锁，继续未完成操作，等等。

用法: cleanup [路径...]

有效选项:
  --diff3-cmd ARG          : 使用 ARG 作为合并命令

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.svn cleanup没有输出，没有太多的例子，如果你没有传递PATH，会使用“.”。<br>
$ svn cleanup<br>
$ svn cleanup /path/to/working-copy

<h4 id="svncommit">svn commit 提交到版本库</h4>

{% highlight javascript %}
commit (ci): 把工作副本的修改提交到版本库。
用法: commit [路径...]

     必须提供日志消息，但它可以是空的。如果没有指定 --message 或 --file
     选项，编辑器会被启动。如果有目标是锁定的或者包含锁定条目，则在成功
     提交后会被解锁。

有效选项:
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --targets ARG            : 传递文件 ARG 内容为附件参数
  --no-unlock              : 不要解锁目标
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]
  --keep-changelists       : 不要在提交后删除修改列表

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.使用命令行提交一个包含日志信息的文件修改，当前目录（“.”）是没有说明的目标路径：<br>
$ svn commit -m “added howto section.”

2.提交一个修改到foo.c（在命令行明确指明），并且msg文件中保存了提交信息：<br>
$ svn commit -F msg foo.c

3.如果你希望使用在–file选项中使用在版本控制之下的文件作为参数，你需要使用–force-log选项：<br>
$ svn commit –file file_under_vc.txt foo.c

4.提交一个已经预定要删除的文件：<br>
$ svn commit -m “removed file ‘c’.”

<h4 id="svncopy">svn copy 版本库中复制数据</h4>

{% highlight javascript %}
copy (cp): 在工作副本或版本库中复制数据，保留历史。
用法: copy SRC[@REV]... DST

当复制多个源时，它们作为 DST 的子节点增加, DST 必须是目录。

  SRC 和 DST 可以是工作副本路径(WC)或版本库地址(URL):
    WC   -> WC:  复制和调度增加(包含历史)
    WC   -> URL: 立即提交工作副本到版本库
    URL  -> WC:  检出 URL 到工作副本，调度增加
    URL  -> URL: 完全服务器端复制；一般用于分支和标签
  所有 SRC 必须是同一类型。

警告：为了与旧版本的 Subversion 兼容，使用两个工作副本复制(WC -> WC)的
操作不会与版本库通信。同样地，在缺省情况下，不能从复制源到目的地传播合
并跟踪信息。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --ignore-externals       : 忽略外部项目
  --parents                : 创建中间目录
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.拷贝工作拷贝的一个项目（只是预定要拷贝—在提交之前不会影响版本库）：<br>
$ svn copy foo.txt bar.txt

2.拷贝你的工作拷贝的一个项目到版本库的URL（直接的提交，所以需要提供一个提交信息）：<br>
$ svn copy near.txt file:///tmp/repos/test/far-away.txt -m “Remote copy.”

3.拷贝版本库的一个项目到你的工作拷贝（只是预定要拷贝—在提交之前不会影响版本库）：<br>
$ svn copy file:///tmp/repos/test/far-away near-here

4.在URL之间拷贝：<br>
$ svn copy file:///tmp/repos/test/far-away file:///tmp/repos/test/over-there -m “remote copy.”

<h4 id="svndelete">svn delete 删除文件和目录</h4>

{% highlight javascript %}
delete (del, remove, rm): 从版本库中删除文件和目录。
用法: 1、delete PATH...
      2、delete URL...

  1、每个 PATH 指定的项目会被调度到下次提交时从版本库删除。除非给出
    --keep-local 参数，工作副本中没有提交的文件和目录会被立即删除。
    如果 PATH 是未版本控制或者已修改的项目，或者包含这些项目，那么仅当
    给出 --force 参数时这些项目才会被删除。

  2、每个 URL 指定的项目会通过立即提交从版本库中删除。

有效选项:
  --force                  : 强制操作运行
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]
  --keep-local             : 在工作副本中保留路径

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.使用svn从工作拷贝删除文件只是预定要删除，当你提交，文件才会从版本库删除。<br>
$ svn delete myfile

2.然而直接删除一个URL，你需要提供一个日志信息：<br>
$ svn delete -m “Deleting file ‘yourfile’” file:///tmp/repos/test/yourfile

3.强制删除本地已修改文件的例子：<br>
$ svn delete –force over-there

<h4 id="svndiff">svn diff 对比查看</h4>

{% highlight javascript %}
diff (di): 显示两个版本或路径的差异。
用法: 1. diff [-c M | -r N[:M]] [TARGET[@REV]...]
      2. diff [-r N[:M]] --old=OLD-TGT[@OLDREV] [--new=NEW-TGT[@NEWREV]] \
               [PATH...]
       3. diff OLD-URL[@OLDREV] NEW-URL[@NEWREV]

  1、显示版本 REV 中 TARGET 在两个不同的版本之间的差异。TARGET 要么全是
     工作副本路径，要么全是 URL。如果 TARGET 是工作副本路径，N 默认为
     BASE，M 默认为工作副本；如果 TARGET 是 URL，N 必须指定， M 默认为 HEAD。
    “-c M” 等价于 “-r M-1:M”，“-c -M” 等价于 “-r M:M-1”。

  2、显示新旧版本中对应目标的差异。PATH 是相对于新旧目标的相对路径，
     它限制只输出这些路径上的差异。新旧目标可以是工作副本路径或地址 URL[@REV]。
     新目标默认与旧目标相同，OLDREV 默认为 N，NEWREV 默认为 M。

  3、“svn diff --old=OLD-URL[@OLDREV] --new=NEW-URL[@NEWREV]” 的简写。

     使用不加参数的 “svn diff” 显示工作副本中的本地修改。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -c [--change] ARG        : 在ARG版本(如同 -r ARG-1:ARG)作的修改
                             如果ARG为负数则等价于 -r ARG:ARG-1
  --old ARG                : 使用 ARG 作为旧目标
  --new ARG                : 使用 ARG 作为新目标
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --diff-cmd ARG           : 使用 ARG 作为比较命令
  -x [--extensions] ARG    : 缺省: “-u”。当 Subversion 调用外部比较程序时，ARG 直接传给它。但是当
                             Subversion 使用缺省的内置比较实现，或者正
                             显示追溯时, ARG 可以是:
                                -u (--unified):
                                   输出三行统一上下文。
                                -b (--ignore-space-change):
                                   忽略空白数量的修改。
                                -w (--ignore-all-space):
                                   忽略所有的空白。
                                --ignore-eol-style:
                                   忽略行尾样式的改变。                            -p (--show-c-function):
                                   在比较输出中显示 C 函数名称。
  --no-diff-deleted        : 不要输出删除文件造成的差异
  --notice-ancestry        : 比较差异时提示原始信息
  --summarize              : 显示结果的概要
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]
  --force                  : 强制操作运行
  --xml                    : 输出为 XML

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.比较BASE和你的工作拷贝（svn diff最经常的用法）：<br>
$ svn diff COMMITTERS

2.查看文件COMMITTERS在修订版本9115修改的内容：<br>
$ svn diff -c 9115 COMMITTERS

3.察看你的工作拷贝对旧的修订版本的修改：<br>
$ svn diff -r 3900 COMMITTERS

4.使用“@”语法与修订版本3000和35000比较：<br>
$ svn diff http://svn.collab.net/repos/svn/trunk/COMMITTERS@3000 http://svn.collab.net/repos/svn/trunk/COMMITTERS@3500

5.使用范围符号来比较修订版本3000和3500（在这种情况下只能传递一个URL）：<br>
$ svn diff -r 3000:3500 http://svn.collab.net/repos/svn/trunk/COMMITTERS

6.使用范围符号比较修订版本3000和3500trunk中的所有文件：<br>
$ svn diff -r 3000:3500 http://svn.collab.net/repos/svn/trunk

7.使用范围符号比较修订版本3000和3500trunk中的三个文件：<br>
$ svn diff -r 3000:3500 –old http://svn.collab.net/repos/svn/trunk COMMITTERS README HACKING

8.如果你有工作拷贝，你不必输入这么长的URL：<br>
$ svn diff -r 3000:3500 COMMITTERS

<h4 id="svnexport">svn export 导出</h4>

{% highlight javascript %}
export: 产生一个无版本控制的目录树副本。
用法: 1、export [-r REV] URL[@PEGREV] [PATH]
      2、export [-r REV] PATH1[@PEGREV] [PATH2]

  1、从 URL 指定的版本库，导出一个干净的目录树到 PATH。如果有指定
     REV 的话，内容即为该版本的，否则就是 HEAD 版本。如果 PATH
     被省略的话，URL的最后部份会被用来当成本地的目录名称。

  2、在工作副本中，从指定的 PATH1 导出一个干净的目录树到 PATH2。如果
     有指定 REV 的话，会从指定的版本导出，否则从工作副本导出。如果
     PATH2 被省略的话，PATH1 的最后部份会被用来当成本地的目录名称。
     如果没有指定 REV 的话，所有的本地修改都保留，但是未纳入版本控制
     的文件不会被复制。

     如果指定了 PEGREV ，将从指定的版本本开始查找。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --force                  : 强制操作运行
  --native-eol ARG         : 使用非标准的 EOL 标记
                系统中立的文件标记 svn:eol-style 属性取值为 “native”。
                ARG 可以是以下之一“LF”，“CR”，“CRLF”
  --ignore-externals       : 忽略外部项目

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.从你的工作拷贝导出（不会打印每一个文件和目录）：<br>
$ svn export a-wc my-export

2.从版本库导出目录（打印所有的文件和目录）：<br>
$ svn export file:///tmp/repos my-export

<h4 id="svnimport">svn import 纳入版本控制</h4>

{% highlight javascript %}
import: 将未纳入版本控制的文件或目录树提交到版本库。
用法: import [PATH] URL

  递归地提交 PATH 的副本至 URL。
  如果省略 PATH，则默认为 “.”。
  父目录会根据需要在版本库内创建。
  如果 PATH 是目录，则其下的内容直接加入到 URL 指定的位置内。
  如果使用了 “--force”，那么不能纳入版本控制的项目，例如设备文件，管道等，会被忽略。

有效选项:
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --auto-props             : 启用自动属性
  --force                  : 强制操作运行
  --no-auto-props          : 关闭自动属性
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]
  --no-ignore              : 忽略默认值和 svn:ignore 属性

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.这将本地目录myproj导入到版本库的trunk/misc，trunk/misc在导入之前不需要存在—svn import会递归的为你创建目录。<br>
$ svn import -m “New import” myproj http://svn.red-bean.com/repos/trunk/misc

2.需要知道这样不会在版本库创建目录myproj，如果你希望这样，请在URL后添加myproj：<br>
$ svn import -m “New import” myproj http://svn.red-bean.com/repos/trunk/misc/myproj

<h4 id="svninfo">svn info 显示本地或远程条目的信息</h4>

{% highlight javascript %}
info: 显示本地或远程条目的信息。
用法: info [TARGET[@REV]...]

  显示每个 TARGET 的信息 (默认: “.”)。
  TARGET 可以是工作副本中的路径或版本库中的 URL。REV 指定从哪个
  版本开始查找目标。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --targets ARG            : 传递文件 ARG 内容为附件参数
  --incremental            : 给予适合串联的输出
  --xml                    : 输出为 XML
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.svn info会展示工作拷贝所有项目的所有有用信息，它会显示文件的信息：<br>
$ svn info foo.c

2.它也会展示目录的信息：<br>
$ svn info vendors

3.svn info也可以针对URL操作：<br>
$ svn info http://svn.red-bean.com/repos/test/readme.doc

<h4 id="svnlock">svn lock 锁定版本库中的路径</h4>

{% highlight javascript %}
lock: 锁定版本库中的路径，使得其他用户不能向其提交修改。
用法: lock TARGET...

     使用 --force 选项可以从其他用户或工作副本窃取到锁。

有效选项:
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -m [--message] ARG       : 指定加锁注释ARG
  -F [--file] ARG          : 从文件 ARG 读取加锁注释
  --force-log              : 强制校验锁定注释源
  --encoding ARG           : 将ARG的值视为字符编码
  --force                  : 强制操作运行

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.在工作拷贝锁定两个文件：<br>
$ svn lock tree.jpg house.jpg

2.锁定工作拷贝的一个被其它用户锁定的文件：<br>
$ svn lock tree.jpg<br>
$ svn lock –force tree.jpg

3.没有工作拷贝的情况下锁定文件：<br>
$ svn lock http://svn.red-bean.com/repos/test/tree.jpg

<h4 id="svnlog">svn log 查看日志</h4>

{% highlight javascript %}
log: 显示一组版本与/或文件的提交日志信息。
用法: 1、log [PATH]
      2、log URL[@REV] [PATH...]

  1、显示本地 PATH (默认: “.”) 的日志信息。默认的版本范围是 BASE:1。

  2、显示 URL 中 PATH (默认: “.”) 的日志信息。如果指定了 REV，就从 REV
     开始查找 URL，版本范围是 REV:1。否则就从 HEAD 开始查找 URL，版本范围
     是 HEAD:1。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -v [--verbose]           : 打印附加信息
  -g [--use-merge-history] : 从合并历史使用/显示额外信息
  -c [--change] ARG        : 版本 ARG 引起的改变
  --targets ARG            : 传递文件 ARG 内容为附件参数
  --stop-on-copy           : 查看历史不要跨越不同的副本
  --incremental            : 给予适合串联的输出
  --xml                    : 输出为 XML
  -l [--limit] ARG         : 日值项最大值
  --with-all-revprops      : 获取所有版本属性
  --with-no-revprops       : 没有找回版本属性
  --with-revprop ARG       : 获取版本属性 ARG

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.查看最近3个版本日志<br>
svn log [PATH] -v -l3

2.查看某两个版本，用来对比<br>
svn log -r 14:15

3.日志放入文件<br>
$ svn log -r 14 > mylog<br>
$ svn log -r 19 >> mylog<br>
$ svn log -r 27 >> mylog<br>
$ cat mylog

或者<br>
$ svn log –incremental -r 14 > mylog<br>
$ svn log –incremental -r 19 >> mylog<br>
$ svn log –incremental -r 27 >> mylog<br>
$ cat mylog

<h4 id="svnmerge">svn merge 将两个源差异应用至工作副本</h4>

{% highlight javascript %}
merge: 将两个源差异应用至工作副本。
用法:  1. merge sourceURL1[@N] sourceURL2[@M] [WCPATH]
       2. merge sourceWCPATH1@N sourceWCPATH2@M [WCPATH]
       3. merge [-c M[,N...] | -r N:M ...] SOURCE[@REV] [WCPATH]

  1、第一种形式中，源 URL 的版本 N 与 M 作为比较的来源。如果没有指定版本，默认
     为 HEAD。

  2、在第二种形式中，两个源工作副本路径对应的版本库中的 URL 作为比较的来源。这
     里必须指定版本。

  3、第三种形式中，SOURCE 可为 URL 或工作副本中的路径(后者会使用版本库中对应
     的 URL)。比较版本为 REV 的 SOURCE，就像它在版本 N 到 M 存在一样。如果没
     有给出 REV，默认为 HEAD。选项 “-c M” 等价于 “-r :M”，“-c -M” 与之
     相反，等价于 “-r M:”。如果没有指定版本范围，默认为 0:REV。可以指定
     多个 “-c” 或 “-r”，并且可以混合使用向前范围或向后范围。

  WCPATH 是接收修改的工作副本路径。如果省略了 WCPATH，默认为 “.”，除非来源中
  有与当前目录内同名的文件，这时修改会直接应用至该文件。

  注意:  Subversion 只有当两个合并源有继承关系的时候才会内部跟踪合并操作的源数
  据 -- 即如果第一个合并源是第二个的祖先，或者相反。在上述的第三种形式中，已经
  保证了满足条件。选项 --ignore-ancestry 忽略继承关系，强制 Subversion 认为合并
  源之间没有关系，不跟踪本次合并。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -c [--change] ARG        : 在ARG版本(如同 -r ARG-1:ARG)作的修改
                             如果ARG为负数则等价于 -r ARG:ARG-1
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --force                  : 强制操作运行
  --dry-run                : 尝试操作但没有修改
  --diff3-cmd ARG          : 使用 ARG 作为合并命令
  --record-only            : 标记版本为已合并(使用 -r 参数)
  -x [--extensions] ARG    : 缺省: “-u”。当 Subversion 调用外部比较程序时，ARG 直接传给它。但是当
                             Subversion 使用缺省的内置比较实现，或者正
                             显示追溯时, ARG 可以是:
                                -u (--unified):
                                   输出三行统一上下文。
                                -b (--ignore-space-change):
                                   忽略空白数量的修改。
                                -w (--ignore-all-space):
                                   忽略所有的空白。
                                --ignore-eol-style:
                                   忽略行尾样式的改变。                            -p (--show-c-function):
                                   在比较输出中显示 C 函数名称。
  --ignore-ancestry        : 合并时忽略原始信息
  --accept ARG             : 指定自动解决冲突动作
                            ('postpone', 'base', 'mine-conflict',
                             'theirs-conflict', 'mine-full', 'theirs-full',
                             'edit', 'launch')
  --reintegrate            : 批量合并所有源 URL 中未合并的修改

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.将一个分支合并回主干（假定你有一份主干的工作拷贝，分支在修订版本250创建）：<br>
$ svn merge -r 250:HEAD http://svn.red-bean.com/repos/branches/my-branch

2.如果你的分支在修订版本23，你希望将主干的修改合并到分支，你可以在你的工作拷贝的分支上这样做：<br>
$ svn merge -r 23:30 file:///tmp/repos/trunk/vendors

3.合并一个单独文件的修改：<br>
$ svn merge -r 30:31 thhgttg.txt

<h4 id="svnmergeinfo">svn mergeinfo 显示合并的相关信息</h4>

{% highlight javascript %}
mergeinfo: 显示合并的相关信息。
用法: mergeinfo SOURCE[@REV] [TARGET[@REV]]

  显示 SOURCE 与 TARGET(默认为 “.”) 之间的合并(或潜在合并)信息。如果没有
  提供选项 --show-revs，那么就显示已经从 SOURCE 合并到 TARGET 的版本。否
  则就显示选项 --show-revs 指定的信息。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  --show-revs ARG          : 指定显示哪个版本集合
                             ('merged', 'eligible')

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.查看分支目录合并信息<br>
$ svn mergeinfo branches/test

<h4 id="svnmkdir">svn mkdir 创建纳入版本控制的新目录</h4>

{% highlight javascript %}
mkdir: 创建纳入版本控制的新目录。
用法: 1、mkdir PATH...
      2、mkdir URL...

  创建版本控制的目录。

  1、每个以工作副本 PATH 指定的目录，都会在本地创建，并且加入增加
     调度，直到下次提交。

  2、每个以 URL 指定的目录，都会通过立即提交在版本库中创建。

  在这两个情况下，所有的中间目录都必须事先存在，
  除非使用选项 --parents。

有效选项:
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --parents                : 创建中间目录
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.在工作副本创建一个目录：<br>
$ svn mkdir newdir

<h4 id="svnmove">svn move 移动或改名文件或目录</h4>

{% highlight javascript %}
move (mv, rename, ren): 在工作副本或版本库中移动或改名文件或目录。
用法: move SRC... DST

当移动多个源时，它们作为 DST 的子节点增加，DST 必须是目录。

  注意: 本子命令等同于先 “copy”，然后 “delete”。
  注意: 此命令中 --revision 选项没有作用，已经淘汰。

  SRC 可同时为工作副本(WC) 路径或 URL:
    WC  -> WC  :  移动并加入新增调度 (连同历史记录)
    URL -> URL :  完全是服务器端更名。
  所有 SRC 必须是同一类型。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --force                  : 强制操作运行
  --parents                : 创建中间目录
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.移动工作拷bede一个文件：<br>
$ svn move foo.c bar.c

2.移动工作副本的一些文件到子目录：<br>
$ svn move baz.c bat.c qux.c src

<h4 id="svnpropdel">svn propdel 删除目录、文件或版本的属性</h4>

{% highlight javascript %}
propdel (pdel, pd): 删除目录、文件或版本的属性。
用法: 1、propdel PROPNAME [PATH...]
      2、propdel PROPNAME --revprop -r REV [URL]

  1、删除工作副本中纳入版本管理的文件或目录的属性。
  2、删除版本库中版本的属性。TARGET只用来判断访问哪个版本库。

有效选项:
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  --revprop                : 在版本属性上操作(使用-r参数)
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.删除你的工作副本中一个文件的一个属性：<br>
$ svn propdel svn:mime-type some-script

2.删除一个修订版本的属性：<br>
$ svn propdel –revprop -r 26 release-date

<h4 id="svnpropedit">svn propedit 编辑属性</h4>

{% highlight javascript %}
propedit (pedit, pe): 使用外部编辑器编辑属性。
用法: 1、propedit PROPNAME TARGET...
      2、propedit PROPNAME --revprop -r REV [TARGET]

  1、编辑工作副本或者版本库中受版本控制的属性。
  2、编辑版本库中指定版本的未版本控制的远程属性。
     TARGET 只用来判断访问哪一个版本库。

参见 “svn help propset” 以获得更多设置属性的信息。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  --revprop                : 在版本属性上操作(使用-r参数)
  -m [--message] ARG       : 指定日志信息ARG
  -F [--file] ARG          : 从文件ARG读取日志信息
  --force-log              : 强制校验日志信息资源
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --encoding ARG           : 将ARG的值视为字符编码
  --with-revprop ARG       : 在新版本设置版本属性 ARG
                使用格式 name[=value]
  --force                  : 强制操作运行

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.svn propedit对修改多个值的属性非常简单：<br>
$ svn propedit svn:keywords foo.c

<h4 id="svnpropget">svn propget 显示目录、文件或版本的属性取值</h4>

{% highlight javascript %}
propget (pget, pg): 显示目录、文件或版本的属性取值。
用法:  1、propget PROPNAME [TARGET[@REV]...]
       2、propget PROPNAME --revprop -r REV [URL]

  1、显示版本控制的属性。REV 指定从哪个版本开始查找目标。
  2、显示版本库中版本的未版本控制的远程属性。
     只用来判断访问哪个版本库。

  本子命令默认会在属性内容加上额外的换行符，得到美观的输出。另外，
  当同时处理多个路径时，每一个属性内容会在开头增加上它所对应的路径。
  使用 --strict 选项可以关闭这些美化行为 (它很有用，例如将二进制属
  性内容重定向到一个文件，但是仅用于单一目标的非递归操作)。

有效选项:
  -v [--verbose]           : 打印附加信息
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  --revprop                : 在版本属性上操作(使用-r参数)
  --strict                 : 使用严格的语法
  --xml                    : 输出为 XML
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.检查工作副本的一个文件的一个属性：<br>
$ svn propget svn:keywords foo.c

2.对于修订版本属性相同：<br>
$ svn propget svn:log –revprop -r 20

<h4 id="svnproplist">svn proplist 列出目录、文件或版本的所有属性</h4>

{% highlight javascript %}
proplist (plist, pl): 列出目录、文件或版本的所有属性。
用法:  1、proplist [TARGET[@REV]...]
       2、proplist --revprop -r REV [TARGET]

  1、显示目标的属性。REV 指定从哪个版本开始查找目标。
  2、列出版本库中版本的属性。TARGET 只用来判断访问哪个版本库。

有效选项:
  -v [--verbose]           : 打印附加信息
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --revprop                : 在版本属性上操作(使用-r参数)
  --xml                    : 输出为 XML
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.你可以使用proplist看到你工作拷贝的属性：<br>
$ svn proplist foo.c

2.使用 –verbose参数，将更清楚的显示工作拷贝的属性：<br>
$ svn proplist –verbose foo.c

<h4 id="svnpropset">svn propset 设定目录、文件或版本的属性</h4>

{% highlight javascript %}
propset (pset, ps): 设定目录、文件或版本的属性。
用法: 1. propset PROPNAME PROPVAL PATH...
      2. propset PROPNAME --revprop -r REV PROPVAL [TARGET]

  1、在工作副本中修改一个受版本控制的属性。
  2、在版本库的版本中修改一个非版本控制的属性。
     (TARGET 只用来判断访问哪一个版本库。)

  可以使用选项 --file 来代替 PROPVAL 赋值。

  注意: svn 除了能存储任意属性外，还识别以下专用的版本控制属性:
    svn:ignore     - 以换行符隔开的文件忽略模式列表。
    svn:keywords   - 将被展开的关键字。有效的关键字为。
      URL, HeadURL             - 此对象最新版本的 URL。
      Author, LastChangedBy    - 最后修改文件的人。
      Date, LastChangedDate    - 此对象最后修改的日期。
      Rev, Revision,           - 此对象最后修改的版本号。
      LastChangedRevision
      Id                       - 前四个关键字的压缩摘要。
      Header                   - 与 Id 类似，只是包含完整的 URL。
    svn:executable   - 如果存在此属性则设置文件为可执行的。
      使用 “svn propdel svn:executable PATH...” 清除。
    svn:eol-style    - “native”，“LF”，“CR”，“CRLF” 之一。
    svn:mime-type    - 文件的媒体类型。用以决定是否该合并该文件，
      以及 Apache 如何处理它。媒体类型以“text/”开头(或是没有指
      定媒体类型)的文件会被视为纯文本文件，否则就是二进制数据。
    svn:externals    - 以换行符隔开的模块说明列表，每项都由一个
      相对路径、可选的版本标志、一个 URL 组成。这三个元素的顺序实现
      不同的行为。Subversion 1.4 和早期的版本仅支持下列格式，并且 URL
      中不能有 peg 版本：
        foo             http://example.com/repos/zig
        foo/bar -r 1234 http://example.com/repos/zag
      Subversion 1.5 和更高的版本支持以上格式，以及下列格式，URL 中可以有
      peg 版本：
                http://example.com/repos/zig foo
        -r 1234 http://example.com/repos/zig foo/bar
      Subversion 1.5 和更高的版本在上述格式中支持相对 URL，在用 URL 开始用
      下述字符串之一来指示：
        ../  相对于外部引用的父目录
        ^/   相对于版本库的根
        //   相对于方案
        /    相对于服务器根
      相对路径“relative_path relative_path”这种不明确的格式来自
      “relative_url relative_path”和 peg 版本支持。
    svn:needs-lock - 此属性表明文件在修改前应该加锁。
      如果没有加锁，则此文件在工作副本中应被设为只读。使用
      “svn propdel svn:needs-lock PATH...” 清除。

    svn:keywords, svn:executable, svn:eol-style, svn:mime-type 以及
    svn:needs-lock 属性不能设置在目录上。使用非递归选项(-N)在目录上
    设置这些属性时会失败，递归操作时只会作用于指定目录中的文件子孙上。

有效选项:
  -F [--file] ARG          : 从文件 ARG 读取属性值
  --encoding ARG           : 将ARG的值视为字符编码
  -q [--quiet]             : 不打印信息，或只打印概要信息
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --revprop                : 在版本属性上操作(使用-r参数)
  --force                  : 强制操作运行
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.给文件设置MIME type属性:<br>
$ svn propset svn:mime-type image/jpeg foo.jpg

2.在UNIX系统，如果你希望一个文件设置执行权限：<br>
$ svn propset svn:executable ON somescript

3.或许为了合作者的利益你有一个内部的属性设置：<br>
$ svn propset owner sally foo.c

4.如果你在特定修订版本的日志信息里有一些错误，并且希望修改，可以使用–revprop设置svn:log为新的日志信息： <br>
$ svn propset –revprop -r 25 svn:log “Journaled about trip to New York.”

<h4 id="svnresolve">svn resolve 解决工作副本中目录或文件的冲突</h4>

{% highlight javascript %}
resolve: 解决工作副本中目录或文件的冲突。
用法: resolve --accept=ARG [PATH...]

  注意:  当前需要选项 --accept 。

有效选项:
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --accept ARG             : 指定自动解决冲突动作的源
                            ('base', 'working', 'mine-conflict',
                             'theirs-conflict', 'mine-full', 'theirs-full')

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1、svn update后，1.txt文件出现冲突，选择base版本，即1.txt.rOld作为最后提交的版本<br>
$ svn resolve –accept base

2.手工修改1.txt文件，然后将当前拷贝即1.txt作为最后提交的版本<br>
$ svn resolve –accept working 1.txt

3.使用1.txt.rNew作为最后提交的版本<br>
$ svn resolve –accept theirs-full 1.txt

4.使用1.txt.mine作为最后提交的版本 <br>
$ svn resolve –accept mine-full 1.txt

5.使用1.txt.mine作为最后提交的版本 <br>
$ svn resolve –accept theirs-conflict 1.txt

<h4 id="svnresolved">svn resolved 删除工作副本中目录或文件的“冲突”状态</h4>

{% highlight javascript %}
resolved: 删除工作副本中目录或文件的“冲突”状态。
用法: resolved PATH...

  注意: 本子命令不会依语法来解决冲突或是删除冲突标记；它只是删除冲突相关的
        附加文件，让 PATH 可以被再次提交。它已经过时，被
        “svn resolve --accept working”取代。

有效选项:
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -q [--quiet]             : 不打印信息，或只打印概要信息

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1、当你解决了foo.c的冲突，并且准备提交，运行svn resolved让你的工作拷贝知道你已经完成了所有事情。<br>
$ svn resolved foo.c

<h4 id="svnrevert">svn revert 将工作副本文件恢复到原始版本</h4>

{% highlight javascript %}
revert: 将工作副本文件恢复到原始版本(恢复大部份的本地修改)。
用法: revert PATH...

  注意: 本子命令不会访问网络，它解除任何冲突的状态。
  但是，它不恢复被删除的目录。

有效选项:
  --targets ARG            : 传递文件 ARG 内容为附件参数
  -R [--recursive]         : 向下递归，与 --depth=infinity 相同
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1、丢弃对一个文件的修改：<br>
$ svn revert foo.c

2.如果你希望恢复一整个目录的文件，可以使用–recursive选项：<br>
$ svn revert –recursive .

3.最后，你可以取消预定的操作：<br>
$ svn add mistake.txt whoops<br>
$ svn revert mistake.txt whoops

<h4 id="svnstatus">svn status 显示工作副本中目录与文件的状态</h4>

{% highlight javascript %}
status (stat, st): 显示工作副本中目录与文件的状态。
用法: status [PATH...]

  未指定参数时，只显示本地修改的条目(没有网络访问)。
  使用 -q 时，只显示本地修改条目的摘要信息。
  使用 -u 时，增加工作版本和服务器上版本过期信息。
  使用 -v 时，显示每个条目的完整版本信息。

  输出的前七栏各占一个字符宽度:
    第一栏: 表示一个项目是增加、删除，还是修改
      “ ” 无修改
      “A” 增加
      “C” 冲突
      “D” 删除
      “I” 忽略
      “M” 改变
      “R” 替换
      “X” 未纳入版本控制的目录，被外部引用的目录所创建
      “?” 未纳入版本控制
      “!” 该项目已遗失(被非 svn 命令删除)或不完整
      “~” 版本控制下的项目与其它类型的项目重名
    第二栏: 显示目录或文件的属性状态
      “ ” 无修改
      “C” 冲突
      “M” 改变
    第三栏: 工作副本目录是否被锁定
      “ ” 未锁定
      “L” 锁定
    第四栏: 已调度的提交是否包含副本历史
      “ ” 没有历史
      “+” 包含历史
    第五栏: 该条目相对其父目录是否已切换，或者是外部引用的文件
      “ ” 正常
      “S” 已切换
      “X” 被外部引用创建的文件
    第六栏: 版本库锁定标记
      (没有 -u)
      “ ” 没有锁定标记
      “K” 存在锁定标记
      (使用 -u)
      “ ” 没有在版本库中锁定，没有锁定标记
      “K” 在版本库中被锁定，存在锁定标记
      “O” 在版本库中被锁定，锁定标记在一些其他工作副本中
      “T” 在版本库中被锁定，存在锁定标记但已被窃取
      “B” 没有在版本库中被锁定，存在锁定标记但已被破坏
    第七栏: 项目冲突标记
      “ ” 正常
      “C” 树冲突
    如果项目包含于树冲突之中，在项目状态行后会附加行，说明冲突的种类。

  是否过期的信息出现的位置是第九栏(与 -u 并用时):
      “*” 服务器上有更新版本
      “ ” 工作副本是最新版的

  剩余的栏位皆为变动宽度，并以空白隔开:
    工作版本号(使用 -u 或 -v 时)
    最后提交的版本与最后提交的作者(使用 -v 时)
    工作副本路径总是最后一栏，所以它可以包含空白字符。

  范例输出:
    svn status wc
     M     wc/bar.c
    A  +   wc/qax.c

    svn status -u wc
     M           965    wc/bar.c
           *     965    wc/foo.c
    A  +         965    wc/qax.c
    Status against revision:   981

    svn status --show-updates --verbose wc
     M           965       938 kfogel       wc/bar.c
           *     965       922 sussman      wc/foo.c
    A  +         965       687 joe          wc/qax.c
                 965       687 joe          wc/zig.c
    Status against revision:   981

    svn status
     M      wc/bar.c
    !     C wc/qaz.c
          >   local missing, incoming edit upon update
    D       wc/qax.c

有效选项:
  -u [--show-updates]      : 显示更新信息
  -v [--verbose]           : 打印附加信息
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --no-ignore              : 忽略默认值和 svn:ignore 属性
  --incremental            : 给予适合串联的输出
  --xml                    : 输出为 XML
  --ignore-externals       : 忽略外部项目
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}

<h4 id="svnswitch">svn switch 更新工作副本至不同的URL</h4>

{% highlight javascript %}
switch (sw): 更新工作副本至不同的URL。
用法:  1、switch URL[@PEGREV] [PATH]
       2、switch --relocate FROM TO [PATH...]

  1、更新工作副本，切换到同一版本库中的新 URL。其行为跟 “svn update” 很像，
     也是将工作副本切换到同一版本库中某个分支或者标签的方法。PEGREV 决定从，
     哪个版本查找目标。

     如果使用了 “--force” 选项，在工作副本中未版本控制的障碍路径，不会自动
     导致失败。如果障碍路径与版本库中的对应路径类型相同(文件或目录)，它将成
     为受版本控制的路径，但是内容不改变。它意味着障碍路径的子孙，如果也是障
     碍路径，那么也会受版本控制。对于障碍路径中的文件，如果与版本库内的不同，
     将视为工作副本发生本地修改。版本库中的所有属性都应用于障碍路径。

     使用 “--set-depth” 选项设置此操作目标的工作副本之新深度。

  2、重写工作副本的 URL 元数据，以反映单纯的 URL 改变。当版本库的根 URL 改
     变(比如方案或者主机名称变动)，但是工作副本仍旧对应同一版本库的同一目录
     时，使用这个命令更新工作副本与档案库的对应关系。

  参见 “svn help update” 以获取报告执行动作的字符列表。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --set-depth ARG          : 设置工作副本的新深度为 ARG(“exclude”，“empty”，“files”，“immediates”，
                            或“infinity”)
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --diff3-cmd ARG          : 使用 ARG 作为合并命令
  --relocate               : 通过URL改写重新定位
  --ignore-externals       : 忽略外部项目
  --force                  : 强制操作运行
  --accept ARG             : 指定自动解决冲突动作
                            ('postpone', 'base', 'mine-conflict',
                             'theirs-conflict', 'mine-full', 'theirs-full',
                             'edit', 'launch')

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1、如果你目前所在目录vendors分支到vendors-with-fix，你希望转移到那个分支：<br>
$ svn switch http://svn.red-bean.com/repos/branches/vendors-with-fix .

2.为了跳转回来，只需要提供最初取出工作拷贝的版本库URL：<br>
$ svn switch http://svn.red-bean.com/repos/trunk/vendors .

<h4 id="svnunlock">svn unlock 解除工作副本或URL的锁定</h4>

{% highlight javascript %}
unlock: 解除工作副本或URL的锁定。
用法: unlock TARGET...

    使用 --force 终止锁定。

有效选项:
  --targets ARG            : 传递文件 ARG 内容为附件参数
  --force                  : 强制操作运行

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.解锁工作拷贝中的两个文件：<br>
$ svn unlock tree.jpg house.jpg

2.解锁工作拷贝的一个被其他用户锁定的文件：<br>
$ svn unlock tree.jpg

3.没有工作拷贝时解锁一个文件：<br>
$ svn unlock http://svn.red-bean.com/repos/test/tree.jpg

<h4 id="svnupdate">svn update 将版本库的修改合并到工作副本中</h4>

{% highlight javascript %}
update (up): 将版本库的修改合并到工作副本中。
用法: update [PATH...]

  如果没有指定版本，则将工作副本更新到 HEAD 版本。否则同步到 -r 选项所
  指定的版本。

  每更新一项就输出一行信息，使用首字符来报告执行的动作。这些字符的含义是:

    A  已添加
    D  已删除
    U  已更新
    C  合并冲突
    G  合并成功
    E  已存在

  第一列字符表示文件内容更新，第二列表示文件属性更新。第三列中的字符“B”
  表示此文件上的锁被终止或窃取。

  如果使用了 “--force” 选项，在工作副本中未版本控制的障碍路径，不会自动
  导致签出失败。 如果障碍路径与版本库中的对应路径类型相同(文件或目录),它
  将成为受版本控制的路径，但是内容不改变。它意味着障碍路径的子孙，如果也
  是障碍路径，那么也将受版本控制。对于障碍路径中的文件，如果与版本库内的
  不同，将视为工作副本发生本地修改。版本库中的所有属性都应用于障碍路径。
  用第一列字符 “E” 来报告障碍路径。

  使用“--set-depth”选项设置此操作目标的工作副本之新深度。

有效选项:
  -r [--revision] ARG      : ARG (一些命令也接受ARG1:ARG2范围)
                             版本参数可以是如下之一:
                                NUMBER       版本号
                                '{' DATE '}' 在指定时间以后的版本
                                'HEAD'       版本库中的最新版本
                                'BASE'       工作副本的基线版本
                                'COMMITTED'  最后提交或基线之前
                                'PREV'       COMMITTED的前一版本
  -N [--non-recursive]     : 过时；尝试 --depth=files 或 --depth=immediates
  --depth ARG              : 受深度参数 ARG(“empty”，“files”，“immediates”，或“infinity”) 约束的操作
  --set-depth ARG          : 设置工作副本的新深度为 ARG(“exclude”，“empty”，“files”，“immediates”，
                            或“infinity”)
  -q [--quiet]             : 不打印信息，或只打印概要信息
  --diff3-cmd ARG          : 使用 ARG 作为合并命令
  --force                  : 强制操作运行
  --ignore-externals       : 忽略外部项目
  --changelist ARG         : 只能对修改列表 ARG 成员操作
                             [aliases: --cl]
  --editor-cmd ARG         : 使用 ARG 作为外部编辑器
  --accept ARG             : 指定自动解决冲突动作
                            ('postpone', 'base', 'mine-conflict',
                             'theirs-conflict', 'mine-full', 'theirs-full',
                             'edit', 'launch')

全局选项:
  --username ARG           : 指定用户名称 ARG
  --password ARG           : 指定密码 ARG
  --no-auth-cache          : 不要缓存用户认证令牌
  --non-interactive        : 不要交互提示
  --trust-server-cert      : 不提示的接受未知的 SSL 服务器证书(只用于选项 “--non-interactive”)
  --config-dir ARG         : 从目录 ARG 读取用户配置文件
  --config-option ARG      : 以下属格式设置用户配置选项：
                                 FILE:SECTION:OPTION=[VALUE]
                             例如：
                                 servers:global:http-library=serf
{% endhighlight %}
常用操作<br>
1.获取你上次更新之后版本库的修改：<br>
$ svn update

2.你也可以将工作拷贝更新到旧的修订版本<br>
$ svn update -r30
