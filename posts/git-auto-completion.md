---
title: git 命令行自动补全
date: '2013-08-26'
description: git 自动补全功能失效的修复
categories:
- binary
tags:
- git
- tips
---

本来是通过apt-get安装的git，但今天为了确认一个问题是不是git的bug，下载代码自己编译了一把，升级到1.8.x。（编译过程参考[官方文档](http://git-scm.com/book/en/Getting-Started-Installing-Git)，很简单）

但是卸载掉之前的安装版本后发现git的自动补全功能没有了，[官方文档](http://git-scm.com/book/en/Git-Basics-Tips-and-Tricks)有介绍怎么弄，原来是一个脚本实现的。

> If you use the Bash shell, Git comes with a nice auto-completion script you can enable. Download the Git source code, and look in the contrib/completion directory; there should be a file called git-completion.bash. Copy this file to your home directory, and add this to your .bashrc file:
> 
> source ~/.git-completion.bash

