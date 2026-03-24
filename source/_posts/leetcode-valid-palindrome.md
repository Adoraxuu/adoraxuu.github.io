---
layout: post
title: 'Leetcode挑戰: Day10 valid palindrome'
date: 2024-02-08 23:03:06
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [valid palindrome]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第十天，終於完成1/3了，讓我們開始吧！

<img src="/images/leetcode-valid-palindrome/banner.png">

題目來源：[leetcode](https://leetcode.com/problems/valid-palindrome)

這個問題要求判斷句子是否為回文。在進行比較時，會需要先將所有字母轉換為小寫，並去除所有非字母和數字。然後，比較處理過的字串是否和它的反轉相等。


## Ruby
```ruby
def is_palindrome(s)
  clean_s = s.downcase.gsub(/[^a-z0-9]/, '')
  clean_s == clean_s.reverse
end
```
使用`downcase`將字符轉換為小寫，再使用`gsub`去除非字母和數字的字，最後比較反轉是否相同就完成了～

## Python
```python
def is_palindrome(s):
    # 將字符轉換為小寫，去除非字母和數字的字
    clean_s = ''.join(char.lower() for char in s if char.isalnum())
    # 比較處理過的字串和它的反轉是否相等
    return clean_s == clean_s[::-1]
```

`isalnum()`方法用於檢查字符串中的所有字是否都是字母和數字。如果是，該方法返回 True，表示所有字符均為字母（a-z、A-Z）和數字（0-9）。反之，如果字符串中包含非字母和非數字的字符，例如空格、驚嘆號、井號、百分號、和問號等，該方法將返回 False。

使用這個方法就不用使用`gsub`取代了，因為只有是字母和數字才會放進`clean_s`裡

## JavaScript
```js
function isPalindrome(s) {
  // 將字串轉換為小寫，去除非字母和數字
  const cleanS = s.toLowerCase().replace(/[^a-z0-9]/g, '');
  // 比較處理過的字串和它的反轉是否相等
  return cleanS === cleanS.split('').reverse().join('');
}
```
 

