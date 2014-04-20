---
title: emacs 学习进行时
date: '2014-03-10'
description: emacs 学习无止境, 抽空折腾下.
categories:
- binary
tags:
- emacs
---

最近又添加了几个minor mode, 试用之后感觉不错，相见恨晚。如果你也还没用，赶紧用。

### expand-region ###
在代码中快速框选region，从小到大。比如选取当前的symbol，再按一下选当前表达式，再按一下选括号外。。。可大可小，很方便。

不过在大的c代码中还有性能问题。我提了一个issue到 git 上，正在跟踪。
[https://github.com/magnars/expand-region.el/issues/134](https://github.com/magnars/expand-region.el/issues/134)

### multi cursors ###
选取相似的点，建立多个cursor，同时编辑。感觉很好用的样子，虽然刚装上还没有体会到便利。

### ace jump ###
这个很酷！mind blow～ 跳转到屏幕的任何位置，不只是当前的buffer。我绑定到 `M-m` 键，按了之后再按一个字母，是我想跳转位置的首字母，然后屏幕灰下来，首字母符合我选择的点会高亮，并以此变成abcdef，再按想去的点对应的字母就过去了。描述起来有点困难。。。用过就知道，肯定好用。

### recentf ###
这个简单，就是最近访问过的N个文件，列出来。


### desktop save mode ###
这个也简单,保存当前打开的所有文件，下次启动emacs时继续打开。



