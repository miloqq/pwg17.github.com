---
layout: post
title: curl install jdk
description: "curl 安装 jdk"
category: "shell"
tags: ["shell"]
---



现在oracle安装个jdk还得登录点击什么的，在终端下很不方便。可以用curl在终端下安装：

{% highlight javascript %}

curl -L --header "Cookie: s_nr=1359635827494; s_cc=true; gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk7-downloads-1880260.html; s_sq=%5B%5BB%5D%5D; gpv_p24=no%20value" http://download.oracle.com/otn-pub/java/jdk/7u25-b15/jdk-7u25-linux-x64.tar.gz -o jdk-7u25-linux-x64.tar.gz

{% endhighlight %}