---
title: u-boot加载Linux过程
date: 2016-03-30 13:08:24
category: Linux内核
tags: [Linux,Uboot]
---

之前学习完了u-boot的启动，本文主要讲u-boot为linux内核的运行做了哪些准备工作。总结u-boot的作用，主要就是初始化硬件设备、写入linux的启动参数以及加载linux内核。下面对这几个主要作用作说明。

# 初始化(TQ210开发板)
首先了解在u-boot启动前，BL0完成的工作：
1. 禁止看门狗
2. 初始化指令cache
3. 初始化栈、堆
4. 初始化块设备拷贝函数
5. 初始化PLL（ 锁相环）、设置系统时钟
6. 根据OM引脚配置，从指定的外部存储器拷贝BL1到内部SRAM
7. 校验BL1的校验和

在BL1（u-boot-spl） 中初始化时钟、DRAM控制器。

# 写入linux启动参数
u-boot给linux参数是通过将参数以约定的格式放在内存中，然后u-boot将这个存放这个参数的内存地址传递给linux内核。

u-boot启动后，会自动执行其环境变量里bootcmd代表的命令，在执行这个命令前，可以添加一个延时，在这段时间内可以通过设定的按键来中止自动执行bootcmd，这个延时的长短由CONFIG_BOOTDELAY决定，但也可以设置环境变量来改变它（对于其它环境变量也是这样）。当CONFIG_BOOTDELAY等于0时，可以通过定义CONFIG_ZERO_BOOTDELAY_CHECK来中止自动执行过程，但按键必须在启动前按下。
   
u-boot传递给linux的参数是通过bootargs来传递的，在执行bootm前必须设置好bootargs，然后在bootcmd里添加bootm命令。

bootm的工作是根据bootargs写入参数并将linux内核拷贝到运行的位置。写入的格式参照《3.2.4 设置内核的启动参数》 章节和U-Boot与Linux内核的交互，写入的位置在内存的0-0x4000这个范围内，0x4000-0x8000存放的是内核页表，这些地址在内核中有相关的宏定义，这也是为什么内核的加载地址一般为0x****8000的原因。在参数写入完成后，将寄存器R0赋值为0,R1赋值为机器号, R2赋值为启动参数数据结构的首地址。

# 加载linux内核

调用bootm命令时，需要传入一个参数，这个参数代表的是将内核放到什么地方，那从什么地方去读内核文件呢？是通过u-boot分区别表，简单的说就是u-boot指定一块存储空间为kernel分区，并把内核映像放到该分区里，加载的时候就直接从这里读取了，详细请看http://blog.sina.com.cn/s/blog_95268f5001013e50.html。

找到内核映像（uImage）后，读取它的前0x40个字节，得到loadaddr 和entry point两个地址，这两个地址是在制作uImage时指定的，u-boot会对这几个地址作判断。最后跳转到linux处，将控制权移交给linux。

        


           