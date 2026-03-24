---
layout: post
title: 'Python: PEP 8 程式碼風格指南 '
date: 2024-03-01 15:20:11
author: Adora Xu
tags:
- [python]
- [pep8]
- [style guide]

categories:
- [Python]
---

<img src="/images/python-pep8-style-guide-for-python-code/python-logo.png">

> 圖片來源: [Python.org](https://www.python.org/community/logos/)

### 
自己在練習寫Python時，常覺得怪怪的，後來才發現，是因為我沒有了解Python的書寫風格。
就像練習寫作文要了解句讀的規則，寫程式一開始，也需要清楚該程式的書寫風格，才能夠在打好基礎架構下精進。

雖然目前有許多方便的小工具，能夠自動幫忙整理排版、縮排等等，但覺得自己應該要先知道為什麼，再利用工具，才能夠更順利～

Python有定義了PEP(Python Enhancement Proposal)，裡面有許多章節，而其中的[PEP 8 – Style Guide for Python Code](https://peps.python.org/pep-0008/)，則可以解決我的煩惱，因為PEP 8的內容有很多，以下文章會分享自己覺得常用的規範。

## [縮排 Indentation](https://peps.python.org/pep-0008/#indentation)

- 每行縮排4個空格
- 續行應該在括號內的垂直對齊：

✅正確示範

```python
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
```

❎錯誤示範

第一行若要保留參數的話，括號應該要垂直對齊

```python
foo = long_function_name(var_one, 
		var_two,var_three, var_four)
```

- 續行亦可使用懸掛縮排。在使用懸掛縮排時，應考慮以下幾點：第一行不應有參數（要按Enter換到下一行），應使用進一步的縮排來明確區分自己是一個續行：

✅正確示範

```python
#懸掛縮排
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

依據以上的規則，提供多一點示範：

✅正確示範

```python
def context(user, password, 
            timeout, command):
    pass

user_list = [
    'user1',
    'user2',
    'user3',
    'user4'
]
```

❎錯誤示範

```python
def context(user, password, 
    timeout, command):
    pass

user_list = [
    'user1',
    'user2',
    'user3',
    'user4']
```

### 括號 brace/bracket/parenthesis

括號單獨一行，縮排 or 不縮排都可以

✅正確示範

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
```

✅正確示範

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
)
```

- 每行程式碼最長不要超過79個字：為什麼是79個字呢？因為當初定義PEP8時年代久遠，螢幕比較小，79剛好符合某種螢幕的寬度🤣，不過因為現在螢幕的寬度都做得比較大，因此此規範可以靈活應對。

## [空行 Blank Lines](https://peps.python.org/pep-0008/#blank-lines)

- 寫多個函數，函數間要空兩個空行

✅正確示範

```python
def get_name(value):
    pass

def get_age(value):
    pass
```

- 在同一個class內的兩個method，method間要空一個空行

✅正確示範

```python
class Demo(object):

    def __init__(self):
        pass
    
    def do_something(self):
        pass
```

## [Imports](https://peps.python.org/pep-0008/#imports)

- 導入通常應該在單獨的行上：

✅正確示範

```python
import os
import sys
```

✅正確示範

```python
from subprocess import Popen, PIPE
```

❎錯誤示範

```python
import sys, os
```

### Import Order

導入順序應該依照以下分組：

1. Standard library imports.(Python內建Library)
2. Related third party imports.(第三方)
3. Local application/library specific imports.(本地Module相互導入)

應該在每組導入中間加一行空行

✅正確示範

```python
import os
import sys

from flask import abort
from werkzeug.security import generate_password_hash, check_password_hash

from my_project.extensions import database
from my_project.utils import custom_function, custom_url
```

## [空格 White Space](https://peps.python.org/pep-0008/#whitespace-in-expressions-and-statements)

### 避免多餘空格

- 括號與內容間不需要空格

```python
# Correct:
spam(ham[1], {eggs: 2})
# Wrong:
spam( ham[ 1 ], { eggs: 2 } )
```

- 空格接在逗號、分號、冒號之後

```python
# Correct:
if x == 4: print(x, y); x, y = y, x
# Wrong:
if x == 4 : print(x , y) ; x , y = y , x
```

- 但是，在切片中，冒號的作用類似於二元運算符，兩個冒號必須相同間距。例外：當省略切片參數時，空格也被省略：

```python
# Correct:
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
ham[lower:upper], ham[lower:upper:], ham[lower::step]
ham[lower+offset : upper+offset]
ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
ham[lower + offset : upper + offset]
```

- 左括號前方不需要有空格：

```python
# Correct:
dct['key'] = lst[index]
# Wrong:
dct ['key'] = lst [index]
```

- 變數等號左右各一個空格、但不需要強制對齊：

```python
# Correct:
x = 1
y = 2
long_variable = 3

# Wrong:
x             = 1
y             = 2
long_variable = 3
```

## [注釋 Comments](https://peps.python.org/pep-0008/#comments)

- **[Block Comments](https://peps.python.org/pep-0008/#block-comments)**
- **[Inline Comments](https://peps.python.org/pep-0008/#inline-comments)**
    - `#`後隔一個空格，注釋與程式碼間至少要隔兩個空格：

```python
x = x + 1                 # Compensate for border
```

- **[Documentation Strings](https://peps.python.org/pep-0008/#documentation-strings)**
    - 應該出現在def之後
    - 結束的`“”“`應該要單獨占一行

```python
def add_numbers(a, b):
    """
    這是一個加法函數，用於將兩個數字相加。

    參數：
    a (int): 第一個數字
    b (int): 第二個數字

    返回：
    int: 兩個數字的和
    """
    return a + b
```

## [命名規則 **Naming Conventions**](https://peps.python.org/pep-0008/#naming-conventions)

以下為常見的命名方式：

- `b`（單個小寫字母）
- `B`（單一大寫字母）
- `lowercase`
- `lower_case_with_underscores`
- `UPPERCASE`
- `UPPER_CASE_WITH_UNDERSCORES`
- `CapitalizedWords` （也是駝峰式命名法 CapWords, or CamelCase）
- `mixedCase`（與 CatalizedWords 的區別在於首字母小寫）

### [Class Name](https://peps.python.org/pep-0008/#class-names)

使用駝峰命名法，範例：

```python
class MyCar:
    def __init__(self, make, model, year):
        self.make, self.model, self.year, self.odometer_reading = make, model, year, 0

    def full_name(self):
        return f"{self.year} {self.make} {self.model}"

my_car = MyCar("Toyota", "Camry", 2022)
print(my_car.full_name())
```

### **[Package and Module Names](https://peps.python.org/pep-0008/#package-and-module-names)**

- 模組(Module)應該有短的、全小寫的名稱。如果可以提高可讀性，可以在模組名稱中使用底線 _
- 套件(Package)也應該有短的、全小寫的名稱，儘管不鼓勵使用底線

### **[Function and Variable Names](https://peps.python.org/pep-0008/#function-and-variable-names)**

- 函數(function)名稱應小寫，必要時用底線分隔單字以提高可讀性
- 變數(variable)與函數遵循相同規定

### **[Constants](https://peps.python.org/pep-0008/#constants)**

- 常數通常在模組層級定義，並全部用大寫字母書寫，並用下劃線分隔單字。例如：MAX_OVERFLOW 和 TOTAL。

## 命名整理

|  | 命名規則 |
| --- | --- |
| Class Name | CapitalizedWords |
| Package and Module Names | lower_case_with_underscores |
| Function and Variable Names | lower_case_with_underscores |
| Constants | UPPER_CASE_WITH_UNDERSCORES |

透過閱讀PEP 8，也為編寫Python之路打下了良好的基礎，因為PEP8的內容很多，這裡只有舉比較常見的範例，細節還是可以去官方文件查看，分享給各位～

本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [PEP 8 – Style Guide for Python Code](https://peps.python.org/pep-0008/)

> [Udemy - Python 3 编程技巧汇总](https://www.udemy.com/course/python3-tips/)
