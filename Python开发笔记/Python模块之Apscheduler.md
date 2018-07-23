[官方文档](https://apscheduler.readthedocs.io/en/v3.5.1/userguide.html)
[API文档](http://apscheduler.readthedocs.io/en/latest/py-modindex.html)
2. 1. 安装
	通过anaconda安装，不知道为什么，默认的conda找不到apsscheduler库，可能是没有爬梯子的原因。
	于是，使用国内镜像通道
	
``` 
	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/ 
	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/ 
	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/ 
	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/menpo/ 
```
安装：conda install apscheduler 

2. 简单应用

``` python
import time
from apscheduler.schedulers.blocking import BlockingScheduler
 
def my_job():
    print time.strftime('Hello World！')
 
sched = BlockingScheduler()
# 每过5秒打印一次Hello World！
# 可以使用修饰器@sched.scheduled_job('interval', seconds=5)代替
# interval 间隔调度（每隔多久执行）;cron 定时调度 ;date 定时调度（任务只会执行一次）
sched.add_job(my_job, 'interval', seconds=5)
sched.start()
```
	