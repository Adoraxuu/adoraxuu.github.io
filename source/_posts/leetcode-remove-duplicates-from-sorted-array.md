---
layout: post
title: 'Leetcode挑戰: Day07 remove duplicates from sorted array'
date: 2024-02-05 23:52:44
author: Adora Xu

tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [remove duplicates from sorted array]

categories:
- [leetcode]
---


為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第七天，讓我們開始吧！

<img src="/images/leetcode-remove-duplicates-from-sorted-array/1.png">

題目來源：[leetcode](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

此題目雖然看似簡單，但需要注意一個小細節，除了最後回傳不重複的數有幾個外，原始傳入的`nums`也需要改變，這樣才會通過測驗！

## Ruby
```ruby
def remove_duplicates(nums)
  nums.uniq!
  nums.size
end
```
見證Ruby黑魔法的時刻٩(^ᴗ^)۶，這邊特別注意的是，`uniq!`為什麼要使用驚嘆號`!`呢？因為使用驚嘆號才能更改原始的`nums`，如果使用`uniq`則是回傳新的Array

詳情可見Ruby API [uniq!](https://rubyapi.org/3.3/o/s?q=uniq%21), [uniq](https://rubyapi.org/3.3/o/s?q=uniq)

## Python
```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        nums[:] = sorted(set(nums))
        return len(nums)
```
Python的話可以使用`set(nums)`，set 是一組無序且沒有重複的元素，所以我外面使用了`sorted`排序。

因為要改變`nums`的值，所以使用`nums[:]`

詳情可參考：[Python docs](https://docs.python.org/zh-tw/3/tutorial/datastructures.html)

## JavaScript
```js
function removeDuplicates(nums) {
    nums.splice(0, nums.length, ...new Set(nums));
    return nums.length;
}
```

JS定義`set`為：`集合（set）中的元素只会出现一次，即集合中的元素是唯一的。`，可以參考：[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)，但因為要改變`nums`的數，因此需要使用`splice`，移除或者替换已存在的元素和/或添加新元素就地改變一个Array的内容。

最後答案就出來了！

## 參考資料
- [Ruby API](https://rubyapi.org/3.3/o/s?q=uniq%21)
- [Python docs](https://docs.python.org/zh-tw/3/tutorial/datastructures.html)
- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)