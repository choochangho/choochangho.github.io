---
layout: post
title:  "Anaconda fontList.py3k.cache Not found error"
categories: anaconda python matplotlib
date:   2016-11-29 09:56
permalink: /archivers/anaconda-fontlist-error
---

# matplotlib font list 에러 발생

```python
import matplotlib.pyplot as plt
```

```shell
Traceback (most recent call last):
  File "C:\Anaconda3\lib\site-packages\matplotlib\font_manager.py", line 1412, in <module>
    fontManager = pickle_load(_fmcache)
  File "C:\Anaconda3\lib\site-packages\matplotlib\font_manager.py", line 963, in pickle_load
    with open(filename, 'rb') as fh:
FileNotFoundError: [Errno 2] No such file or directory: 'C:\\Users\\UserName\\.matplotlib\\fontList.py3k.cache'
```

# font_manager.py 수정

231 번째 line 을 수정 한다.

```python
#direc = os.path.abspath(direc).lower()
direc = direc.split ('\0' , 1) [0]
```
