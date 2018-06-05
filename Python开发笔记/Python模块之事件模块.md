---
title: Python模块之事件模块
tags: datatime,time,python
grammar_cjkRuby: true
date:  2018-6-5
---


[python time模块和datetime模块详解](https://www.cnblogs.com/tkqasn/p/6001134.html)

一、time模块

	time.time()的结果返回值是float类型的
	
	time模块中时间表现的格式主要有三种：

　　a、timestamp时间戳，时间戳表示的是从1970年1月1日00:00:00开始按秒计算的偏移量

　　b、struct_time时间元组，共有九个元素组。

　　c、format time 格式化时间，已格式化的结构使时间更具可读性。包括自定义格式和固定格式。