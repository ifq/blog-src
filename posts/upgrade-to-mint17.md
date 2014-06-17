---
title: upgrade to mint17
date: '2014-06-17'
description: 升级到linux mint17啦
categories:
- binary
tags:
- linux mint
---

升级到 linux mint 17 MATE 的版本了。整好了之后感觉不错，用了一天有几个地方感觉比较舒服。
- 之前用 mint 14 的时候关机、重启经常有问题。现在好了，关机很顺畅。（在virtualbox中运行）
- 界面更舒服了。
- 输入法顺带升级到 sogou 1.0，好用了很多。
- 在virtualbox下的网络更稳定了。

本来14用着也能满足工作需要，花功夫升级到17算是力气没白花。遇到的几个问题记录一下。

## 升级系统，保留HOME ##
由于跨越了几个版本，觉得重新安装的方式比较稳妥。但是想保留之前的文件，不影响开发工作。还好之前工作相关的内容都在`/work`目录下，单独mount了一个盘。唯独home文件夹之前没有注意，这次需要折腾下。

virtualbox下还是比较好弄的，比host上直接装好折腾。大致的过程就是创建一个新的虚拟盘，mount到久系统的`/home2`目录下，把`/home`的内容拷贝过去。然后在新系统上mount上去，再选择性的拷贝到新系统的`/home`下。然后在吧所有内容拷贝到`home2`目录。然后修改`/etc/fstab`,把这个盘单独mount到`/home`目录即可。方法比较笨，不过也算搞定了。不知道还有没有更好的方法。

这样环境基本上平滑切换了，没有做太多的配置新的系统就可以用了。

## virtualbox share目录自动加载问题 ##
手动mount没有问题，添加到fstab文件中加载就不行。调试了下发现时fstab加载的时候，vboxsf这个必要的组建还没有加载。用了个简单的方法，不把这个mount加载到fstab里了，而是放到`/etc/rc.local`中：

    mount.vboxsf -w -o uid=1000,gid=1000 ifqdata /media/sf_ifqdata/
    mount.vboxsf -w -o uid=1000,gid=1000 temp /media/sf_temp/


## 搜狗输入法黑框问题 ##
linux终于有体面的输入法了！搜狗1.0的输入法，和win下的基本上差不多了。用起来舒坦了很多。安装过程就不说了，搜狗官网有。关键装好之后总是有个1cm见方的黑框在屏幕上浮动，让人很不爽。花了几个小时没搞定，网上搜一下`linux 搜狗 黑框`就搞定了。有好几个方法，我用的这个:

> 打开“控制中心”，在“桌面设置”之“窗口“中勾选“Use compositing”即可。

