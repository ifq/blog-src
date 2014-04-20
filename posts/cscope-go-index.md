---
title: cscope增加对go文件的index支持
date: '2014-03-16'
description: cscope增加对go文件的index支持
categories:
- binary
tags:
- emacs
- golang
---

golang本身提供的godef工具可以解决大部分代码检索的问题，但是有时候需要全局搜索的时候还是需要其他工具帮忙。cscope工具主要用在C语言下，但是go语言的语法类似，一定程度上也支持。

但是cscope在文件索引时默认不会找go文件，每次手动搞不方便。其实就改一行就可以了。

    +++ /usr/bin/cscope-indexer	2014-03-15 14:10:40.696994116 +0800
    @@ -137,7 +137,7 @@
     	find $DIR \( -type f -o -type l \)
         fi
     ) | \
    -    egrep -i '\.([chly](xx|pp)*|cc|hh)$' | \
    +    egrep -i '\.([chly](xx|pp)*|cc|hh|go)$' | \
         sed -e '/\/CVS\//d' -e '/\/RCS\//d' -e 's/^\.\///' | \
         sort > $LIST_FILE

