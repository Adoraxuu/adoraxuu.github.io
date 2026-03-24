---
layout: post
title: 'Leetcode挑戰: Day08 majority element'
date: 2024-02-06 18:23:43
author: Adora Xu

tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [majority element]

categories:
- [leetcode]
---


為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第八天，讓我們開始吧！

<img src="/images/leetcode-majority-element/0206.png">

題目來源：[leetcode](https://leetcode.com/problems/majority-element/?envType=study-plan-v2&envId=top-interview-150)

在讀題目的時候，發現有一個小細節，題目寫要找出出現次數大於`[n/2]`的數字，且假定這個數字一定存在Array中。那也就代表，只要找出最常出現的數就可以了，因為陣列中只要其中一個數出現的數量大於`[n/2]`，剩下的數出現次數就不會大於`[n/2]`，可以想像成，西瓜被挖走一半多一點，剩下的西瓜再怎麼分都沒辦法大於一半。

所以這題只要找出，出現次數最多的數字即可！讓我們開始解題吧～


## Ruby

```ruby
def majority_element(nums)
  nums.tally.max_by {|key, value| value }[0]
end
```
這裡使用了[tally](https://rubyapi.org/3.3/o/s?q=tally)

在Ruby API的解釋為：
```
Enumerable#tally
Returns a hash containing the counts of equal elements:
Each key is an element of self.
Each value is the number elements equal to that key.
```
意思就是建立一個hash，key是`self`，value是出現的次數，簡單的範例：
```ruby
result = %w[a b c b c a c b].tally
puts result
# => {"a"=>2, "b"=>3, "c"=>3}
```

建立了hash之後，即可以使用[max_by](https://rubyapi.org/3.3/o/s?q=max_by)，`max_by`，`max_by`允許輸入值為{}，會返還{}內最大的值，因為返還的會是`Array`，來看一下範例：
```ruby
result = {foo: 1, bar: 10, baz: 8}.max_by {|key, value| value }
puts result
#[:bar, 10]
```

如果只要取出key呢？可以使用`[0]`來取出Array的第一個數，也是`Array`內最常出現的`key`:
```ruby
result = {foo: 1, bar: 10, baz: 8}.max_by {|key, value| value }[0]
puts result
#10
```



## Python
```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        return Counter(nums).most_common(1)[0][0]
```

在python計算數量可以使用[Counter()物件](https://docs.python.org/zh-tw/3/library/collections.html#collections.Counter)，來看看範例：
```python
from collections import Counter
words = ['red', 'blue', 'red', 'green', 'blue', 'blue']
cnt = Counter()
cnt.update(words)

print(cnt)
#Counter({'blue': 3, 'red': 2, 'green': 1})
```

接著可以使用`.most_common(1)[0][0]`，`(1)`代表取出第一個結果，這時輸出的值會是`[(3, 2)]`，因此還必須要使用`[0][0]`代表取出第一組Array的第一個數才會得到解答！

## JavaScript
```js
function majorityElement(nums) {
    let count = 0;
    let majority;

    for (const num of nums) {
        if (count === 0) {
            majority = num;
        }

        count += (num === majority) ? 1 : -1;
    }

    return majority;
}
```

JS我採用的是[摩爾投票法](https://zh.wikipedia.org/zh-tw/%E5%A4%9A%E6%95%B0%E6%8A%95%E7%A5%A8%E7%AE%97%E6%B3%95)，消除不同元素之間的票數，最終保留的就是可能的主要元素。

一開始先把`count`設定為0，如果接下來遇到的數字相同`count + 1`，不同`count - 1`，如果`count`又變回 0（代表刪掉一樣數量的a & b），這時就會更改`majority = num`，最後殘存活下來的就是`majority`了！


