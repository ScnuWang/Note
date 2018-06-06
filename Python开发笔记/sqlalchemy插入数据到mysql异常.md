---
title: sqlalchemy插入数据到mysql异常
tags: sqlalchemy,mysql
grammar_cjkRuby: true
date:  2018-6-6
---



### 插入中文报错：
 
 在数据库名称后面添加?charset=utf8
 engine = create_engine("mysql+pymysql://root:admin@localhost/data?charset=utf8", echo=True)
 
 
 ### 正常插入，但是提示以下警告：

``` console?linenums
2018-06-06 16:28:39,789 INFO sqlalchemy.engine.base.Engine SHOW VARIABLES LIKE 'sql_mode'
2018-06-06 16:28:39,790 INFO sqlalchemy.engine.base.Engine {}
D:\PythonProjects\venv\lib\site-packages\pymysql\cursors.py:170: Warning: (1366, "Incorrect string value: '\\xD6\\xD0\\xB9\\xFA\\xB1\\xEA...' for column 'VARIABLE_VALUE' at row 480")
  result = self._query(query)
```
代码如下：
``` python?linenums
from sqlalchemy import Column,Integer,String,DECIMAL,create_engine,DateTime
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()


class Product(Base):
    __tablename__ = 'cj_product_test'
    id = Column(Integer,primary_key=True,autoincrement=True)
    original_id = Column(String(255))
    product_name = Column(String(255))
    def __repr__(self):
        return "<Product(id='%d', original_id='%s', product_name='%s')>" % (self.id, self.original_id, self.product_name)

# 插入中文会报错，需要在数据库名称后面添加?charset=utf8
engine = create_engine("mysql+pymysql://root:admin@localhost/data?charset=utf8", echo=True)
Product.metadata.create_all(engine)
DBSession = sessionmaker(bind=engine)
product = Product(original_id='432143',product_name='施华洛世奇')
session = DBSession()
session.add(product)
session.commit()
session.close()
```
MySQL自生的一个BUG:
https://www.cnblogs.com/pengyusong/p/6008936.html
https://bugs.mysql.com/bug.php?id=82414