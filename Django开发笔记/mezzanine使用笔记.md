### 加入Markdown支持

https://pypi.org/project/mezzanine-pagedown/#description

- 安装

> pip install mezzanine-pagedown Pygments

- 在settings.py文件中注入mezzanine-pagedown组件

```
INSTALLED_APPS = (
    "mezzanine_pagedown",  # 这里
    "django.contrib.admin",
    "django.contrib.auth",
```

- 在setting.py中加入mezzanine-pagedown的配置信息

```
#####################
# PAGEDOWN SETTINGS #
#####################
RICHTEXT_WIDGET_CLASS = 'mezzanine_pagedown.widgets.PageDownWidget'
RICHTEXT_FILTERS = ['mezzanine_pagedown.filters.codehilite',]
PAGEDOWN_MARKDOWN_EXTENSIONS = ('extra','codehilite','toc')
RICHTEXT_FILTER_LEVEL = 3  # 防止站点把>当作错误。
PAGEDOWN_SERVER_SIDE_PREVIEW = False # markdown应该是Client来Render的。
```

- 编辑urls.py文件，加入mezzanine_pagedown的url

```
import mezzanine_pagedown.urls
  
  ("^pagedown/", include(mezzanine_pagedown.urls)),
```

![1526023706749](C:\Users\geekview\AppData\Local\Temp\1526023706749.png)