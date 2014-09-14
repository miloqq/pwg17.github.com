---
layout: post
title: "2014 6.1 PC QQ UDP协议分析系列之0x0001【获取好友自定义分组】篇"
description: "pc qq 协议分析"
category: "协议分析"
tags: ["协议分析", "逆向"]
---
#####本文仅出于研究目的，如有法律风险，请联系删帖，谢绝跨市。  
<br/>
sendto:

	[NO.86 2014/9/9 14:46:28 sento 47字节]
	02
	35 2F
	00 01
	3F 5D
	10 E3 C2 F7
	02 00 00 00 01 01 01 00 00 67 0A 
	
	8C A6 49 5E 7F 80 71 88 
	64 05 55 D3 23 B8 D7 8A 12 E3 86 DE CF D6 E7 8A
	[
	20
	01 00 00 00 00 01 00 00
	]
	03

recvfrom:

	[NO.91 2014/9/9 14:46:28 recvfrom 175字节]
	02
	35 2F
	00 01
	3F 5D
	10 E3 C2 F7
	00 00 00

	D5
	35 37 9B 95 C2 A2 5C 5B 39 2D F7 8C 8A FA E8 8D
	E9 4E CA DE 8E FA 73 A0 E0 BE 9B 84 D0 4C D3 16
	0A 1B 85 A6 A0 D8 F3 ED C3 BF 83 F5 FA 07 13 9F
	8C 83 FA 83 5C 5A A8 C8 EF 81 5B 50 F9 09 5B 10
	B3 5B DE 3F 0B D2 C0 6D 2A B3 5D 02 67 E1 47 A0
	C3 1C 43 A3 C2 A9 0E 62 55 CD 9C E7 FB CC E5 03
	EB BD ED 83 57 3E 1C 09 C6 F3 2F 6C 9E C2 A2 2B
	69 18 84 B2 EA EE C1 70 E3 1A 60 77 38 F2 BA 9A
	D5 36 4A F7 3B A6 C5 BD 8C C9 69 4D B1 C3 DC 9A
	E5 7C 69 48 F6 0C 63 90 25 C3 32 03 18 6D 49
	[
	20 
	00 01 0A 00 00 00 51 01 01 
	//分组个数
	0A

	//默认分组 “我的好友” 编号是 00

	00 0F[该分组字节长度] 01[分组编码] 06
	//分组名长度，UTF-8编码 
	0C 
	//一线之隔
	E4 B8 80 E7 BA BF E4 B9 8B E9 9A 94 

	00 09[该分组字节长度] 02[分组编号] 04 
	//分组名长度，UTF-8编码
	06 
	//
	XX XX XX XX XX XX 

	00 09 03 03 
	06 
	//
	XX XX XX XX XX XX

	00 09 04 05 
	06 
	//
	XX XX XX XX XX XX

	00 12 05 07
	0F
	//
	XX XX XX XX XX XX XX XX XX XX XX XX XX XX XX

	00 09 06 02 
	06 
	//
	XX XX XX XX XX XX

	00 09 07 0B 
	06
	//
	XX XX XX XX XX XX 

	00 0F 08 08 
	0C 
	//
	XX XX XX XX XX XX XX XX XX XX XX XX 

	00 09 09 09 
	06 
	//
	XX XX XX XX XX XX 

	00 0F 0A 0A 
	0C 
	//
	XX XX XX XX XX XX XX XX XX XX XX XX 

	//结束标记
	00 00
	]
	03