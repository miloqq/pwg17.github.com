---
layout: post
title: "记一次有趣的采集分析"
description: "爬虫采集%2F分析"
category: "爬虫"
tags: ["爬虫", "HTTP协议分析"]
---

[http://www.emeraldinsight.com/toc/f/32/11%2F12](http://www.emeraldinsight.com/toc/f/32/11%2F12)

这个链接浏览器是可以打开的，%2F是 / 的转义，但下面这样是打不开的：

[http://www.emeraldinsight.com/toc/f/32/11/12](http://www.emeraldinsight.com/toc/f/32/11/12)

可能是对方防采集的措施，或者是其他原因，这个不管了，爬虫采集的话，自然是原汁原味的模拟浏览器行为，用最上面那个链接來采集。

但，程序中你会遇到404，打不开这个页面，采集不到内容。

这个时候自然是上http协议分析的神器Fiddler分析一番，（tcp或udp协议用另外一个神器wireshark。）用这个抓包对比程序发出的请求和浏览器发出的请求的差别，经过一番添加UserAgent、referer、cookie模拟都不行之后，发现差别是c#发出的请求把url中的%2F给转义回 / 了。呃，前面说过把%2F转义成 / 连浏览器都打不开的。

找到问题自然就好解决了，把c#对url中的%2F自动转义取消就行了。

###0x00 .net 2.0的解决方案：

c#中Uri有个构造函数：

	public Uri(
		string uriString,
		bool dontEscape
	)

第二个参数用true就行了，

	Uri uri = new Uri("http://xxxx",true);
	
###0x01 .net 4.0 之后的解决方案：
4.0之后的框架上面的解决方案失效了，因为Uri类改了，看文档：

	[ObsoleteAttribute("The constructor has been deprecated. Please use new Uri(string). The dontEscape parameter is deprecated and is always false. http://go.microsoft.com/fwlink/?linkid=14202")]
	public Uri(
		string uriString,
		bool dontEscape
	)
	
嗯，不管外面传什么，dontEscape参数永远是false了。

.net 4.0之后，在配置文件中改，添加下面的节点就行了：

	<configuration>
  		<uri>
    		<schemeSettings>
      			<add name="http|https" genericUriParserOptions="DontUnescapePathDotsAndSlashes"/>
    		</schemeSettings>
  		</uri>
	</configuration>
	

ok，打完收工。

下面再给一个备用方案，预防万一：
###0x02 备用方案，利用Reflection修改Uri。

{% highlight csharp %}

	namespace MiniNet.Net
	{
    	using System;
    	using System.Collections.Generic;
    	using System.Linq;
    	using System.Text;
    	using System.Reflection;

    	/// <summary>
    	/// TODO: Update summary.
    	/// </summary>
    	public static class UriHelper
    	{
        	private const int UnEscapeDotsAndSlashes = 0x2000000;

        	public static Uri DontEscape(this Uri uri)
        	{
            	if (uri == null)
            	{
                	throw new ArgumentNullException("uri");
            	}

            	FieldInfo fieldInfo = uri.GetType().GetField("m_Syntax", BindingFlags.Instance | BindingFlags.NonPublic);
            	if (fieldInfo == null)
            	{
                	throw new MissingFieldException("'m_Syntax' field not found");
            	}
            	object uriParser = fieldInfo.GetValue(uri);

            	fieldInfo = typeof(UriParser).GetField("m_Flags", BindingFlags.Instance | BindingFlags.NonPublic);
            	if (fieldInfo == null)
            	{
                	throw new MissingFieldException("'m_Flags' field not found");
            	}
            	object uriSyntaxFlags = fieldInfo.GetValue(uriParser);

            	// Clear the flag that we don't want
            	uriSyntaxFlags = (int)uriSyntaxFlags & ~UnEscapeDotsAndSlashes;

            	fieldInfo.SetValue(uriParser, uriSyntaxFlags);

            	return uri;
        	}
    	}
	}

{% endhighlight %}

这样调用：

	Uri uri = new Uri("http://xxxx",true).DontEscape();
	
done，只要浏览器能访问，没有采集不了的页面！

参考链接：

[http://msdn.microsoft.com/en-us/library/9zh9wcb3\(v=vs.110\).aspx](http://msdn.microsoft.com/en-us/library/9zh9wcb3\(v=vs.110\).aspx)
<br/>
[http://msdn.microsoft.com/en-us/library/ee656539\(v=vs.110\).aspx](http://msdn.microsoft.com/en-us/library/ee656539\(v=vs.110\).aspx)



