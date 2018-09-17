---
title: Atom安装使用
date: 2018-09-04 04:12:28
tags: IDE
categories: Atom
copyright: true
---


> 考虑到编写的智能合约是用solidity语言写的，Atom是比较好的IDE工具，本文仅仅是针对solidity语言来用到的地方来做了一些介绍，它不仅启动速度快，而且还提供了很多插件;界面美观，能提供很多种语言的代码补全、格式验证、代码亮丽等特点，而且可以多个页面同时展示和操作，此外还支持markdown语法，可以在上面直接写静态页面
# Atom下载
## 官方Atom下载地址：https://atom.io/
```
sudo dpkg -i atom-amd64.deb   
```

# 插件安装
## 安装linter-solidity插件（用solidity语言 提供代码提示）
```
sudo apm install linter 
sudo apm install linter-solidity 
```
<!-- more -->
# 遇到的问题

>如果因为翻墙原因无法通过命令下载
>执行命令：npm config get registry
>发现返回 https://registry.npmjs.org/
>通过命令：npm config set registry https://registry.npm.taobao.org
>设置成国内镜像下载

>也可以通过git下载下来源码的方式，把源码下载到 .atom/packages文件包中
>然后cd 到下载后的插件中，执行 sudo npm install 来下载模块依赖


# 一些常用的插件：

>autocomplete-solidity代码自动补齐
linter-solium、linter 、linter-solidity代码错误检查和代码提示
language-ethereum支持Solidity代码高亮以及Solidity代码片段
Sublime Block Comment或者docblockr 可以快捷键自动注释
关于手动下载Atom的插件地址可以从下面地址找：https://atom.io/packages

# 常用快捷键
>Crtl+Shift+M    开启Markdown实时预览
Command+Shift+P    打开命令窗口，可以运行各种菜单功能
Command + T    快速多文件切换
Command + F    文件内查找和替换
Command + Shift + F    多文件查找和替换
Command + [    对选中内容向左缩进
Command + ]    对选中内容向右缩进
Command + \    显示或隐藏目录树 
Crtl + m    相应括号之间，html tag之间等跳转 
Crtl + Alt + B    格式化代码（需要安装atom-beautify） 
Crtl + `    调起CLI命令行界面（需要安装terminal-panel）

# 可以参考的文档

+ http://wiki.jikexueyuan.com/project/atom-flight-manual-zh-cn/
+ https://blog.csdn.net/qq_32340877/article/details/79095610

