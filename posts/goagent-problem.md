---
title: goagent youtube problem
date: '2013-10-10'
description: 解决用goagent看youtube时卡住的问题
categories:
- binary
tags:
- goagent
---

不知道从什么时候开始，goagent看youtube开始卡顿。观看的过程中会随机的停住，必须重新刷新然后鼠标点到之前的位置才能继续看，十分恼人。尝试了几次去解决，都没有搞定。今天又试了一下，貌似搞定了。

但是好像不是goagent自身的问题，而是配合其使用的firefox插件autoproxy的问题（之前用chrome时好像就没这问题，但是chrome又有播放flash卡顿的问题。。。）。升级goagent到3.0.4之后，使用firefox+autoproxy还是会出问题。然后关掉autoproxy，使用goagent的pac，设置到firefox的网络设置里。`http://localhost:8086/proxy.pac`

貌似就没有问题了！！！眼泪哗哗滴～～～ 现在观察了一段时间是不会卡主了，有同样问题的朋友可以试试不用autoproxy。

