### 一、通用的命令及相关笔记

1. ps -ef|grep uwsgi（服务名）

   字段含义如下：
   	UID       PID       PPID      C     STIME    TTY       TIME         CMD

   ```
   root@iZwz9fzs35shvq6yjcglx1Z:~/Blog# ps -ef|grep uwsgi
   root     15637     1  0 May12 ?        00:00:08 /usr/local/bin/uwsgi --ini Blog_uwsgi.ini
   root     15927 15637  0 May12 ?        00:00:05 /usr/local/bin/uwsgi --ini Blog_uwsgi.ini
   root     15928 15637  0 May12 ?        00:00:00 /usr/local/bin/uwsgi --ini Blog_uwsgi.ini
   root     15929 15637  0 May12 ?        00:00:01 /usr/local/bin/uwsgi --ini Blog_uwsgi.ini
   root     15930 15637  0 May12 ?        00:00:00 /usr/local/bin/uwsgi --ini Blog_uwsgi.ini
   root     18594 18400  0 11:11 pts/2    00:00:00 grep --color=auto uwsgi
   ```

   > 字段说明：
   >
   > UID      ：程序被该 UID 所拥有	PID      ：就是这个程序的 ID 	PPID    ：则是其上级父程序的ID
   >
   > C          ：CPU使用的资源百分比	
   >
   > STIME ：系统启动时间	
   >
   > TTY     ：登入者的终端机位置
   >
   > TIME   ：使用掉的CPU时间。
   >
   > CMD   ：所下达的是什么指令