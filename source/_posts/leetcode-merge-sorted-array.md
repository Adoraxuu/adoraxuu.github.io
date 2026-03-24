---
layout: post
title: 'Leetcode挑戰: Day01 merge sorted array'
date: 2024-01-30 21:59:31
author: Adora Xu

tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [merge sorted array]

categories:
- [leetcode]

---

為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第一天，讓我們開始吧！

<img src="/images/leet-code-day-1/1.png">

題目來源：[leetcode](https://leetcode.com/problems/merge-sorted-array/description/?envType=study-plan-v2&envId=top-interview-150)

## Ruby

```ruby
def merge(nums1, m, nums2, n)
  nums1.replace((nums1[0...m] + nums2).sort)
end

# 測試
nums1 = [1, 2, 3, 0, 0, 0]
m = 3
nums2 = [2, 5, 6]
n = 3

merge(nums1, m, nums2, n)
result_array = nums1.take(m + n)
puts result_array
```

使用`replace`取代，

`nums1[0..m]`為取位置0..m的陣列內容，再使用 `+nums2`，將兩個陣列內容組合起來，最後使用sort完成遞增排序。

Ruby蠻簡潔的！

## Python
```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        nums1[m:] = nums2
        nums1.sort()
```
python和Ruby一樣簡潔，不過有稍微不同：
`nums1[:m]`:是指從nums1陣列中切片取出`m`個元素
`nums1[m:] = nums2`:將nums2添加到切片後的nums1內
最後再使用`sort`排序`nums1[:m]`

## JavaScript

```ruby
var merge = function(nums1, m, nums2, n) {
  nums1.splice(0, m + n, ...nums1.slice(0, m).concat(nums2).sort((a, b) => a - b));
};

// 測試
var nums1 = [1, 2, 3, 0, 0, 0];
var m = 3;
var nums2 = [2, 5, 6];
var n = 3;

merge(nums1, m, nums2, n);
console.log(nums1);
```

`slice`

- `arr.slice([begin[, end]])`
- slice() 方法會回傳一個新陣列物件，為原陣列選擇之 begin 至 end（不含 end）部分。而原本的陣列將不會被修改。
- 使用在這裡是要刪掉num1重複的部分，插入num2

`splice`

- `array.splice(start[, deleteCount[, item1[, item2[, ...]]]])`
- splice() 方法可以藉由刪除既有元素並／或加入新元素來改變一個陣列的內容。
- 在這裡是要加入num1+num2組合的內容
- 範例：

```jsx
const months = ['Jan', 'March', 'April', 'June'];
months.splice(1, 0, 'Feb');
// Inserts at index 1
console.log(months);
// Expected output: Array ["Jan", "Feb", "March", "April", "June"]
```

- `nums1.splice(0, m + n, ...)`將合併且排序的數組插入到nums1的開始位置，取代原有的元素，其中0是開始位置，m + n是要刪除的元素數量，...是展開運算符，用於將排序後的數組展開成單獨的元素。
- 這個函數的目的是將兩個已排序的數組 nums1 和 nums2 合併，並將結果存儲在 nums1 中，同時保持排序狀態。

---

參考：
<a href="https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/slice">MDN</a>
