---
layout: post
title: "Python开发"
date: 2017-08-26
tags: python
---


#### 常用技巧

1. 判断IP是否合法

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


### 标题2

不知道该何去何从

## 标题3

低调做人，高调做事

```python
def print_function(_str):
    print "Hello Word!"
```

>* 1. 内容1

>* 2. 内容2

**这是什么,推荐一部感人的动画电影** `你的名字`

福利链接地址:
[豆腐西施全套种子](http://www.pyfeng.com)

逝去的激情

```
import os
def test(_str):
   print "a"
```
