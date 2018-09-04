---
layout: post
title: "Python开发"
date: 2017-08-26
tags: python
---


### 常用技巧

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

[首页](http://www.pyfeng.com)

