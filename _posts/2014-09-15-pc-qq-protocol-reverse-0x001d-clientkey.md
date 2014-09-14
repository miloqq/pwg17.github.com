---
layout: post
title: "2014 6.1 PC QQ UDP协议分析系列之0x001D【获取clientkey】篇"
description: "pc qq 协议分析"
category: "协议分析"
tags: ["协议分析", "逆向"]
---
#####本文仅出于研究目的，如有法律风险，请联系删帖，谢绝跨市。 
  关于clientkey作用不多说，这个包是触发式的，平时看不到，QQ面板上点击空间或者其它服务之后触发，触发后每13分钟更新一次。另外这个单点登录链接是http的，很不安全，可以被嗅探，附上一篇 [[科普]嗅探(被动嗅探)与ARP欺骗(主动嗅探)详解](http://www.freebuf.com/articles/system/11846.html)

sendto:

	[NO.2066 2014/9/9 14:59:28 sento 39字节]
	02
	35 2F
	00 1D
	33 B3
	10 E3 C2 F7
	02 00 00 00 01 01 01 00 00 67 0A 
	C6 FE 0B E0 11 C1 4E 0D 39 7A 98 C0 C5 0F 78 2A
	[
	11
	]
	03

recvfrom:

	[NO.2067 2014/9/9 14:59:28 recvfrom 63字节]
	02
	35 2F
	00 1D
	33 B3
	10 E3 C2 F7
	00 00 00

	00
	15 2A 4A 10 04 9E 38 44 39 D7 ED E2 69 E3 BA EB
	DC 9E 72 A1 DC 40 68 13 18 90 37 52 96 05 74 56
	80 DF D8 92 AD B9 22 2D 12 B4 96 AA FD EE 34
	[
	11
	00 
	//clientkey
	8E 44 D2 A0 49 3C C6 D2 D6 B5 3C CD AA 49 30 CB
	9F A0 F1 BD F0 10 B7 A1 B6 35 B6 A5 D7 AE 16 17
	]
	03