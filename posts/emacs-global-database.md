---
title: 设定 emacs 中 cscope 的固定搜索目录
date: '2014-04-20'
description: 如何在emacs 的 cscope 中制定全局的搜索位置
categories:
- binary
tags:
- emacs
- cscope
---
组件化的开发中，各个组件不在一个目录，但互相有依赖。有时候想看一个api的定义，因为不在当前组件目录下，cscope搜索不到。

看了下emacs的帮助文件，定制以下cscope 的 `cscope-database-regexps` 就可以实现搜索固定位置的功能。

    (setq cscope-database-regexps
	  '(
		( "/xxx/.*sdk"
		  ( t )
		  ( "/work/src/xxx/ef")
		  ( "/work/src/xxx/yyy/tags/1.0.0/code")
		  )
		( "/yyy/.*bsp"
		  ( t )
		  ( "/work/src/xxx/ef")
		  )
		)
	  )


上边的例子中，如果当前使用 cscope 的目录符合 `/xxx/.*sdk` ，则搜索完当前目录还会搜索 `/work/src/xxx/ef` 和 `/work/src/xxx/yyy/tags/1.0.0/code` 目录。

emacs 里查看 `cscope-database-regexps` 的帮助，会找到更高级的用法。

TFL.
