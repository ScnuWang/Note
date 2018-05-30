1. 安装教程：
2. https://www.cnblogs.com/bari/p/7825763.html
3. https://www.jianshu.com/p/56750622cac9
4. 初始默认密码：是在安装时自动生成的那个复杂密码：B)JUsOx3q^sPmz(mqZ
5. AKISMET API KEY ：f01ffefd5cd8
6. 启用https:

```
进入到Wordpress安装目录下，复制以下代码至wp-config.php末尾
define('FORCE_SSL_ADMIN', true);
define('FORCE_SSL_LOGIN', true);
```
7. 新建页面之后访问提示404

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
8. 上传图片提示Http错误。

​	描述：上午上传时正常的，下午就上传不了，修复在上传图像时出现的 HTTP 错误。

​	原因：搞半天是由于打开了XX-NET梯子。

​	解决办法：撤掉梯子

​	扩展：https://zhuanlan.zhihu.com/p/34339259这里搜集了会出现这类错误的解决办法。

