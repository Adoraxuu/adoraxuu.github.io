---
layout: post
title: 'Leetcode挑戰: Day04 roman to integer'
date: 2024-02-02 23:36:56
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [roman to integer]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此記錄每天刷leetcode的解法+說明，今天會用`Ruby`、`Python`、`JavaScript`來解題，今天來到第四天，讓我們繼續吧！

<img src="/images/leetcode-roman-to-integer/1.png">

題目來源：[leetcode](https://leetcode.com/problems/roman-to-integer)

羅馬數字的解釋:[羅馬數字](https://zh.wikipedia.org/zh-tw/%E7%BD%97%E9%A9%AC%E6%95%B0%E5%AD%97)

羅馬數字共有7個，即Ⅰ（1）、Ⅴ（5）、Ⅹ（10）、Ⅼ（50）、Ⅽ（100）、Ⅾ（500）和Ⅿ（1000）。另外因為羅馬數字有`左加右減`的特性，代表說大的數字在左邊就往右加，大的數字在右邊就往左減。

像是`MCMXCIV`的羅馬數字為何？可以依序先把他們變成阿拉伯數字，再從個位數開始往左邊遵循左加右減的規則，就可以得到答案了：
```
M(1000),C(100),M(1000),X(10),C(100),I(1),V(5)
1000, 100, 1000, 10, 100, 4(-1 + 5)
1000, 100, 1000, 10, 104(100 + 4)
1000, 100, 1000, 94(-10 + 104)
1000,100,1094 (1000 + 94)
1000, 994(-100 + 1094) 
1994 (1000+994)
```
其實規則蠻單純的，遵循左加右減即可，這樣就可以把它改寫成程式了：

## Ruby
```ruby
def roman_to_int(s)
  roman_num = {'I'=>1, 'V'=>5, 'X'=>10, 'L'=>50, 'C'=>100, 'D'=>500, 'M'=>1000}
  result = 0
  prev_value = 0

  s.chars.reverse_each do |char|
    value = roman_num[char]
    result += (value < prev_value) ? -value : value
    prev_value = value
  end

  result
end
```

先把羅馬數字的七個數做成`hash`，接著設定`result` `pre_value`為`0`，再使用`reverse_each`，把值從個位數丟進去，轉換為阿拉伯數字後，比較`prev_value`，因為`prev_value`恆在右邊，所以如果`value`比較小，`value`則為`-`

這邊要特別注意，因為每次比較的值是要比較原始的`value`，所以會需要`prev_value`來儲存上一次的值，而不是和`result`比較。

## Pyton

```python
class Solution:
    def romanToInt(self, s: str) -> int:
        roman_num = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000}
        result = 0
        prev_value = 0

        for char in reversed(s):
            value = roman_num[char]
            result += -value if value < prev_value else value
            prev_value = value

        return result
```

因為基本的邏輯架構和`Ruby`一樣，這邊只有改變寫法而已

## JavaScript
```javascript
function romanToInt(s) {
    const romanNum = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000};
    let result = 0;
    let prevValue = 0;

    for (let i = s.length - 1; i >= 0; i--) {
        const char = s[i];
        const value = romanNum[char];
        result += (value < prevValue) ? -value : value;
        prevValue = value;
    }

    return result;
}
```

因為JS沒有`reverse`，所以我使用最原始的方法，利用`索引值`，`i`起始值設為`s.length - 1`，就可以反向丟入羅馬字，再利用`hash`轉換為數字即可，接下來的邏輯都和`Ruby`一樣，只不過變數慣例寫不一樣而已！

今天的題目比較難的在前面轉換的邏輯，理清之後轉換成程式碼就簡單多了！

