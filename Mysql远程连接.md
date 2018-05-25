一、lnmp一键安装（默认禁止远程连接）

1. 数据库表修改：

   ```
   GRANT ALL PRIVILEGES ON . TO 'root' @'%' IDENTIFIED BY 'xy@910523' WITH GRANT OPTION;
   FLUSH PRIVILEGES;
   exit;
   ```

2. 查看已有的iptables规则，以序号显示：

   ```
   root@iZwz9fzs35sh:~# iptables -L -n --line-numbers
   Chain INPUT (policy ACCEPT)
   num  target     prot opt source               destination         
   1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
   2    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
   3    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
   4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
   5    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443
   6    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:3306
   7    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0            icmptype 8
   
   Chain FORWARD (policy ACCEPT)
   num  target     prot opt source               destination         
   
   Chain OUTPUT (policy ACCEPT)
   num  target     prot opt source               destination  
   ```

3. 删除对应的DROP规则：

   ```
   iptables -D INPUT 6
   ```

4. 如果是云服务器，还有在服务器安全组里面配置开放3306端口