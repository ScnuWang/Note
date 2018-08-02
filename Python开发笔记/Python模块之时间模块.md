[python time模块和datetime模块详解](https://www.cnblogs.com/tkqasn/p/6001134.html)

一、time模块

	time.time()的结果返回值是float类型的
	
	time模块中时间表现的格式主要有三种：
	
	 a、timestamp时间戳，时间戳表示的是从1970年1月1日00:00:00开始按秒计算的偏移量
	 
	 b、struct_time时间元组，共有九个元素组。
	 
	 c、format time 格式化时间，已格式化的结构使时间更具可读性。包括自定义格式和固定格式。
	 
``` python?linenums
import time

# time 模块


# print(time.time())
#
# print(time.mktime(time.localtime()))
#
# print(time.localtime())
#
# print(time.localtime(time.time()))
#
# print(time.gmtime()) # 格林威治时间
# print(time.gmtime(time.time()))
#
# print(time.strptime('2018-06-05 14:41:06', '%Y-%m-%d %X'))
#
# print(time.strftime('%Y-%m-%d %X'))
# print(time.strftime('%Y-%m-%d %X',time.localtime()))
#
# print(time.asctime(time.localtime()))
# print(time.ctime(time.time()))

'''
1528181125.936323
1528181125.0
time.struct_time(tm_year=2018, tm_mon=6, tm_mday=5, tm_hour=14, tm_min=45, tm_sec=25, tm_wday=1, tm_yday=156, tm_isdst=0)
time.struct_time(tm_year=2018, tm_mon=6, tm_mday=5, tm_hour=14, tm_min=45, tm_sec=25, tm_wday=1, tm_yday=156, tm_isdst=0)
time.struct_time(tm_year=2018, tm_mon=6, tm_mday=5, tm_hour=6, tm_min=45, tm_sec=25, tm_wday=1, tm_yday=156, tm_isdst=0)
time.struct_time(tm_year=2018, tm_mon=6, tm_mday=5, tm_hour=6, tm_min=45, tm_sec=25, tm_wday=1, tm_yday=156, tm_isdst=0)
time.struct_time(tm_year=2018, tm_mon=6, tm_mday=5, tm_hour=14, tm_min=41, tm_sec=6, tm_wday=1, tm_yday=156, tm_isdst=-1)
2018-06-05 14:45:25
2018-06-05 14:45:25
Tue Jun  5 14:45:25 2018
Tue Jun  5 14:45:25 2018
'''
```

二、datetime模块

``` python?linenums
from datetime import datetime

print(datetime.now()) # 2018-08-02 15:21:46.118491
print(datetime.now().date()) # 2018-08-02
print(datetime.now().time()) # 15:21:46.118491
print(datetime.now().timestamp()) # 1533194506.118491
print(datetime.now().strftime('%Y-%m-%d %X')) # 2018-08-02 15:21:46

# 时间戳转为时间
print(datetime.fromtimestamp(datetime.now().timestamp())) # 2018-08-02 15:21:46.118491
```