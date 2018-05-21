##### 安装教程：https://segmentfault.com/a/1190000008655555

源码放在/data/wwwroot/120.78.152.69下去掉build这一级目录

##### 1.typecho安装过程中提示"对不起，无法连接数据库，请先检查数据库配置再继续进行安装"

> 之所以出现是因为typecho没有对该目录的写权限而导致的问题.

##### 2.安装主题

> 1. 下载心仪的主题
> 2. 解压上传到/usr/themes目录下
> 3. 登录到Typecho后台---->跟换外观，启动即可

##### 3.主题下载网站：

https://typecho.me/

##### 4.只显示摘要：

在需要截取的地方加上：<!--more-->

##### 5.markdown插件

http://jiongks.name/blog/plugin-markdown/

https://dt27.org/php/editormd-for-typecho/

##### 6.目录插件

https://github.com/wuruowuxin74/MenuTree

##### 7.上传文件失败

修改usr文件夹，已经uploads文件夹权限为777，并且包含子目录