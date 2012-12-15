---
layout: post
title: outline
description: outline
---
今天上某个服务器看到一个程序卡在了上个月16号，今天6号-_-!!!,程序没退出，也没报错。

……

祭出windbg！

心中莫名的兴奋,嘿嘿，刚好想写一篇关于windbg的博客呢,就是你了！

安装windbg

从Windows SDK 中安装，勾选除了Debugging tools for windows 之外的。
http://www.microsoft.com/en-us/download/details.aspx?id=8279

配置Symbol:

安装好之后，运行，点击File--Symbol File Path,粘贴：
SRV*D:\localsymbols*http://referencesource.microsoft.com/symbols; SRV*D:\localsymbols*http://msdl.microsoft.com/download/symbols

打开dump文件

0:000> .loadby sos mscorwks

看下线程都在做什么
!threads

进去某个线程
~*s
看看当前线程托管堆内容
!clrstack -a

查看某个对象内容
!do

查看数组
!dumparray

=============
.Net GC是按照“代”来回收的。程序中的对象共有3代，0代、1代和2代，0代是最年轻的对象，2代对象存活的时间最长。GC按代回收垃圾也是出于性能考虑的;通常的对象都会在0代是被回收。例如，在一个asp.net程序中，和每一个请求相关的对象都应该在请求结束时回收掉。而没有被回收的对象会成为1代对象;也就是说1代对象是常驻内存对象和马上消亡对象之间的一个缓冲区。

从代的角度看，大对象属于2代对象，因为只有在2代回收时才会处理大对象。当某代垃圾回收执行时，会同时执行更年轻代的垃圾回收。比如：当1代垃圾回收时会同时回收1代和0代的对象，当2代垃圾回收时会执行1代和0代的回收.

代是垃圾回收器区分内存区域的逻辑视图。从物理存储角度看，对象分配在不同的托管堆上。一个托管堆(managed heap)是垃圾回收器从操作系统申请的内存区(通过调用windows api VirtualAlloc)。当CLR载入内存之后，会初始化两个托管堆，一个大对象堆(LOH –large object heap)和一个小对象对(SOH – small object heap)。

　　内存分配请求就是将托管对象放到对应的托管堆上。如果对象的大小小于85000byte，它会被放置在SOH;否则会被放在LOH上。
　　
　　对于SOH，对象在执行一次垃圾回收之后，会进入到下一代。也就是说如果在第一次执行垃圾回收时，存活下来的对象会进入第二代，如果在第2次垃圾回收之后该对象仍然没有被当作垃圾回收掉，它就会成为2代对象;2代对象就是最老的对象不会在提升代数。  
　　
　　
　　当触发垃圾回收时，垃圾回收器会在小对象堆做碎片整理，将存活下来的对象移动到一起。而对于大对象堆，由于移动内存的开销很大，CLR团队选择只是清除它们，将回收掉的对象组成一个列表，以便满足下次有大对象申请使用内存，相邻的垃圾对象会被合并成一块空闲的内存块。