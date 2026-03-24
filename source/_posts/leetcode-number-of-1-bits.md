---
layout: post
title: 'Leetcode: number of 1 bits'
date: 2024-03-16 20:55:48
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [number of 1 bits]
- [漢明權重]
- [hamming weight]

categories:
- [leetcode]
---
為了加強自己的程式能力，會記錄刷leetcode的解法+說明，這次也會用`Ruby`、`python`和`JavaScript`三個語言來解題，讓我們開始吧！

<img src="/images/leetcode-number-of-1-bits/01.png">

題目來源：[leetcode](https://leetcode.com/problems/number-of-1-bits/description/)

## 漢明權重
此題提到了個特別的詞：[漢明權重](https://zh.wikipedia.org/zh-tw/%E6%B1%89%E6%98%8E%E6%9D%83%E9%87%8D)，維基百科中解釋，漢明權重是一串符號中非零符號的個數。因此它等同於同樣長度的全零符號串的漢明距離。在最為常見的數據位符號串中，它是1的個數。

漢明權重(Hamming weight)是以理察·衛斯里·漢明(Richard Wesley Hamming)的名字命名的，它在包括資訊理論、編碼理論、密碼學等多個領域都有應用。

而這題的解法，就是將輸入的數字轉為二進位制的字串，接著計算`1`的數量即可。

## Ruby
```ruby
def hamming_weight(n)
  n.to_s(2).count('1')
end

puts hamming_weight(0b00000000000000000000000000001011) # 3
puts hamming_weight(0b00000000000000000000000010000000) # 1
puts hamming_weight(0b11111111111111111111111111111101) # 31
```
在程式中經常使用`0b`開頭表示此為二進位制，而在Ruby中，前面有零的數字會被當作八進制數字解析，所以此題的傳入值前面可以加上`0b`，確保最後的結果會是正確的。

## Python
```py
class Solution:
    def hammingWeight(self, n: int) -> int:
        return bin(n).count('1')

solution = Solution()

print(solution.hammingWeight(0b00000000000000000000000000001011)) #3
print(solution.hammingWeight(0b00000000000000000000000010000000)) #1
print(solution.hammingWeight(0b11111111111111111111111111111101)) #31
```

Python的`bin(x)`將整數轉為帶前綴`0b`的二進制字串，因為`0b`並不會影響我們最後要計算`1`的數量的結果，因此不需要特別轉換。

如果要控制是否顯示前缀`0b`，可以使用：
```py
format(14, '#b'), format(14, 'b')
('0b1110', '1110')
f'{14:#b}', f'{14:b}'
('0b1110', '1110')
```
來源：[Python官方文件](https://docs.python.org/zh-cn/3/library/functions.html#bin)

## JavaScript
```js
var hammingWeight = function(n) {
    return n.toString(2).split('1').length - 1;
};

console.log(hammingWeight(0b00000000000000000000000000001011)) //3
console.log(hammingWeight(0b00000000000000000000000010000000)) //1
console.log(hammingWeight(0b11111111111111111111111111111101)) //31
```

JS的使用`.split('1')`，將字串依據1分割為陣列，最後計算陣列的長度再`-1`就可以得到答案了！

為什麼這裡要`-1`呢？可以想像一個蛋糕，如果切了一刀會變成兩塊，其中的那一刀，在這裡就是`1`的意思，我們最後要得到的是`切了幾刀`，而不是`蛋糕有幾塊`，所以為了校正最後會需要`-1`。

以上就是今天的Leetcode刷題解釋٩(●˙▿˙●)۶