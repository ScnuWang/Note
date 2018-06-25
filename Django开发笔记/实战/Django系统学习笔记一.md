### Django命令

创建项目：
	django-admin startproject project_name
	
查看manage.py 有那些命令可用
	python manage.py  help

启动项目：
	python manage.py runserver

数据库迁移
	python manage.py makemigrations
	python manage.py migrate
	
创建超级管理员
	python manage.py  createsuperuser
	
创建应用
	python manage.py startapp app_name
	
创建缓存表
	python manage.py createcachetable

	
### Django知识点
1. urls.py
	
	访问路径配置：
	
		2.x 使用的path,正则表达式使用re_path;
							
		1.x 使用的版本是url，
		
2. 访问admin之前要先迁移数据库，否则会提示：no such table: django_session等

	python manage.py makemigrations
	
	python manage.py migrate
	
	
### Python 知识点

同一个目录，引用用.代替