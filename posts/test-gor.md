---
title: 正式切换到gor
date: '2013-07-29'
description:
categories:
- binary
tags:
- gor
- golang
---

切换到github做博客也有将近一年时间了。之前一直用的是octopress作为博客的引擎。其他都挺好，有一个问题很头大：**慢！**

加上从baidu那边转过来的博客（当然，大部分是废话，*但是废话放30年后说不定会有价值* ）,目前有309篇博客。每次用octopress来生成的时候需要4,5分钟，尽管可以用小技巧单独先生成个别文件用来preview，但是还是慢。。让人没有了更新的欲望。多次在网上探寻加速的方法均无果。最近在折腾blog的时候，又等的发狂了，在网上搜索一通，发现了[gor](https://github.com/wendal/gor)，试用之后精神为之一震啊！

同样300+篇博客，用ruby的octopress需要4,5分钟，而使用[go](http://golang.org)的gor只需要数秒！！。这种差距也让我对go语言产生了兴趣（最近正在疯狂学习中）。看来国人也能出精品啊～一定得支持，于是果断换用gor。

<img class="imgbox" src="http://iwood.qiniudn.com/media/frontpage.png"  alt="go语言形象代言人" width="300" />


## 切换准备 ##

### 补齐yml标签 ###
octopress下，识别博客的日期是通过文件名前边包含年月日来实现的。而gor下面得在文件头部插入 `date` 标签，因此需要做下转换。本来用sed之类的写个脚本就搞定了，但是为了学习go语言，强迫用go写了一个[转换的脚本](https://gist.github.com/6103416)。
### 图片文件重整 ###
图片问题其实不是切换到gor直接导致的，而是在熟悉gor的过程中和[别人讨论的成果](https://github.com/wendal/gor/issues/32)。有人提到图片也放到github中管理会十分臃肿，应该放到外部管理。思考了以下也对，随便加点图片就200M了，长此以往肯定是不行的。加之国内的网速上传github本来就比较慢，所以还是分开管理比较好。

找寻外部存储空间，一开始只找了在线相册，不是不支持外链，就是国内访问有问题。后来找到[国内的云存储平台七牛](http://www.qiniu.com/)，解决了所有问题。不但外链没有问题（专门就是干这个的），而且上传也很方便（linux下有命令行工具批量同步），而且外链的地址也和本地目录树相同，方便生成`<img>`。

### permalink问题 ###
octopress下面，permalink设置为 `/blog/:year/:month/:day/:title/` 的时候，实际生成的地址是以文件名中出去日期的部分结尾，而在gor下面，则是以文章中实际的`title:`标题。这会导致链接地址与以前不一致。查了一下，改用 `/blog/:year/:month/:day/:filename/`，并把 md 文件里的日期部分去掉，从而保持url不变。

TFL。
