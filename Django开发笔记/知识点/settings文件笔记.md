### 配置修改

```python

LANGUAGE_CODE = 'zh-hans' # 中文繁体 ： zh-hant

TIME_ZONE = 'Asia/Shanghai' 

# 当执行：python manage.py collectstatic 会将项目内所有的静态文件搜到STATIC_ROOT
STATIC_ROOT = os.path.join(BASE_DIR,'static')

```

### 引用配置文件配置
```python?linenums

from  django.conf import settings

```

### 规范

应用注册顺序：Django自带应用，第三方应用，自己的应用

### 常用配置

