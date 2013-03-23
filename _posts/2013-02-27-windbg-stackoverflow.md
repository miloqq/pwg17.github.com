---
layout: post
title: windbg调试堆栈溢出异常。
description: "windbg 调试 StackOverflow 堆栈溢出，c#程序"
category: "windbg"
tags: ["windbg调试", "堆栈溢出"]
---

不废话，直接上调试的过程：

先看下各线程的状况：

0:030> !threads

ThreadCount: 51
UnstartedThread: 0
BackgroundThread: 4
PendingThread: 0
DeadThread: 21
Hosted Runtime: no
                                              PreEmptive                                                Lock
       ID OSID        ThreadOBJ     State   GC     GC Alloc Context                  Domain           Count APT Exception
   0    1 1478 0000000000ae0800   201a220 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA
   2    2  134 0000000000ae80f0      b220 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA (Finalizer)
   5    3  b24 0000000000b9f1c0      b220 Enabled  0000000020a65780:0000000020a675b8 0000000000ad80e0     1 MTA
   6    4 1250 0000000000bcc960      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027eeec8)
   7    5  ccc 0000000000bcef20      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (000000000480a948)
   8    7 1200 000000001c309690      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000039ff6e0)
   9    9 1098 000000001c2fe940      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (000000000371afe8)
  10    b 1600 000000001c31b850      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f7098)
  11    c 11ac 0000000000bb1dc0      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000028ebc30)
  12    f  f30 000000001c31d060      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.InvalidOperationException (00000000027eb788)
  13   12 1248 000000001c34eac0      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Xml.XmlException (00000000027f5678)
  14    a 106c 000000001c34fc30      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f8090)
  15   16 1314 000000001c351370      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000028ebfa8)
  16   18 148c 000000001c351f10      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027faed8)
  17   1c  1ec 000000001c353650      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f88f0)
  18   21 15f4 000000001c355360      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (0000000002cc2df8)
  19   17  f50 000000001c351940      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027fb740)
  20   11  64c 000000001c36def0      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (0000000004527ef0)
  21   20 1394 000000001c354d90      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f8fe8)
  22   1f 14b8 000000001c3547c0      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (0000000002911ed8)
  23   1d 16e8 000000001c353c20      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f3400)
  24   1b 1234 000000001c353080      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f63a0)
  25   1a  6a4 000000001c352ab0      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (00000000027f6090)
  26    d 1438 000000001c36f060      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA System.Net.WebException (0000000002b87e30)
XXXX   10    0 000000001c431930      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   22    0 000000001c431360      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX    6    0 000000001c430d90      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   1e    0 000000001c431f00      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
  28   40 1430 000000001f046ee0      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     1 MTA System.FormatException (00000000044a7e68)
  29   4f 1228 000000001f020340      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     1 MTA
  30   55 1378 000000001c448b60      b022 Disabled 0000000020a655b8:0000000020a655b8 0000000000ad80e0     0 MTA System.StackOverflowException (00000000027410d0)
  31   46 164c 000000001c38d920   1801220 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn (Threadpool Worker)
XXXX   44    0 000000001f045a80      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
  32   2e 17a0 000000001c41c880      b020 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA
XXXX   58    0 000000001c41ce50      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   3d    0 000000001f140730      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   4d    0 000000001f140d00      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   43    0 000000001f13ad00      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   5d    0 000000001f0434b0      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   4b    0 000000001c4eb720      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   36    0 000000001f046340      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA
XXXX   5a    0 000000001f044340      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   31    0 000000001efd3e80      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 MTA
XXXX   5c    0 000000001c4eb150      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   3b    0 000000001c435360      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   15    0 000000001c435930      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   23    0 000000001c435f00      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   29    0 000000001c41c2b0      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   32    0 000000001c41bce0      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
XXXX   37    0 000000001f140160      9820 Enabled  0000000000000000:0000000000000000 0000000000ad80e0     0 Ukn
  33   4a  f80 000000001c41b710      b020 Disabled 0000000020b8d708:0000000020b8d728 0000000000ad80e0     1 MTA (GC)
  

