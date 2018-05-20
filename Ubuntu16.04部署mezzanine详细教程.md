### 一、准备工作

1. 服务器：阿里云ECS 1核2GB内存,是做活动的时候买的，3年279RMB
2. 操作系统：Ubuntu16.04 LTS
3. Python版本：自带python2.7.12  + python 3.5.2
4. 部署方式：Nginx + uWSGI

> Django的部署可以有很多方式，采用Nginx +uwsgi的方式是其中比较常见的一种方式。
>
> 在这种方式中，我们的通常做法是，将Nginx 作为服务器最前端，它将接收WEB的所有请求，统一管理请求。Nginx 把所有静态请求自己来处理（这是Nginx 的强项）。然后，Nginx 将所有非静态请求通过uwsgi传递给Django，由Django来进行处理，从而完成一次WEB请求。
>
> uWSGI的作用就类似一个桥接器,起到桥梁的作用。

### 二、环境搭建

1. 安装pip3

   ```
   apt install python3-pip
   ```

   >若提示：Unable to locate package，先执行apt update
   >
   >升级pip3: pip3 install --upgrade pip

2. 安装Nginx

   ```
   apt install nginx
   ```

3. 安装uWSGI

   ```
   python3 -m pip install uwsgi
   ```

4. 安装mezzanine

   ```
   pip3 install mezzanine
   pip3 install south #用于数据库升级
   ```

5. 初始化mezzanine项目

   ```
   mezzanine-project Blog
   ```

6. 初始化站点

   ```
   cd /root/Blog # 刚刚初始化目录下面
   python3 manage.py createdb
   python3 manage.py runserver  # 监听端口127.0.0.1:8000
   或者
   python3 manage.py runserver  0.0.0.0:80 # 监听端口5800端口，可以外网访问
   ```

7. 使用 uWSGI运行项目 

   ```
   uwsgi --http :80 --chdir /root/Blog --wsgi-file Blog/wsgi.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191 # wsgi.py文件所在位置：/root/Blog/Blog/wsgi.py
   ```

   >常用参数：
   >
   >**http** ： 协议类型和端口号
   >
   >**processes** ： 开启的进程数量
   >
   >**workers** ： 开启的进程数量，等同于processes（官网的说法是spawn the specified number ofworkers / processes）
   >
   >**chdir** ： 指定运行目录（chdir to specified directory before apps loading）
   >
   >**wsgi-file** ： 载入wsgi-file（load .wsgi file）
   >
   >**stats** ： 在指定的地址上，开启状态服务（enable the stats server on the specified address）
   >
   >**threads** ： 运行线程。由于GIL的存在，我觉得这个真心没啥用。（run each worker in prethreaded mode with the specified number of threads）
   >
   >**master** ： 允许主进程存在（enable master process）
   >
   >**daemonize** ： 使进程在后台运行，并将日志打到指定的日志文件或者udp服务器（daemonize uWSGI）。实际上最常用的，还是把运行记录输出到一个本地文件上。
   >
   >**pidfile** ： 指定pid文件的位置，记录主进程的pid号。
   >
   >**vacuum** ： 当服务器退出的时候自动清理环境，删除unix socket文件和pid文件（try to remove all of the generated file/sockets）

### 三、Nginx + uWSGI + mezzanine 整合

1. 新建blog_uwsgi.ini文件（uwsgi支持多种类型的配置文件，如xml，ini等 ）并放在manage.py文件同级目录，将使用wsgi命令启动项目文件化，内容如下：

   ```
    # Blog_uwsgi.ini file
   [uwsgi]
   
   # Django-related settings
   
   #指定项目执行的端口号。
   socket = :8000
   
   # 应用所在目录路径
   chdir = /root/Blog
   
   # 指对于blog_uwsgi.ini文件来说，与它的平级的有一个Blog目录下的wsgi.py文件。
   module = Blog.wsgi
   
   # process-related settings
   # master
   master          = true
   
   # maximum number of worker processes
   processes       = 4
   
   # ... with appropriate permissions - may be needed
   # chmod-socket    = 664
   # clear environment on exit
   vacuum          = true
   ```

2. 通过blog_uswgi.ini文件启动项目

   ```
   uwsgi --ini Blog_uwsgi.ini # 需切换到blog_uwsgi.ini所在目录执行命令
   ```

3. 修改nginx.conf配置文件 /etc/nginx/nginx.conf ）

   ```
   server {
       listen         80; 
       server_name    127.0.0.1 
       charset UTF-8;
       access_log      /var/log/nginx/myweb_access.log;
       error_log       /var/log/nginx/myweb_error.log;
   
       client_max_body_size 75M;
   
       location / { 
           include uwsgi_params;
           uwsgi_pass 127.0.0.1:8000;
           uwsgi_read_timeout 2;
       }   
       location /static {
           expires 30d;
           autoindex on; 
           add_header Cache-Control private;
           alias /root/Blog/static/;
        }
    }
   ```

   >**nginx到底是如何uwsgi产生关联 ?**　
   >
   >​    include uwsgi_params;
   >
   >　uwsgi_pass 127.0.0.1:8000;
   >
   >　include 必须指定为uwsgi_params；而uwsgi_pass指的本机IP的端口号与blog_uwsgi.ini配置中的文件中的必须一致。

4. 启动Nginx

   ```
   service nginx start
   ```

5. 浏览器访问127.0.0.1:

   ![初始页面](C:\Users\ADMINI~1\AppData\Local\Temp\1526111611804.png)

### 四、个人总结

​	这个教程实际上可以延伸到所有的Django项目部署，因为mezzanine 本身就是一个Django项目。









参考文章：

- https://www.cnblogs.com/fnng/p/5268633.html