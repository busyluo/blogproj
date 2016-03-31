---
title: ARC内存管理解惑
date: 2016-03-30 13:10:31
category: iOS
tags: [iOS,Objective-C,ARC,内存管理]
---
自动引用计数(ARC)是一个编译器特性，它提供了自动的objective - c对象的内存管理。ARC是通过在编译时添加代码来确保OC对象只在需要的时候存在。从概念上讲，它添加适当的内存管理调用，并且遵循手动引用计数的内存管理规范。因此，在使用ARC时，不用关心什么时候使用retain，release 和 autorelease，并且ARC也不是直接发送retain等OC消息，而是直接调用runtime的C函数，以获得更高的效率。

# property的作用
property为你的成员变量生成setter 和 getter, 在生成时，对于不同的变量，肯定会有不同的管理方式，因此不能一概而论。于是就需要使用atomic，nonatomic, strong, retain,  weak, assign, copy, readonly, readwrite来设置不同的变量的管理方式。其中atomic和nonatomic很好理解，决定变时是否是线程安全的。 readonly和readwrite也好理解，决定其可读写性。剩下的strong, retain,  weak, assign, copy和setter相关。

需要注意的是，只有在通过**点格式**（obj.var）访问变量时，这些属性才会有效果。

```
self.name = something;  //正确的方式
_name = something;		//会绕开setter, 直接访问变量
```

# strong 与 retain ，weak 与 assign
strong和weak是ARC中新增的property属性，strong用于表明对对象的强引用，让其保持在内存中，直到不再指向它，其作用与retain完全相同，不过用意似乎更明确了。而weak 则不具备让对象保持在内存中的权利，当不再有strong指向weak指向的对象时，对象会被释放。

assign 和 weak的区别在于，当weak指向的对象被释放时，weak会指向nil， 而assign则不会这么做。

# copy 的作用
在使用copy时，setter传入的对象会被拷贝，并strong指向拷贝的对象。使用copy可以确保传入的对象不被外部修改。并且如果使用了copy，但对象没有被修改，会被优化成strong（未证实）。

# ARC中的内存泄露
虽然ARC能自动管理内存，但不恰当的使用也会导致内存泄漏。最常见的就是循环强引用。主要有以下几种情况：
 - Outlet类型指针
 - 委托
 - block
Outlet和委托可以可以将其声明为weak来避免循环引用。
对于block,可以使用@weakify 宏：
```
@weakify(self);  
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
      @strongify(self);
      [self doSomething];
      [self doOtherThing];
});
```

# 用法总结
在ARC工程中，使用strong 来代替retain，对原始的C语言变量使用assign，对OC对象的弱引用使用weak，对防止被外部修改的对象使用copy，另外，当需要声明一个block变量时，也应该使用copy。



# 参考链接：
http://stackoverflow.com/questions/8927727/objective-c-arc-strong-vs-retain-and-weak-vs-assign
http://stackoverflow.com/questions/9262535/explanation-of-strong-and-weak-storage-in-ios5/9262768#9262768
http://stackoverflow.com/questions/387959/nsstring-property-copy-or-retain stackoverflow问答
https://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html Apple官方文档
http://www.jianshu.com/p/633b6316d89b iOS arc VS mrc学习笔记