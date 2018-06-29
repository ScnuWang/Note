#### 1. 配置文件检查正常（nginx -t），但是启动的时候提示如下：

```
root@iZwz9fzs35shvq6yjcglx1Z:~/wordpress# nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
root@iZwz9fzs35shvq6yjcglx1Z:~/wordpress# service nginx start
Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details.
```

> 解决办法：
>
> 1. ps -ef | grep  nginx 
> 2. pkill -9 nginx 
>
> 这种应该是端口占用的情况，但是提示信息里面却没有一点占用的信息（摊手）。

####  2. 使用HTTPS访问域名提示：403 Forbidden

​	原因：

​		1.用户权限问题 

​		2.缺少index.html或者index.php文件

​	解决办法：在nginx.conf的开头 添加: user root;

#### 3. 配置好SSL后，访问域名直接是下载文件

​	原因：没有配置解析PHP的代码块：

```
location ~ [^/]\.php(/|$)
        {
            try_files $uri =404;
            fastcgi_pass  unix:/tmp/php-cgi.sock;
            fastcgi_index index.php;
            include fastcgi.conf;
        }
```

