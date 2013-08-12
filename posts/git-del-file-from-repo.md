---
title: 如何从git repo里删除一个文件
date: '2013-08-12'
description: 如何彻底删除一个文件
categories:
- binary
tags:
- git
- tips
---

有时候一些敏感信息或者垃圾信息会被无意添加到git repo里想要删除。通过 `git rm filename` 只能表皮上删除一个文件，如果希望彻底删除需要其他的方式。

    git filter-branch --force --index-filter \
    'git rm --cached --ignore-unmatch <filename>' \
    --prune-empty --tag-name-filter cat -- --all

`<filename>` 的位置替换为要清除的文件。然后push repo

    git push origin master --force

push 之前记得把清除的文件加到 .gitignore 里边，免得又 add 进去了。

更多信息可以参考[这里](https://help.github.com/articles/remove-sensitive-data).
