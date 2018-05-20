1. 解决docker build慢的问题（Ubuntu18.04 完美解决）

   ```
   curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://a44bccec.m.daocloud.io 
   ```

   配置加速器：https://www.daocloud.io/mirror#accelerator-doc

   ```
   jason@jason:~$ curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://a44bccec.m.daocloud.io
   docker version >= 1.12
   [sudo] jason 的密码： 
   {"registry-mirrors": ["http://a44bccec.m.daocloud.io"]}
   Success.
   You need to restart docker to take effect: sudo systemctl restart docker.service
   jason@jason:~$ sudo systemctl restart docker.service
   ```

   

