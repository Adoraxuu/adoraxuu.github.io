---
layout: post
title: 'Leetcode挑戰: Day05 longest common prefix'
date: 2024-02-03 20:58:13
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [longest common prefix]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此記錄每天刷leetcode的解法+說明，今天會用`Ruby`、`Python`、`JavaScript`來解題，今天來到第五天，讓我們繼續吧！

<img src="/images/leetcode-longest-common-prefix/1.png">

題目來源：[leetcode](https://leetcode.com/problems/longest-common-prefix/)

## Ruby
```ruby
def longest_common_prefix(strs)
  return "" if strs.empty?

  common_prefix = ""
  first_str = strs.first

  first_str.chars.each_with_index do |char, i|
    break unless strs.all? { |str| str[i] == char }
    common_prefix += char
  end

  common_prefix
end
```

先從陣列中取出第一個數為`first_str`，作為比較的基準，接著使用`.chars.each_with_index`對第一個詞每個字母做迭代，並返回索引值`i`。

`.all`是來檢查`array`中的所有元素是否都符合给定的條件，這裡的條件就是`str[i] == char`，如果有不同即停止。

`common_prefix`即記錄`array`中相同的字

## Python
```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ""

        common_prefix = ""
        first_str = strs[0]

        for i, char in enumerate(first_str):
            if any(i >= len(s) or s[i] != char for s in strs):
                break
            common_prefix += char

        return common_prefix
```
`enumerate`會回傳字串的`index`，以下是範例
```python
>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
```
`s[i]`則是代表`str`內的`i`位置的字母，基本上和ruby邏輯一樣，只是寫法不同而已！

## JavaScript
```js
function longestCommonPrefix(strs) {
    if (strs.length === 0) {
        return "";
    }

    let commonPrefix = "";
    const firstStr = strs[0];

    for (let i = 0; i < firstStr.length; i++) {
        const char = firstStr[i];
        if (!strs.every(str => str[i] === char)) {
            break;
        }
        commonPrefix += char;
    }

    return commonPrefix;
}
```

`JS`的寫法和`Ruby`差不多，可以使用`.every`來確保是否為相同字母，其他解法就和`Ruby`和`Python`類似，差別在使用`for (let i = 0; i < firstStr.length; i++)`迴圈，並使用`str[i]`來比對文字是否相符，最後也完成解答！

這幾天寫下來Ruby看起來還是最簡潔優雅٩(●˙▿˙●)۶，看起來好舒服