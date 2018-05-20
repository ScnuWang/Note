### 一、准备工作

1. 服务器：阿里云ECS 1核2GB内存,是做活动的时候买的，3年279RMB
2. 操作系统：Ubuntu16.04 LTS
3. Python版本：自带python2.7.12  + python 3.5.2
4. 部署方式：Nginx + uwsgi

> Django的部署可以有很多方式，采用Nginx +uwsgi的方式是其中比较常见的一种方式。
>
> 在这种方式中，我们的通常做法是，将Nginx 作为服务器最前端，它将接收WEB的所有请求，统一管理请求。Nginx 把所有静态请求自己来处理（这是Nginx 的强项）。然后，Nginx 将所有非静态请求通过uwsgi传递给Django，由Django来进行处理，从而完成一次WEB请求。
>
> uwsgi的作用就类似一个桥接器,起到桥梁的作用。

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

3. 安装uwsgi

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

   ​