-----------------------------------------

可以看到是第30条线程抛出堆栈溢出：

30   55 1378 000000001c448b60      b022 Disabled 0000000020a655b8:0000000020a655b8 0000000000ad80e0     0 MTA System.StackOverflowException (00000000027410d0)


-----------------------------------------

再看看当前堆栈的情况：

0:030> !clrstack

OS Thread Id: 0x1378 (30)
(!clrstack processes a max of 1000 stack frames)
Child-SP         RetAddr          Call Site
000000001bfc4e50 000007ff002d5673 Html.HtmlNodeCollection.GetEnumerator()
000000001bfc4e90 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc4f20 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc5180 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc5210 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc5470 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc5500 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc5760 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc57f0 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc5a50 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc5ae0 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc5d40 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc5dd0 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc6030 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc60c0 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc6320 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc63b0 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc6610 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc66a0 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc6900 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)
000000001bfc6990 000007ff002d56b2 Html.HtmlNode.WriteTo(System.IO.TextWriter)
000000001bfc6bf0 000007ff001cd77b Html.HtmlNode.WriteContentTo(System.IO.TextWriter)

……省略……


看来是不断的嵌套调用WriteTo和WriteContentTo方法导致堆栈溢出的。

------------------------------------------

这个时候解决问题的思路有很多，上面已经知道是哪条线程出错，和已经知道异常的地址 System.StackOverflowException (00000000027410d0) 可以切入进去，用!do命令查看里面的信息。

另外可以结合自己代码情况选择更快速的路径，这个程序是多线程下载解析html的程序，看堆栈情况是解析不规范的html的时候导致堆栈溢出的，所以只需要找出程序出错时正在处理的html就行了，找出之后在本地用vs调试修改。

这个项目下载的页面是放在Spider.PageData中的，所以：

0:030> !dumpheap -type Spider.PageData

         Address               MT     Size
0000000002bf3ef8 000007ff00242cc8      120    
0000000002bf85a8 000007ff00242cc8      120    
0000000004802d50 000007ff00242cc8      120    
00000000049e6100 000007ff00242cc8      120    
0000000004a4e788 000007ff00242cc8      120    
0000000004a7ef70 000007ff00242cc8      120    
0000000004fc79c0 000007ff00242cc8      120    
0000000004fce970 000007ff00242cc8      120    
0000000004fdf210 000007ff00242cc8      120    
00000000051c6298 000007ff00242cc8      120    
0000000005b01d80 000007ff00242cc8      120    
0000000005f65450 000007ff00242cc8      120    
00000000204572b8 000007ff00242cc8      120    
000000002068cf80 000007ff00242cc8      120    
total 14 objects
Statistics:
              MT    Count    TotalSize Class Name
000007ff00242cc8       14         1680 Spider.PageData
Total 14 objects

------------------------------------------

上面就是一个个的当前程序正在处理的Spider.PageData了，里面有url信息，接着用!do 连续技找出url：

0:030> !do 0000000002bf3ef8

Name: Spider.PageData
MethodTable: 000007ff00242cc8
EEClass: 000007ff002323f8
Size: 120(0x78) bytes
 (D:\NewCrawler\Spider.dll)
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
000007fef9229598  400002e       60          System.Guid  1 instance 0000000002bf3f58 id
000007fef86d8010  400002f        8           System.Uri  0 instance 0000000002bf21d8 uri

------------------------------------------------
0:030> !do 0000000002bf21d8

