1. 安装教程：
2. https://www.cnblogs.com/bari/p/7825763.html
3. https://www.jianshu.com/p/56750622cac9
4. 初始默认密码：是在安装时自动生成的那个复杂密码：B)JUsOx3q^sPmz(mqZ
5. AKISMET API KEY ：f01ffefd5cd8
6. 

| **邮箱**    | **POP3 服务器（端口110）** | **SMTP 服务器（端口25）** |
| ----------- | -------------------------- | ------------------------- |
| 188.com     | pop3.188.com               | smtp.188.com              |
| 163.com     | pop3.163.com               | smtp.163.com              |
| 126.com     | pop3.126.com               | smtp.126.com              |
| netease.com | pop.netease.com            | smtp.netease.com          |
| yeah.net    | pop.yeah.net               | smtp.yeah.net             |

7. 启用https:

   ```
   进入到Wordpress安装目录下，复制以下代码至wp-config.php末尾
   define('FORCE_SSL_ADMIN', true);
   define('FORCE_SSL_LOGIN', true);
   ```

8. 新建页面之后访问提示404

   ```
   #在Nginx配置文件中添加以下代码：
   if (-f $request_filename/index.html){
   rewrite (.*) $1/index.html break;
   }
   
   if (-f $request_filename/index.php){
   rewrite (.*) $1/index.php;
   }
   
   if (!-f $request_filename){
   rewrite (.*) /index.php;
   }
   ```

   