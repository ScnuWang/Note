[官方示例地址](https://docs.djangoproject.com/en/2.0/topics/settings/)

[官方完整的配置说明文档](https://docs.djangoproject.com/en/2.0/ref/settings/)

settings.py文件包含所有的Django相关的配置。



### 注意点：

- 如果设置DEBUG = False,则ALLOWED_HOSTS 必须有值例如：ALLOWED_HOSTS = ['www.example.com']
- 不允许有Python语法错误，毕竟配置文件还是一个py文件
- 可以使用普通的Python语法动态分配设置。例如：MY_SETTING = [str(i) for i in range(30)]
- 可以从其他设置文件导入值。
- 默认配置存放在`django/conf/global_settings.py`.配置文件不应从global_settings导入，因为它有很多冗余的配置。
- 自己创建配置文件时：参数名称必须全部大写；不要重新新建已有的配置



### 在代码中调用settings

```python
from django.conf import settings

if settings.DEBUG:
    # Do something
```

> 请注意，您的代码不应从global_settings或您自己的设置文件中导入。 django.conf.settings抽象了默认设置和特定于站点的设置的概念;它提供了一个单一的界面。它还会将使用设置的代码从您的设置位置分离出来。

### 通过configure()配置

`django.conf.settings.``configure`(*default_settings*, **\*settings*)[¶](https://docs.djangoproject.com/en/2.0/topics/settings/#django.conf.settings.configure)

```python
from django.conf import settings

settings.configure(DEBUG=True)
```

根据需要将configure（）传递给许多关键字参数，每个关键字参数表示一个设置及其值。每个参数名称应全部为大写，与上述设置名称相同。如果一个特定的设置没有传递给configure（）并且在稍后的某个时间点需要，Django将使用默认的设置值。

当你在一个更大的应用程序中使用一个框架时，以这种方式配置Django是非常必要的 - 而且的确如此，建议。

因此，当通过settings.configure（）进行配置时，Django将不会对进程环境变量进行任何修改（请参阅[TIME_ZONE](https://docs.djangoproject.com/en/2.0/ref/settings/#std:setting-TIME_ZONE)的文档以了解通常会发生的原因）。假设在这些情况下你已经完全控制了你的环境。

### 疑点：

DJANGO_SETTINGS_MODULE  什么意思？



语言编码：LANGUAGE_CODE = ‘zh-hans’ （中文简体，默认：en-us）