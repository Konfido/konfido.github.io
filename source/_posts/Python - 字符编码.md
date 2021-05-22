---
title: Python encode decode & 字符编码检测
alias: [encoding]
tags: [python]
typora-copy-images-to: ../images
date: 2019-04-18 00:04:12
updated: 2019-04-18 00:04:12
blogged: true
urlname: encoding-and-decoding-in-Python
---

## 编码类型

- 万国码/字码表：unicode
- 编码方法 （将unicode数据编码成byte数据）：产生不同的字符集
  - 英文：ascii，utf-8
  - 中文：utf-8，utf-16，gbk

## 编码转换

- `encode()`： str (unicode) -> bytes
- `decode()`：bytes -> str (unicode)
- 不同字符集之间通过Unicode转换：utf-8->unicode->gbk



## Note

- Python 2
    - 默认编码 **ASCII**，自动处理byte到unicode的转换，处理非ASCII时易出错。
    - 两种字符类型
        - `unicode`：unicode 数据
        - `str`：bytes 数据
    - `unicode`： `u'苑'` / `u'\u2d1'`
        - encode('utf-8')： `\xe8\x8b\x91` （`str`，bytes）
        - encode('gbk') ： `\xd4\xb7` （`str`，bytes）

- Python 3

    - 默认编码 **Unicode**，不会再对bytes数据自动解码
    - 两种字符类型
        - `str`：unicode 数据
        - `bytes`：bytes 数据
        - **文本总是Unicode**，由`str`类型表示，二进制数据由`bytes`类型表示
    - `str`：`'苑'` / `\u82d1`
        - encode('utf-8')：`b'\xe8\x8b\x91'`  (`bytes`)
        - encode('gbk')：`'\xd4\xb7'` (`bytes`)
    - Example
        ```python
        >>> c="編碼abc"			# 字符串str为unicode数据，可以encode为bytes
        >>> c
        '編碼abc'
        >>> c.encode('utf-8')
        b'\xe7\xb7\xa8\xe7\xa2\xbcabc'
        >>> c.encode('utf-16')
        b'\xff\xfe\xe8}\xbcxa\x00b\x00c\x00'
        >>> c.encode('gbk')
        b'\xbe\x8e\xb4aabc'
        >>> c.encode('unicode_escape')
        b'\\u7de8\\u78bcabc'
        
        >>> d=u'\u7de8\u78bcabc'	# d为16进制unicode字符
        >>> d						# 可以看出python3中对d与c的处理结果是相同的
        "編碼abc"
        >>> d.encode('utf-8')
        b'\xe7\xb7\xa8\xe7\xa2\xbcabc'
        >>> d.encode('unicode_escape')
        b'\\u7de8\\u78bcabc'
        >>> d.encode('utf-8').decode('latin-1')	# 解码时出现乱码，可能是编码用错
        'ç·¨ç¢¼abc'
        
        >>> 'ç\xa0\x81'.encode('latin-1').decode('utf8') # 将乱码重新编码再解码
        '码'
        ```




## 字符编码检测

```python
import chardet
with open(file, 'rb') as f:
    a = f.read()
chardet.detect(a)

# {'encoding': 'Big5', 'confidence': 0.99, 'language': 'Chinese'}
```





