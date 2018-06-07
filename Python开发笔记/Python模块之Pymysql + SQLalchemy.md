---
title: Python模块之Pymysql + SQLalchemy
tags: python,mysql
grammar_cjkRuby: true
date:  2018-6-6
---

[SQlalchemy官方文档](http://docs.sqlalchemy.org/en/latest/core/tutorial.html)

### 官方文档关键点
除了创建表格时，String上的length字段以及Integer，Numeric等上提供的类似精度/比例字段不会被SQLAlchemy引用。





### 相关博文

Pymysql + SQLalchemy
https://www.cnblogs.com/aylin/p/5770888.html

游标设置为字典类型(默认获取的数据是元祖类型)
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)


