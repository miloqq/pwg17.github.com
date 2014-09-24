---
layout: post
title: "hadoop运维之java.lang.NoClassDefFoundError"
description: "hadoop java.lang.NoClassDefFoundError 异常解决"
category: "hadoop"
tags: ["hadoop运维"]
---

	./hadoop jar xxxx.jar

hadoop中如果运行jar如果遇到java.lang.NoClassDefFoundError，一般都是classpath设置有问题，导致找不到jar。

查看hadoop-env.sh，可以看到：
	
	# Extra Java CLASSPATH elements.  Automatically insert capacity-scheduler.
	for f in $HADOOP_HOME/contrib/capacity-scheduler/*.jar; do
	if [ "$HADOOP_CLASSPATH" ]; then
        export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$f
    else
    	 export HADOOP_CLASSPATH=$f
    fi
	done

那设置$HADOOP_CLASSPATH这个变量，把hbase的lib目录配进去就行了，或者把hbase/lib下面的所有jar拷贝到hadoop的$HADOOP_CLASSPATH目录下面。

这个命令可以看hadoop都配置了哪些classpath路径：

	./hadoop classpath
	
这就结束了，下面说说远程访问hbase遇到这个问题的解决。
在远程主机执行：

	java -jar xxxx.jar

如果也遇到java.lang.NoClassDefFoundError，也是classpath的问题，jvm找不到你引用的第三方jar，不过解决方法稍微不同。

使用-jar参数运行的时候，java VM会屏蔽所有的外部classpath,而只以本身xxxx.jar的内部class作为类的寻找范围，所以jvm找不到你引用的那些第三方jar。

解决方法有几种，这里只说一种：）

把hadoop、hbase lib目录下的jar和你引用的第三方jar拷贝到{Java_home}\jre\lib\ext目录，这个目录jvm肯定是搜索的。

