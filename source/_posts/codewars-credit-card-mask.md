---
layout: post
title: 'CodeWars: credit card mask'
date: 2024-02-16 16:05:11
author: Adora Xu
tags:
- [codewars]
- [Ruby]
- [JavaScript]
- [Python]
- [credit card mask]

categories:
- [codewars]
---
這幾天遇到程式的面試題目，想說也可以一併將解法分享٩(^ᴗ^)۶
題目：[Codewars](https://www.codewars.com/kata/5412509bd436bd33920011bc)
一樣會用`Ruby`、`Python`和`JavaScript`三個語言來解題，讓我們開始吧！
<img src="/images/codewar-credit-card-mask/1.png">

如果有像是銀行卡號、銀行帳號等等機密訊息需要加密，可以使用此方法，僅顯示最後四碼，如果總數小於四碼就顯示所有內容。

## Ruby
```ruby
def maskify(cc)
  cc.size > 4 ? "#" * (cc.size - 4) + cc[-4..-1] : cc
end
```
Ruby可以使用三元運算子來解，首先判斷數入的值長度`cc.size`是否大於4碼，因為最後面4碼要顯示，所以如果大於4碼，就將前面四碼的長度`cc.size`扣掉`4碼` * `#`，再使用`cc[-4..-1]`顯示倒數四位～倒數最後一位。

如果沒有大於4碼，就直接顯示`cc`即可 ٩(^ᴗ^)۶

## Python
```py
def maskify(cc):
    return "#" * (len(cc)-4) + cc[-4:]
```
Python比較特別，如果字串`*`小於等於零的數字，會返還空值。
所以這裡不需要使用判斷式就可以完成o(^▽^)o

## JavaScript
```js
function maskify(cc) {
    return cc.length > 4 ? "#".repeat(cc.length - 4) + cc.slice(-4) : cc
}
```
JS和Ruby的解法邏輯一樣，使用三元運算子判斷長度`cc.length`是否 > 4，如果大於的話則返回扣掉4個長度的` "#".repeat(cc.length - 4)`+最後四碼`cc.slice(-4)`，如果小於4直接返還`cc`

Codewars 7kyu的題目和leetcode easy相比稍微簡單一點，很適合練習不同程式的基本用法～很可惜前幾天的Leetcode挑戰斷在Day 15，接下來繼續努力o(^∀^*)o