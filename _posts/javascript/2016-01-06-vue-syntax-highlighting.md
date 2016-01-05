---
layout:     post
title:      Atom编辑器添加Vue文件高亮
summary:    给Atom编辑器添加.vue文件的高亮
categories: javascript
tags:       [atom,vue,syntax,highlighting]
---

以前捣鼓vue库都是写的js文件没有用什么自动化工具，最近开始捣腾[webpack+vue-loader](https://github.com/vuejs/vue-cli)。但是.vue的文件在Atom编辑器下面默认是没有高亮的，所以本文记录我为Atom安装.vue文件高亮的过程。

1、首先我使用Atom的vue高亮是这个[package](https://github.com/hedefalk/atom-vue)，下载来来解包。

2、我使用的是Mac环境，需要把Finder隐藏文件给打开。在iTerm执行命令```javascript defaults write com.apple.Finder AppleShowAllFiles YES ```，然后重启Finder。

3、在Finder中打开目录```javascript /Users/MacName/.atom/packages ``` ，然后把下载好了Atom package 复制进去，重启Atom。Enjoy ：）
