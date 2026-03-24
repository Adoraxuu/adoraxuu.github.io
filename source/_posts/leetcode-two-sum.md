---
layout: post
title: 'Leetcode挑戰: Day02 two sum'
date: 2024-01-31 17:28:12
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [two sum]

categories:
- [leetcode]

---

為了加強自己的程式能力，因此記錄每天刷leetcode的解法+說明，會用`Ruby`來解題，今天來到第二天，讓我們繼續吧！

<img src="/images/leetcode-two-sum/1.png">

來源:<a href="https://leetcode.com/problems/two-sum/">Leetcode</a>

## Ruby
```ruby
def two_sum(nums, target)
  seen = {}
  nums.each_with_index { |num, i| return [seen[target - num], i] if seen.key?(target - num); seen[num] = i }
end

# Testing
puts two_sum([2, 7, 11, 15], 9)  # Output: [0, 1]
puts two_sum([3, 2, 4], 6)       # Output: [1, 2]
puts two_sum([3, 3], 6)          # Output: [0, 1]
```
- `seen`: 空的hash，來記錄已經遍歷過的數字及其對應的索引
- `nums`: 傳入的陣列
- `target`:目標之和
- `each_with_index`:迭代+索引，會得到`num`& 索引值`i`
- `if seen.key?(target - num)`: target - num為第二個數，因為target = a + b，target - a = b，第一個數字為num時，第二個則為target - num
- `seen[target - num]`: 目標數字的索引值
- `seen[num] = i`: 如果沒有匹配的數對，就將當前數字`num`&索引`i`加入`seen`當中，之後找比較快

今天的難度比昨天難好多！！不愧是面試常見的題目，但使用Ruby還是能夠很簡潔地寫出來！
