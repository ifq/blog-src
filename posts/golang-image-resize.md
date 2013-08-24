---
title: Go语言中生成图片缩略图
date: '2013-08-22'
description: 在Go 语言中快速生成缩略图
categories:
- binary
tags:
- golang
- image processing
---

最近在实现自己的一个idea，做一个小app。是一个用移动设备浏览自己电脑上海量照片的应用。多方面考虑决定用Go语言来完成。目前看来Go语言还是十分高效的，一方面是执行效率，一方面是开发效率。目前写800行代码就能实现一半的功能了。也许用其他脚本语言可以更短，但是执行效率不一定有Go好。不过也不是说Go就万能了，碰到一些实际问题，照样歇菜，比如本文的缩略图生成。

既然是图片应用，其中免不了生成缩略图这个处理。开始这个项目时，打算用pure Go来做，这样依赖简单点。于是开始找缩略图生成的GO实现。

一开始找到了 https://github.com/nfnt/resize 这个库，生成缩略图还可以选择各种算法，感觉不错。但实际跑起来才意识到不靠谱--速度太慢了。。5Mbyte的原图生成一个100像素宽的缩略图要6s（单核下）。我自己就有快100G的照片，要是用这个速度生成，小孩都会打酱油了。于是给原作者提了一个[issue](https://github.com/nfnt/resize/issues/5)，不过他好像不在意。

继续找，[在golang-nuts上找到一段代码](https://groups.google.com/forum/#!topic/golang-nuts/n4aQ2OU3OmE/discussion)，实现比较简单，用间隔取点的方式生成小图。实际测试生成速度1.3s左右。由于这段代码比较简单，就自己理解了下，改了改，希望能够提升点速度。尝试改了修改索引坐标的方式，没有提升；后来发现image库默认是解析为YCbCr的格式，而生成缩略图的过程使用的RGBA的格式，中间的转换产生了不必要的消耗，于是生成过程全部用YCbCr，但是还是没有提升。看来要提升性能，得在更深层面进行优化了。目前的Go下的图形库还做不到。

-------------------------------------------------------------------------------

Go的实现不行，就开始投奔C了。C比Go快是一方面，另外C的图形库也比较多。一番查找有以下几个有印象的：

- freeimage，比较轻量级。
- [IPP](http://software.intel.com/en-us/intel-ipp)，Intel的库，网上介绍最快，算法和处理器上有优化，但是是付费的。
- [VIPS](https://github.com/jcupitt/libvips)，免费的，[而且貌似速度也不错](http://www.vips.ecs.soton.ac.uk/index.php?title=Speed_and_Memory_Use)。

于是最后选择了VIPS。对我来说，要在GO程序中整合C程序有两种途径。一种是使用Cgo库的代码级别的整合，另一种是exec调用c进程。在golang-nut上学习了一番，感觉Cgo还不是很成熟，而且比较麻烦。另一方面，VIPS已经提供了专用的小程序vipsthumbnail用来生成缩略图，所以就偷个懒直接调用这个程序生成得了。实测结果生成一张图需要0.3s左右，再起四个goroutine并行跑，速度杠杠的。唯一的缺憾是需要单独的vipsthumbnail程序，以及一堆动态库依赖文件。

```
cmd := exec.Command("vipsthumbnail", spath, "-o", tgt, "-s", strconv.Itoa(size[i].X))
err := cmd.Run()
if err != nil {
    log.Println("run err:", spath, tgt, err)
}
```

完整一点的程序在[gist上](https://gist.github.com/ifq/6326773)。

TFL。



