---
layout: post
title: "Python开发"
date: 2017-08-26
tags: python
---


### 常用

* **判断IP是否合法**

```
import socket
def valid_ip(address):
    try:
        socket.inet_aton(address)
        return True
    except:
        return False
if __name__ == "__main__":
    print valid_ip("1.1.1.1")
    print valid_ip("2,1.1.1")
```
* **获取机器主机名**

```
import socket
hostname = socket.gethostname()
print hostname
```

* **缓存结果, 提高性能**

```
import time
import functools

def memo(func):
    "Memoize function f."
    table = {}
    def fmemo(args):
        if args not in table:
            table[args] = func(args)
        return table[args]
    fmemo.memo = table
    return fmemo

def clock(func):
    @functools.wraps(func)
    def clocked(*args, **kwargs):
        t0 = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - t0
        name = func.__name__
        arg_list = []
        if args:
            arg_list.append(", ".join(repr(arg) for arg in args))
        if kwargs:
            pairs = ['%s=%r' % (k, w) for k, w in sorted(kwargs.items())]
            arg_list.append(", ".join(pairs))
        arg_str = ", ".join(arg_list)
        print('[%0.8fs] %s(%s) -> %r ' % (elapsed, name, arg_str, result))
        return result
    return clocked

# 在 Python 的 3.2 版本中，引入了一个非常优雅的缓存机器，即 functool 模块中的("Least Recently Used") lru_cache 装饰器
# 如果要在 python2 中使用 lru_cahce 需要安装 functools32
@memo  # 同 @functools.lru_cache() 等价
@clock
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-2) + fibonacci(n-1)

if __name__ == "__main__":
    fibonacci(10)
```

* **进程池,需要结果统计**

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import multiprocessing
import time

def func(msg):
    print('hello :', msg, time.ctime())
    time.sleep(2)
    print('end :', msg, time.ctime())
    return 'done' + msg

if __name__=='__main__':
    pool = multiprocessing.Pool(2)
    result = []
    for i in range(5):
        msg = 'hello %s' %i
        result.append(pool.apply_async(func=func,args=(msg,)))

    pool.close()
    pool.join()

    for res in result:
        print('------------:',res.get())

    print('--All End--')
```

### 单元测试

```
测试是一个贯穿于整个开发过程的连续过程，从某个意义上说，软件开发的过程实际上就是测试过程。正如Martin Fowler所说的"在你不知道如何测试代码之前，就不该编写程序。而一旦你完成了程序，测试代码也应该完成。除非测试成功，你不能认为你编写出了可以工作的程序 测试最基本的原理就是比较预期结果是否与实际执行结果相同，如果相同则测试成功，否则测试失败。为了更好地理解PyUnit这一自动测试框架的作用，先来看一个简单的例子，假设我们要对例1中的Widget类进行测试：

例1. widget.py
# 将要被测试的类
class Widget:
    def __init__(self, size = (40, 40)):
        self._size = size
    def getSize(self):
        return self._size
    def resize(self, width, height):
        if width  0  or height < 0:
            raise ValueError, "illegal size"
        self._size = (width, height)
    def dispose(self):
        pass


# 执行测试的类
class TestWidget:
    def testSize(self):
        expectedSize = (40, 40);
        widget = Widget()
        if widget.getSize() == expectedSize:
            print "test [Widget]: getSize works perfected!"
        else:
            print "test [Widget]: getSize doesn't work!"
# 测试
if __name__ == '__main__':
    myTest = TestWidget()
    myTest.testSize()

稍一留心你不难发现这种手工测试方法存在许多问题。首先，测试程序的写法没有一定的规范可以遵循，十个程序员完全可能写出十种不同的测试程序来，如果每个Python程序员都有自己不同的设计测试类的方法，光维护被测试的类就够麻烦了，谁还顾得上维护测试类。其次，需要编写大量的辅助代码才能进行单元测试，例1中用于测试的代码甚至比被测试的代码还要多，而这毫无疑问将增大Python程序员的工作量。

为了让单元测试代码能够被测试和维护人员更容易地理解，最好的解决办法是让开发人员遵循一定的规范来编写用于测试的代码，具体到Python程序员来讲，则是要采用PyUnit这一自动测试框架来构造单元测试用例。目前PyUnit已经得到了大多数Python开发人员的认可，成了事实上的单元测试标准。如果采用PyUnit来进行同样的测试，则测试代码将如例3所示：

例3. auto.py
from widget import Widget
import unittest
# 执行测试的类
class WidgetTestCase(unittest.TestCase):
    def setUp(self):
        self.widget = Widget()
    def tearDown(self):
        self.widget = None
    def testSize(self):
        self.assertEqual(self.widget.getSize(), (40, 40))
# 构造测试集
def suite():
    suite = unittest.TestSuite()
    suite.addTest(WidgetTestCase("testSize"))
    return suite
# 测试
if __name__ == "__main__":
    unittest.main(defaultTest = 'suite')

在采用PyUnit这一单元测试框架后，用于测试的代码做了相应的改动：

用import语句引入unittest模块。
让所有执行测试的类都继承于TestCase类，可以将TestCase看成是对特定类进行测试的方法的集合。
在setUp()方法中进行测试前的初始化工作，并在tearDown()方法中执行测试后的清除工作，setUp()和tearDown()都是TestCase类中定义的方法。
在testSize()中调用assertEqual()方法，对Widget类中getSize()方法的返回值和预期值进行比较，确保两者是相等的，assertEqual()也是TestCase类中定义的方法。
提供名为suite()的全局方法，PyUnit在执行测试的过程调用suit()方法来确定有多少个测试用例需要被执行，可以将TestSuite看成是包含所有测试用例的一个容器。
虽然看起来有点复杂，但PyUnit使得所有的Python程序员都可以使用同样的单元测试方法，测试过程不再是杂乱无章的了，而是在同一规范指导下进行的有序行为，这就是使用PyUnit这一自动单元测试框架所带来的最大好处。
```

[首页](http://www.pyfeng.com)

