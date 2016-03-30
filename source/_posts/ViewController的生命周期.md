---
title: ViewController的生命周期
date: 2016-03-30 13:11:00
category: iOS
tags: [iOS]
---
# init 与 initWithNibName 
init 可看作与initWithNibName:nil bundle:nil 等效，其作用是查找一个与ViewController同名的Xib文件，用来实例化对应的ViewController（不创建view），当指定initWithNibName:的名称参数时，则按指定的名称来查找Xib文件。

# loadView 
在ViewController的view被访问时，如果view等于nil，则loadView会被调用。loadView里可以选择使用init中指定的Xib文件创建view或者使用代码创建。当内存告急，view被释放后再次被使用时，还会再次loadView。

# awakeFromNib
当Xib中的所有NSObject对象被loadView加载时，会向其发送awakeFromNib，但不包含Xib的所有者。如官方文档描述的那样：In iPhone OS, this message is sent only to the interface objects that were instantiated by the nib-loading code. It is not sent to File’s Owner, First Responder, or any other proxy objects.  需要注意的是，awakeFromNib只会调用一次。

# viewDidLoad
一次loadView后，必然会触发一次ViewDidLoad。

# viewWillAppear
Appear 与 View的Hidden状态相关，每当view显示时，都会发送viewWillAppear。

# viewWillLayoutSubviews
当view 的 bound发生变化时，子视图的布局必然也需要变化，这个函数就是通知重新布局子视图。所以子视图的大小和位置应该在这里确定。


# 实践
做一个小实验：1 为主视图控制器，2在1的loadView中创建，2包含一个子视图3。情况如下
![这里写图片描述](http://img.blog.csdn.net/20160327230957600)
