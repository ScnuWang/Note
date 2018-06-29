
### 一、sched模块 ----- 事件调度程序

[官方说明文档](https://docs.python.org/3.6/library/sched.html)

sched模块定义了一个实现通用事件调度器的类：scheduler

class sched.scheduler(timefunc=time.monotonic, delayfunc=time.sleep)

它需要两个功能来实际处理“外部世界” - timefunc应该可以在没有参数的情况下调用，并返回一个数字（“时间”，以任何单位）。如果time.monotonic不可用，则timefunc默认为time.time。 delayfunc函数应该可以调用一个参数，与timefunc的输出兼容，并且应该延迟很多时间单位。在每个事件运行后，delayfunc也将被调用参数0，以允许其他线程有机会在多线程应用程序中运行。

版本3.3中更改：timefunc和delayfunc参数是可选的。

版本3.3中更改：scheduler class可以安全地用于多线程环境。


示例：

``` python?linenums
>>> import sched, time
>>> s = sched.scheduler(time.time, time.sleep)
>>> def print_time(a='default'):
...     print("From print_time", time.time(), a)
...
>>> def print_some_times():
...     print(time.time())
...     s.enter(10, 1, print_time)
...     s.enter(5, 2, print_time, argument=('positional',))
...     s.enter(5, 1, print_time, kwargs={'a': 'keyword'})
...     s.run()
...     print(time.time())
...
>>> print_some_times()
930343690.257
From print_time 930343695.274 positional
From print_time 930343695.275 keyword
From print_time 930343700.273 default
930343700.276
```

### 二、调度程序对象
调度程序对象实例具有以下方法和属性：

#### scheduler.enterabs(time, priority, action, argument=(), kwargs={})：

	安排新的事件。 time参数应该是一个与传递给构造函数的timefunc函数的返回值兼容的数字类型。计划在同一时间的活动将按其优先顺序执行。较低的数字表示较高的优先级。
	
	执行事件意味着执行动作（*参数，** kwargs）。参数是一个持有动作位置参数的序列。 kwargs是一个持有关键字参数的词典。
	
	返回值是一个事件，可用于稍后取消事件（请参阅cancel（））。
	
	版本3.3中更改：参数参数是可选的。
	
	3.3版新增：kwargs参数被添加。


#### scheduler.enter(delay, priority, action, argument=(), kwargs={})
	
	安排延迟更多时间单位的事件。除了相对时间以外，其他参数，效果和返回值与enterabs（）的相同。
	
	版本3.3中更改：参数参数是可选的。
	
	3.3版新增：kwargs参数被添加。

#### scheduler.cancel(event)
	从队列中删除事件。如果事件不是当前队列中的事件，则此方法将引发ValueError。
	
#### scheduler.empty()
	如果事件队列为空，则返回true
	
#### scheduler.run(blocking=True)
	运行所有计划的事件。此方法将等待（使用传递给构造函数的delayfunc（）函数）执行下一个事件，然后执行此操作，直到没有更多预定事件。
	
	如果阻塞为假，则执行预定的事件，因为最快到期（如果有），然后返回调度程序中下一个预定呼叫的最后期限（如果有）。
	
	action或delayfunc可以引发异常。无论哪种情况，调度程序都将保持一致的状态并传播异常。如果通过操作引发异常，则在将来调用run（）时不会尝试该事件。
	
	如果一系列事件的运行时间比下一个事件之前的可用时间长，那么调度器将会落后。没有事件会被丢弃;调用代码负责取消不再相关的事件。
	
	新增3.3版本：阻止参数。
#### scheduler.queue
	只读属性按照它们将运行的顺序返回即将发生的事件列表。每个事件都显示为具有以下字段的命名元组：时间，优先级，动作，参数，kwargs。