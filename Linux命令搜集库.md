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

2. lsof -i:3306（端口名）

   ​	COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME

   ```
   root@iZwz9fzs35shvq6yjcglx1Z:~# lsof -i:3306
   COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
   mysqld  5946 mysql   14u  IPv6 299475      0t0  TCP *:mysql (LISTEN)
   root@iZwz9fzs35shvq6yjcglx1Z:~# lsof -i:80
   COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
   AliYunDun  888 root   18u  IPv4  14128      0t0  TCP iZwz9fzs35shvq6yjcglx1Z:41028->106.11.68.13:http (CLOSE_WAIT)
   AliYunDun  988 root   18u  IPv4  14128      0t0  TCP iZwz9fzs35shvq6yjcglx1Z:41028->106.11.68.13:http (CLOSE_WAIT)
   AliYunDun  988 root   21u  IPv4  14866      0t0  TCP iZwz9fzs35shvq6yjcglx1Z:41046->106.11.68.13:http (ESTABLISHED)
   nginx     5390 root    6u  IPv4 298969      0t0  TCP *:http (LISTEN)
   nginx     5392  www    6u  IPv4 298969      0t0  TCP *:http (LISTEN)
   
   ```

   > - FD：文件描述符，应用程序通过文件描述符识别该文件。