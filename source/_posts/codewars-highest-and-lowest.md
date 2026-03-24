---
layout: post
title: 'CodeWars: highest and lowest'
date: 2024-02-21 13:15:26
tags:
- [codewars]
- [Ruby]
- [JavaScript]
- [Python]
- [highest and lowest]

categories:
- [codewars]
---
這幾天遇到程式的面試題目，想說也可以一併將解法分享٩(^ᴗ^)۶
題目：[Codewars - Highest and Lowest](https://www.codewars.com/kata/554b4ac871d6813a03000035)
一樣會用`Ruby`、`Python`和`JavaScript`三個語言來解題，讓我們開始吧！
<img src="/images/codewars-highest-and-lowest/0221.png">

## Ruby
```ruby
def high_and_low(numbers)
  numbers.split.map(&:to_i).minmax.reverse.join(' ')
end
```
Ruby有一個蠻好用的方法，[Array#minmax](https://rubyapi.org/3.3/o/s?q=minmax)，可以回傳陣列的最小值和最大值，範例如下：
```ruby
[0, 1, 2].minmax # => [0, 2]
```
但題目是`high_and_low`，所以需要`reverse`，並使用`.join(' ')`將陣列轉為字串。

## Python
```py
def high_and_low(numbers):
    numbers = list(map(int, numbers.split()))
    return f"{max(numbers)} {min(numbers)}"
```
`f"{max(numbers)} {min(numbers)}"`這裡使用[f-string](https://docs.python.org/zh-tw/3/tutorial/inputoutput.html)將最大值和最小值插入到字符串中

## JavaScript
```js
function highAndLow(numbers) {
    const nums = numbers.split(' ').map(Number);
    return `${Math.max(...nums)} ${Math.min(...nums)}`;
}
```
JS和Python也是用類似方法～