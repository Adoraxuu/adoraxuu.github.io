---
layout: post
title: 'Leetcode挑戰: Day03 palindrome number'
date: 2024-02-01 21:47:24
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [palindrome number]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此記錄每天刷leetcode的解法+說明，今天會用`Ruby`、`Python`、`JavaScript`來解題，今天來到第三天，讓我們繼續吧！

<img src="/images/leetcode-palindrome-number/01.png">

題目來源：[leetcode](https://leetcode.com/problems/palindrome-number/)

## Ruby
```ruby
def is_palindrome(x)
  x.to_s == x.to_s.reverse
end
```

我第一個想到使用`reverse`，不過`reverse`必須要字串才行，
因此山不轉路轉，把一開始給的x值使用`.to_s`轉為字串即可！

## Python

```python
class Solution:

  def isPalindrome(self, x: int) -> bool:
    return str(x) == str(x)[::-1]
```
python的reverse寫法很特別！
不過一樣是要先轉成字串所以使用`str(x)`

其中`[::-1]`是反轉讀取的意思，如果是array的話也會反轉讀取
倘若是`[3::-1]`則會從索引值`3`開始反轉讀取

## JavaScript
```javascript
var isPalindrome = function(x) {
    return x == [...x.toString()].reverse().join('');
};
```
我使用`...`的[展開符號](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax)將字串轉為陣列，再使用`reverse`的功能，將陣列反轉，最後使用`join('')`組起來，如果反轉完等於`x`也回傳`true`!

今天的題目稍微簡單一點，我們明天繼續٩(๑❛ᴗ❛๑)۶
