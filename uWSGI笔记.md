### 一、uwsgi服务启动(start)停止(stop)重新装载(reload)

参考文章：http://blog.51cto.com/12482328/2087535?cid=702003

本文以之前部署的mezzanine为例。可以直接看原文，我这里主要是为了加深自己的记忆。

1. **添加uwsgi相关文件（切换到/root/Blog，与uwsgi.ini文件同级）**

   ```
   #新建uwsgi文件夹
   mkdir uwsgi
   #在刚刚新建的uwsgi文件下新建uwsgi.pid和uwsgi.status文件，uwsgi.pid文件用来重启和停止uwsgi服务，uwsgi.status用来查看uwsgi的服务状态。
   cd uwsgi/
   touch uwsgi.pid
   touch uwsgi.status
   ```

2. **修改uwsgi配置文件**

   于我们之前配置的Blog_uwsgin.ini文件，做如下修改，添加pid文件和status文件的配置

   ```
   stats=%(chdir)/uwsgi/uwsgi.status

   pidfile=%(chdir)/uwsgi/uwsgi.pid
   ```

3. **操作uwsgi**

   启动服务：uwsgi --ini Blog_uwsgi.ini

   验证pid：

   ```
   root@iZwz9fzs35shvq6yjcglx1Z:~# cd /root/Blog
   root@iZwz9fzs35shvq6yjcglx1Z:~/Blog# ls
   Blog  Blog_uwsgi.ini  deploy  dev.db  fabfile.py  __init__.py  manage.py  requirements.txt  static  uwsgi
   root@iZwz9fzs35shvq6yjcglx1Z:~/Blog# cat uwsgi/uwsgi.pid
   18605
   root@iZwz9fzs35shvq6yjcglx1Z:~/Blog# ps -ef|grep uwsgi
   root     18605 18400  0 11:19 pts/2    00:00:00 uwsgi --ini Blog_uwsgi.ini
   root     18607 18605  0 11:19 pts/2    00:00:00 uwsgi --ini Blog_uwsgi.ini
   root     18608 18605  0 11:19 pts/2    00:00:00 uwsgi --ini Blog_uwsgi.ini
   root     18609 18605  0 11:19 pts/2    00:00:00 uwsgi --ini Blog_uwsgi.ini
   root     18610 18605  0 11:19 pts/2    00:00:00 uwsgi --ini Blog_uwsgi.ini
   root     18822 18628  0 11:22 pts/0    00:00:00 grep --color=auto uwsgi
   ```

   用ps命令查看一下uwsgi的进程，发现主进程的pid与我们的pid文件里存的是一样的

   重新加载：uwsgi --reload uwsgi/uwsgi.pid

   获取状态：uwsgi --connect-and-read uwsgi/uwsgi.status

   ```
   root@iZwz9fzs35shvq6yjcglx1Z:~/Blog# uwsgi --connect-and-read uwsgi/uwsgi.status
   {
   	"version":"2.0.17",
   	"listen_queue":0,
   	"listen_queue_errors":0,
   	"signal_queue":0,
   	"load":0,
   	"pid":18605,
   	"uid":0,
   	"gid":0,
   	"cwd":"/root/Blog",
   	"locks":[
   		{
   			"user 0":0
   		},
   		{
   			"signal":0
   		},
   		{
   			"filemon":0
   		},
   		{
   			"timer":0
   		},
   		{
   			"rbtimer":0
   		},
   		{
   			"cron":0
   		},
   		{
   			"rpc":0
   		},
   		{
   			"snmp":0
   		}
   	],
   	"sockets":[
   		{
   			"name":":8000",
   			"proto":"uwsgi",
   			"queue":0,
   			"max_queue":100,
   			"shared":0,
   			"can_offload":0
   		}
   	],
   	"workers":[
   		{
   			"id":1,
   			"pid":18825,
   			"accepting":1,
   			"requests":0,
   			"delta_requests":0,
   			"exceptions":0,
   			"harakiri_count":0,
   			"signals":0,
   			"signal_queue":0,
   			"status":"idle",
   			"rss":0,
   			"vsz":0,
   			"running_time":0,
   			"last_spawn":1526268247,
   			"respawn_count":1,
   			"tx":0,
   			"avg_rt":0,
   			"apps":[
   				{
   					"id":0,
   					"modifier1":0,
   					"mountpoint":"",
   					"startup_time":1,
   					"requests":0,
   					"exceptions":0,
   					"chdir":""
   				}
   			],
   			"cores":[
   				{
   					"id":0,
   					"requests":0,
   					"static_requests":0,
   					"routed_requests":0,
   					"offloaded_requests":0,
   					"write_errors":0,
   					"read_errors":0,
   					"in_request":0,
   					"vars":[

   					],
   					"req_info":					{

   					}
   				}
   			]
   		},
   		{
   			"id":2,
   			"pid":18826,
   			"accepting":1,
   			"requests":0,
   			"delta_requests":0,
   			"exceptions":0,
   			"harakiri_count":0,
   			"signals":0,
   			"signal_queue":0,
   			"status":"idle",
   			"rss":0,
   			"vsz":0,
   			"running_time":0,
   			"last_spawn":1526268247,
   			"respawn_count":1,
   			"tx":0,
   			"avg_rt":0,
   			"apps":[
   				{
   					"id":0,
   					"modifier1":0,
   					"mountpoint":"",
   					"startup_time":1,
   					"requests":0,
   					"exceptions":0,
   					"chdir":""
   				}
   			],
   			"cores":[
   				{
   					"id":0,
   					"requests":0,
   					"static_requests":0,
   					"routed_requests":0,
   					"offloaded_requests":0,
   					"write_errors":0,
   					"read_errors":0,
   					"in_request":0,
   					"vars":[

   					],
   					"req_info":					{

   					}
   				}
   			]
   		},
   		{
   			"id":3,
   			"pid":18827,
   			"accepting":1,
   			"requests":0,
   			"delta_requests":0,
   			"exceptions":0,
   			"harakiri_count":0,
   			"signals":0,
   			"signal_queue":0,
   			"status":"idle",
   			"rss":0,
   			"vsz":0,
   			"running_time":0,
   			"last_spawn":1526268247,
   			"respawn_count":1,
   			"tx":0,
   			"avg_rt":0,
   			"apps":[
   				{
   					"id":0,
   					"modifier1":0,
   					"mountpoint":"",
   					"startup_time":1,
   					"requests":0,
   					"exceptions":0,
   					"chdir":""
   				}
   			],
   			"cores":[
   				{
   					"id":0,
   					"requests":0,
   					"static_requests":0,
   					"routed_requests":0,
   					"offloaded_requests":0,
   					"write_errors":0,
   					"read_errors":0,
   					"in_request":0,
   					"vars":[

   					],
   					"req_info":					{

   					}
   				}
   			]
   		},
   		{
   			"id":4,
   			"pid":18828,
   			"accepting":1,
   			"requests":0,
   			"delta_requests":0,
   			"exceptions":0,
   			"harakiri_count":0,
   			"signals":0,
   			"signal_queue":0,
   			"status":"idle",
   			"rss":0,
   			"vsz":0,
   			"running_time":0,
   			"last_spawn":1526268247,
   			"respawn_count":1,
   			"tx":0,
   			"avg_rt":0,
   			"apps":[
   				{
   					"id":0,
   					"modifier1":0,
   					"mountpoint":"",
   					"startup_time":1,
   					"requests":0,
   					"exceptions":0,
   					"chdir":""
   				}
   			],
   			"cores":[
   				{
   					"id":0,
   					"requests":0,
   					"static_requests":0,
   					"routed_requests":0,
   					"offloaded_requests":0,
   					"write_errors":0,
   					"read_errors":0,
   					"in_request":0,
   					"vars":[

   					],
   					"req_info":					{

   					}
   				}
   			]
   		}
   	]
   }
   ```

   停止：uwsgi --stop uwsgi/uwsgi.pid

































