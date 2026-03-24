---
layout: post
title: 'CodeWars: square every digit'
date: 2024-02-20 16:38:52
author: Adora Xu
tags:
- [codewars]
- [Ruby]
- [JavaScript]
- [Python]
- [square every digit]

categories:
- [codewars]
---
這幾天遇到程式的面試題目，想說也可以一併將解法分享٩(^ᴗ^)۶
題目：[Codewars - Square Every Digit](https://www.codewars.com/kata/546e2562b03326a88e000020)
一樣會用`Ruby`、`Python`和`JavaScript`三個語言來解題，讓我們開始吧！
<img src="/images/codewars-square-every-digit/0220_2.png">

這次的題目就是將輸入的內容拆開為陣列，各別平方後，再加入組合起來就能得到答案了٩(^ᴗ^)۶

## Ruby
```ruby
def square_digits(num)
  num.to_s.chars.map { |digit| digit.to_i ** 2}.join.to_i
end
```
聽到`各別`兩個字，腦中一定會自動聯想到`map`，這題也是使用`map`個字平方後再使用`join`組合成字串後，再使用`to_i`返還數字

## Python
```py
def square_digits(num):
    return int(''.join(str(int(digit)**2) for digit in str(num)))
```
`python`則是寫法稍微不同，可以使用`for digit in str(num)`即可

## JavaScript
```js
function squareDigits(num) {
    return parseInt(num.toString().split('').map(digit => parseInt(digit)**2).join(''), 10);
  }
```
