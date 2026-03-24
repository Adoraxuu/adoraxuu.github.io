---
layout: post
title: 'Leetcode挑戰: Day12 plus one'
date: 2024-02-10 20:05:07
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [plus one]

categories:
- [leetcode]
---

為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第十二天，讓我們開始吧！

<img src="/images/leetcode-plus-one/0210.png">

題目來源：[leetcode](https://leetcode.com/problems/plus-one/)

這題題目很簡單，只要會使用將數字陣列轉為整數再加一，接著再轉回數字陣列就可以得到答案～

## Ruby
```ruby
def plus_one(digits)
  (digits.join('').to_i + 1).to_s.chars.map(&:to_i)
end
```

`.map(&:to_i)`原始的寫法為`.map{ |num| num.to_i }`

## Python
```py
class Solution:
    def plusOne(self, digits):
        return [int(digit) for digit in str(int(''.join(map(str, digits))) + 1)]
```
Python轉換型態都是包一個括號在外面，`int(''.join(map(str, digits)))`為將陣列內每一字連接為字串最後轉為整數，而後`+1`，再利用`str()`轉為字串。

最後將每一字轉為整數陣列回傳。

## JavaScript
```js
var plusOne = function(digits) {
    return (BigInt(digits.join('')) + 1n).toString().split('').map(Number);
  };
```

在`js`比較特別，使用[BigInt](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/BigInt)，BigInt 是一個內建的物件，提供了表示大於 2^53 的整數的功能 (2^53 是 JavaScript 原生的Number能夠表示的最大值)，因為使用`BigInt`型態，所以`+1`要使用`+1n`，才能得到加一後的結果。