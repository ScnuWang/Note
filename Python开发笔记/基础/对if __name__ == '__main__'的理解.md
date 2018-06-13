---
title: 对if __name__ == '__main__'的理解
tags: python
grammar_cjkRuby: true
date:  2018-6-4
---



学习来源：[请看这里的第一条评论](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431845183474e20ee7e7828b47f7b7607f2dc1e90dbb000#0)
	
一直没有理解到`if __name__ == '__main__'`的真正含义，今天看到这里算是说的比较清楚易懂的，为了巩固一下，所以摘抄下来。

先编写一个测试模块atestmodule.py

``` python?linenums
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

def addFunc(a,b):  
    return a+b  

print('atestmodule计算结果:',addFunc(1,1))
```
再编写一个模块anothertestmodule.py来调用上面的模块：

``` python?linenums
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

import atestmodule

print('调用anothermodule模块执行的结果是：',atestmodule.addFunc(12,23))
```
在刚才两个模块的路径（我的路径为：“C:\work”）中打开cmd，用命令行运行atestmodule.py：

``` cmd?linenums
C:\work>python atestmodule.py
atestmodule计算结果: 2
```
在刚才两个模块的路径中打开，用命令行运行anothertestmodule.py：

``` cmd?linenums
C:\work>python anothertestmodule.py
atestmodule计算结果: 2
调用test模块执行的结果是： 35

#显然，当我运行anothertestmodule.py后第一句并不是调用者所需要的，为了解决这一问题，Python提供了一个系统变量：__name__

#注：name两边各有2个下划线__name__有2个取值：当模块是被调用执行的，取值为模块的名字；当模块是直接执行的，则该变量取值为：__main__
```

于是乎，被调用模块的测试代码就可以写在if语句里了，如下：

``` python?linenums
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

def addFunc(a,b):  
    return a+b  

if __name__ == '__main__':  
    print('atestmodule计算结果:',addFunc(1,1))
```
当再次运行atestmodule.py：

``` cmd?linenums
C:\work>python atestmodule.py
atestmodule计算结果: 2

#结果并没有改变，因为调用atestmodule.py时，__name__取值为__main__，if判断为真，所以就输出上面的结果
```
当再次运行atestmodule.py：

``` cmd?linenums
C:\work>python anothertestmodule.py
调用test模块执行的结果是： 35

#此时我们就得到了预期结果，不输出多余的结果。能实现这一点的主要原因在于当调用一个module时，此时的__name__取值为模块的名字，所以if判断为假，不执行后续代码。
```
所以代码if name == 'main': 实现的功能就是Make a script both importable and executable，也就是说可以让模块既可以导入到别的模块中用，另外该模块自己也可执行。