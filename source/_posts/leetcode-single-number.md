---
layout: post
title: 'Leetcode挑戰: Day11 single number'
date: 2024-02-09 10:50:44
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [single number]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第十一天，讓我們開始吧！

<img src="/images/leetcode-single-number/1.png">

題目來源：[leetcode](https://leetcode.com/problems/single-number)

## Ruby
```ruby
def single_number(nums)
  nums.reduce(:^)
end
```
這次用到`XOR`符號`^`，這是種運算邏輯，用於對兩個二進制數的對應位進行比較，規則如下：兩個對應位數字不同，會回傳`1`，兩個對應位不同則會回傳`0`，用此來找出只出現一次的數字。

範例如下：

```ruby
0 xor 0 = 0
0 xor 1 = 1
1 xor 0 = 1
1 xor 1 = 0
```

如果我們傳入`[4, 1, 2, 1, 2]`呢？根據`reduce(:^)`會這樣運算`4 ^ 1 ^ 2 ^ 1 ^ 2`：

```ruby
  4:  100
  1:  001
XOR:  101  

  5:  101
  2:  010
XOR:  111 

  7:  111
  1:  001
XOR:  110 

  6:  110
  2:  010
XOR:  100
```
也可以用另外一個方法理解，`1 ^ 1 = 0`, `2 ^ 2 = 0`, `0 ^ 4 = 4`
只要有不同於其他的數，就會被留下來，這樣答案就出來了！

## Python
```python
class Solution:
    def singleNumber(self, nums):
        return reduce(lambda x, y: x ^ y, nums)
```
Python也是一樣邏輯，只不過`reduce`寫法不同，可以參考：[reduce](https://docs.python.org/3.0/library/functools.html)

## JavaScript
```js
var singleNumber = function(nums) {
    return nums.reduce((acc, num) => acc ^ num, 0);
  };
```
JS也是一樣邏輯，只不過`reduce`寫法不同，可以參考：[reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