Name: System.Uri
MethodTable: 000007fef86d8010
EEClass: 000007fef84043a8
Size: 72(0x48) bytes
 (C:\Windows\assembly\GAC_MSIL\System\2.0.0.0__b77a5c561934e089\System.dll)
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
000007fef922ec90  4001b75        8        System.String  0 instance 0000000002bf19e0 m_String
000007fef922ec90  4001b76       10        System.String  0 instance 0000000000000000 m_originalUnicodeString
000007fef86d8298  4001b77       18     System.UriParser  0 instance 0000000002765280 m_Syntax
000007fef922ec90  4001b78       20        System.String  0 instance 0000000000000000 m_DnsSafeHost
000007fef8beb168  4001b79       30        System.UInt64  1 instance 37615763456 m_Flags
000007fef86d8408  4001b7a       28   System.Uri+UriInfo  0 instance 0000000002bf4290 m_Info
000007fef922de60  4001b7b       38       System.Boolean  1 instance                0 m_iriParsing
000007fef922ec90  4001b6a      c90        System.String  0   static 0000000002764c78 UriSchemeFile
000007fef922ec90  4001b6b      c98        System.String  0   static 0000000002764c58 UriSchemeFtp
000007fef922ec90  4001b6c      ca0        System.String  0   static 0000000002764ca0 UriSchemeGopher
000007fef922ec90  4001b6d      ca8        System.String  0   static 0000000002764c08 UriSchemeHttp
000007fef922ec90  4001b6e      cb0        System.String  0   static 0000000002764c30 UriSchemeHttps
000007fef922ec90  4001b6f      cb8        System.String  0   static 0000000002764d18 UriSchemeMailto
000007fef922ec90  4001b70      cc0        System.String  0   static 0000000002764cf0 UriSchemeNews
000007fef922ec90  4001b71      cc8        System.String  0   static 0000000002764cc8 UriSchemeNntp
000007fef922ec90  4001b72      cd0        System.String  0   static 0000000002764db8 UriSchemeNetTcp
000007fef922ec90  4001b73      cd8        System.String  0   static 0000000002764de0 UriSchemeNetPipe
000007fef922ec90  4001b74      ce0        System.String  0   static 0000000002764be8 SchemeDelimiter
000007fef8c14b50  4001b7c      ce8 ...etSecurityManager  0   static 0000000000000000 s_ManagerRef
000007fef922e580  4001b7d      cf0        System.Object  0   static 0000000002765440 s_IntranetLock
000007fef922de60  4001b7e      978       System.Boolean  1   static                1 s_ConfigInitialized
000007fef8bebf98  4001b7f      97c         System.Int32  1   static                0 s_IdnScope
000007fef922de60  4001b80      980       System.Boolean  1   static                0 s_IriParsing
000007fef922e580  4001b81      cf8        System.Object  0   static 00000000027aca90 s_initLock
000007fef9230588  4001b82      d00        System.Char[]  0   static 0000000002765458 HexUpperChars
000007fef9230588  4001b83      d08        System.Char[]  0   static 0000000002765490 HexLowerChars
000007fef9230588  4001b84      d10        System.Char[]  0   static 00000000027654c8 _WSchars

---------------------------------
0:030> !do 0000000002bf19e0

Name: System.String
MethodTable: 000007fef922ec90
EEClass: 000007fef8e3b038
Size: 282(0x11a) bytes
 (C:\Windows\assembly\GAC_64\mscorlib\2.0.0.0__b77a5c561934e089\mscorlib.dll)
String: http://www.gzmama.com/thread-3221997-1-1.html
Fields:
              MT    Field   Offset                 Type VT     Attr            Value Name
000007fef9235f00  4000096        8         System.Int32  1 instance              129 m_arrayLength
000007fef9235f00  4000097        c         System.Int32  1 instance               65 m_stringLength
000007fef92306d8  4000098       10          System.Char  1 instance               68 m_firstChar
000007fef922ec90  4000099       20        System.String  0   shared           static Empty
                                 >> Domain:Value  0000000000ad80e0:0000000002741308 <<
000007fef9230588  400009a       28        System.Char[]  0   shared           static WhitespaceChars
                                 >> Domain:Value  0000000000ad80e0:0000000002741a30 <<
                                 

-----------------------------------

url是： http://www.gzmama.com/thread-3221997-1-1.html

done，功夫在代码之外。
