---
layout: post
title: 'Leetcode挑戰: Day13 search insert position'
date: 2024-02-11 22:09:20
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [search insert position]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第十三天，讓我們開始吧！

<img src="/images/search-insert-position/1.png">

題目來源：[leetcode](https://leetcode.com/problems/search-insert-position/description/)

一開始看到題目原本想說，把數字插入陣列中排序後再返還索引值就好，但實際執行卻發現效率很差ʘ‿ʘ。

後來想到可以使用二分法，先取中間的數字與target比較，分成兩邊，這樣最多就只要比較陣列一半的數量就好，會比較有效率，就像是猜數字，如果一開始就對半猜，猜到數字的機率也會快很多。

## Ruby
```ruby
def search_insert(nums, target)
  left, right = 0, nums.length - 1

  while left <= right
    mid = (left + right) / 2
    if nums[mid] == target
      return mid
    elsif nums[mid] < target
      left = mid + 1
    else
      right = mid - 1
    end
  end

  return left
end
```
這裡的`left`是指陣列的起始索引值，為`0`，`right`是陣列的結束值，為`nums.length - 1`，`mid`就是中間的索引值`(left+right)/2`

如果照排序看可以這樣理解：
`left`,`mid`,`right`

一開始先猜中間，如果運氣好就是中間值，可直接返還`mid`。

如果`target`比較大，即縮小`left`的範圍為`mid + 1`，比較數字的範圍也到了右半邊，接著會繼續新的迴圈，再繼續對半切取新的`mid`值比較。

如果`target`比較小，則縮小`right`的範圍為`mid - 1`，比較數字在左半邊，繼續新的迴圈，再繼續對半切取新的`mid`值比較。

直到`nums[mid] = target`，即會返還`mid`，也就是索引值。

## Python
```py
class Solution:
    def searchInsert(self, nums, target):
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        return left
```
`python`也是一樣邏輯，只是部分寫法不同

## JavaScript
```js
var searchInsert = function(nums, target) {
    let left = 0, right = nums.length - 1;
  
    while (left <= right) {
      let mid = Math.floor((left + right) / 2);
      if (nums[mid] == target) {
        return mid;
      } else if (nums[mid] < target) {
        left = mid + 1;
      } else {
        right = mid - 1;
      }
    }
  
    return left;
  };
```
`js`也是相同，不過需要添加`Math.floor`讓`mid`值為無條件捨去

這就是今天的解題了～其實不管什麼題目都可以用最笨的方法解，但是如果在讀題目時多想一點，就能讓程式碼效率更高！接下來幾天繼續加油🙌

