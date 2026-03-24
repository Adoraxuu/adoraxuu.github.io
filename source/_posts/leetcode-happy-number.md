---
layout: post
title: 'Leetcode: happy number'
date: 2024-02-26 11:26:48
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [happy number]

categories:
- [leetcode]
---
<style>
  .em-div{
  background-color: #d4a373;
  color: white;
  padding: 10px;
  margin: 10px;
  border-radius: 5px;
  }
</style>
為了加強自己的程式能力，因此開始記錄刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題

<img src="/images/leetcode-happy-number/happy-number.png">

題目來源：[leetcode](https://leetcode.com/problems/happy-number/description/)

其實這題和之前解過的[square every digit](https://adora-xu.com/2024/02/20/codewars-square-every-digit/)蠻像的，都要將個別的數字平方，不過這裡多了判斷加總是否等於1的步驟。

其實到這步就可以寫出程式碼，利用不斷迭代加上判斷式，判斷是否為快樂數。
以Ruby示範可以這樣寫：
```ruby
def is_happy(n)
# 解法一
  while n != 1
    n = n.to_s.chars.map { |digit| digit.to_i ** 2 }.sum
  end
  n == 1
end
```
但如果想要程式碼效率更高一點呢？

就需要更了解快樂數一點，關於快樂數的介紹，可以參考維基百科：[快樂數](https://zh.wikipedia.org/zh-tw/%E5%BF%AB%E6%A8%82%E6%95%B8)

其中有提到：
<div class="em-div">
不是快樂數的數稱為不快樂數（英語：unhappy number），所有不快樂數的數位平方和計算，最後都會進入 4 → 16 → 37 → 58 → 89 → 145 → 42 → 20 → 4 的循環中。
</div>

因此，如果數字有`4`可以先排除，因為會進到無限循環中，這時程式碼可以寫成這樣：
```ruby
#解法二
def is_happy(n)
  while n != 1 && n != 4
    n = n.to_s.chars.map { |digit| digit.to_i ** 2 }.sum
  end
  n == 1
end
```

也可以把循環的數字，製作成一個`set`，避掉所有不快樂數無限循環的可能：
```ruby
#解法三
def is_happy(n)
  cycle_set = Set.new([4, 16, 37, 58, 89, 145, 42, 20])

  while n != 1 && !cycle_set.include?(n)
    n = n.to_s.chars.map { |digit| digit.to_i ** 2 }.sum
  end

  n == 1
end
```

因為覺得第二種解法比較直觀，效率也比較好，因此`python` & `js`我都使用第二種寫法：

# Python
```py
def is_happy(n):
    while n != 1 and n != 4:
        n = sum(int(digit) ** 2 for digit in str(n))
    return n == 1
```

# JavaScript
```js
function isHappy(n) {
    while (n !== 1 && n !== 4) {
        n = String(n).split('').map(Number).reduce((sum, digit) => sum + digit ** 2, 0);
    }
    return n === 1;
}
```