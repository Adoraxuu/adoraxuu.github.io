---
layout: post
title: 'Leetcode挑戰: Day15 excel sheet column title'
date: 2024-02-13 23:45:41
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [excel sheet column title]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第十五天，讓我們開始吧！

<img src="/images/leetcode-excel-sheet-column-title/0213.png">

題目來源：[leetcode](https://leetcode.com/problems/length-of-last-word/description/)

這次的題目蠻有趣的，是給予Excel的欄位將數換為數字，Excel欄位的慣例為：
```
A -> 1
B -> 2
C -> 3
...
Z -> 26
AA -> 27
AB -> 28 
...
```
A~Z是1~26，27是AA,28是AB，依序下去，等於是滿`26`後會進位。
這樣來看，其實Excel欄位和26進位蠻像的，轉換需要 / 26後的餘數對應到相應的字母即可（和十進位轉二進位有點像），因為剛好對應的是字母，可以將其轉換為`ASCII`碼，在`ASCII`裡，`A`為`065`、`B`為`066`....依序排序，排序到`Z`為`090`。

## Ruby
```ruby
def convert_to_title(column_number)
  result = '' #利用空字串來儲存結果
  while column_number > 0
    column_number -= 1 # 減去1，這樣1就對應到'A'
    result = ('A'.ord + column_number % 26).chr + result
    # 取得對應字母，加到結果字串的最前面
    column_number /= 26
    # 將 column_number 除以 26，以處理下一位數字
  end
  result
end
```
`column_number -= 1`
此方法是利用`餘數`先取`個位數`的字母，再`/26`取得接下來的數字字母，會需要先`-1`，這樣`1`才能對應到`A`

`result = ('A'.ord + column_number % 26).chr + result`
這段的`ord`是將字母轉換為`ASCII`，因為每個字母都是按照順序排序，所以中間的差數就是`'A'.ord`，最後再使用`.chr`轉為數字，最後 `+ result`更新`result`的數，並確保下一個`result`會放在前面

接著最後再將輸入值`/26`來處理下一位數（真的和十進位轉二進位蠻像的～）

## Python
```python
class Solution:
    def convertToTitle(self, columnNumber: int) -> str:
        result = ''
        while columnNumber:
            columnNumber -= 1
            result = chr(ord('A') + columnNumber % 26) + result
            columnNumber //= 26
        return result

```
Python和Ruby的解法相同，只要改變寫法即可！

## JavaScript
```js
function convertToTitle(columnNumber) {
    let result = '';
    while (columnNumber > 0) {
        columnNumber--;
        result = String.fromCharCode('A'.charCodeAt(0) + columnNumber % 26) + result;
        columnNumber = Math.floor(columnNumber / 26);
    }
    return result;
}
```

JS也和Ruby的解法相同，只要改變寫法即可！
今天就到這裡～明天繼續加油！

