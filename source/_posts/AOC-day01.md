---
title: 'Advent of code 2023 —Day 01'
layout: post
author: Adora Xu
date: 2023-12-17 09:30:16
tags:
- [AOC]
- [Advent of code]
- [Ruby]

categories:
- [Ruby]
---
<style>
.em-div{
background-color: #d4a373;
color: white;
padding: 10px;
margin: 10px;
border-radius: 5px;
}

.em-div2{
border:2px #d4a373 solid;
margin:5px;
padding:10px 2px 2px 2px;
border-style:dashed;
}
</style>

###  關於Advent of code

> [“Advent of code”](https://adventofcode.com/)是一年一度的以聖誕節為主題的計算機程式挑戰。自2015年以來一直在運行。程式難題涵蓋各種技能集和技能級別，可以使用任何編程語言來解決。參與者還可以在全球和私人排行榜上根據速度進行競爭。該活動由軟件工程師Eric Wastl創立並維護。

來源：[維基百科](https://en.wikipedia.org/wiki/Advent_of_Code)

### Day 1 - part.1

今年第一次來參與Advent of code，順便把我的解題過程也記錄下來٩(^ᴗ^)۶

這是第一天 Part 1的題目：

<img src="/images/AOC/01.png">

第一部分比較簡單，只要透過gsub就可以解決！

```ruby
def process_input(input_array)
  results = []
#設定一個空陣列

# 使用each do將陣列裡的數丟出處理並替換
  input_array.each do |input_string|
    digits = input_string.gsub(/\D/, '')
    result = "#{digits[0]}#{digits[-1]}"
#利用字串將首位數+尾數串起來，如果只有一個數字則重複
    results.push(result)
#將結果推進result裡
  end

  # 計算結果陣列中數字的總和 #利用.map(&:to_i)將陣列裡的字串轉為數字才可以相加
  sum_result = results.map(&:to_i).sum

  # 輸出結果
  puts sum_result
end
```

### Day1 Part.2

第二部分我整個大卡關(ᗒᗣᗕ)՞

<img src="/images/AOC/02.png">

有想出說做出一個hash，將"one" => "1"依序輸入進去，
接著使用each+gsub將將裡面的數字替換掉，
但遇到eightwothree，輸出的結果卻是eigh23….

最後上網找了[CJ Avilla](https://www.youtube.com/@cjav_dev)分享的解法，
來源： [Match and Scan - Day 01 - Advent of Code 2023](https://www.youtube.com/watch?v=lrJfC2dF84k&list=PLS6F722u-R6KYlGyUv65EFpGKl2Esmurr&index=15)

原來使用match方法就可以了ʕ •̀ o •́ ʔ

關於match，[Ruby API](https://rubyapi.org/3.2/o/s?q=match)是這樣解釋：
****[String#match](https://rubyapi.org/3.2/o/string#method-i-match)****
<div class="em-div2">

Returns a MatchData object (or `nil`) based on `self` and the given `pattern`.
Note: also updates [Special global variables at `Regexp`](https://rubyapi.org/3.2/o/regexp#class-Regexp-label-Special+global+variables).
 Computes `regexp` by converting `pattern` (if not already a Regexp).
 
</div>
match會回傳符合內容的物件，看起來像這個樣子：

```ruby
'foo'.match('f') # => #<MatchData "f">
```

另外可以使用常規表達法轉換，所以解法可以這樣寫：

```ruby
input = <<~INPUT
two1nine
eightwothree
abcone2threexyz
xtwone3four
4nineeightseven2
zoneight234
7pqrstsixteen
INPUT

replacement = {
  "one" => "1",
  "two" => "2",
  "three" => "3",
  "four" => "4",
  "five" => "5",
  "six" => "6",
  "seven" => "7",
  "eight" => "8",
  "nine" => "9",
  "1" => "1",
  "2" => "2",
  "3" => "3",
  "4" => "4",
  "5" => "5",
  "6" => "6",
  "7" => "7",
  "8" => "8",
  "9" => "9",
  "0" => "0"
}
result = input.each_line.map do |line|
  digits = line.match(/(#{replacement.keys.join('|')})/, 0)
#匹配hash的key，並將所有keys用|(or)隔開，一個符合即可，後面的0為從最左邊最一開始匹配
  first = replacement[digits[0]]

  digits = line.reverse.match(/(#{replacement.keys.map(&:reverse).join('|')})/, 0)
  last = replacement[digits[0].reverse]
#使用反轉搜索最後一個數

  (first+last).to_i
end.sum

p result
```

輸入結果最後拿到星星了(´∀`)（有點心虛）

<img src="/images/AOC/03.png">

不過在挑戰的過程中，感覺自己又進步了一些ヾ(*´∇`)ﾉ

最後真的很感謝[CJ Avilla](https://www.youtube.com/@cjav_dev)大神能夠提供解法！

如果您有興趣了解更多，請參考：

### 參考資料：

> [Match and Scan - Day 01 - Advent of Code 2023](https://www.youtube.com/watch?v=lrJfC2dF84k&list=PLS6F722u-R6KYlGyUv65EFpGKl2Esmurr&index=15)