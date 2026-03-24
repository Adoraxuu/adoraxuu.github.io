---
layout: post
title: 'Leetcode挑戰: Day14 sqrtx'
date: 2024-02-12 22:36:08
author: Adora Xu
tags:
- [leetcode]
- [Ruby]
- [JavaScript]
- [Python]
- [sqrtx]

categories:
- [leetcode]
---
為了加強自己的程式能力，因此開始記錄每天刷leetcode的解法+說明，會用`Ruby`、`python`和`JavaScript`三個語言來解題，今天是第十四天，讓我們開始吧！

<img src="/images/sqrtx/1.png">

題目來源：[leetcode](https://leetcode.com/problems/sqrtx/)

今天的題目是尋找平方根，但是不能用內建的數學方法來找，題目解釋如下：

在给定一个非負整數 x 的情况下，返回四捨五入到最接近整数的 x 的平方根。返回的整數也應為非負整数。

不得使用任何内置指数函数或运算符。
例如，請勿在 c++ 中使用 pow(x, 0.5) 或在 python 中使用 x ** 0.5。

題目還算單純，因為僅要四捨五入到整數位即可，也可以使用昨天用到的`二分法`，從中間切一半開始測試，這樣很快就可以找到了！

## Ruby
```ruby
def my_sqrt(x)
  # 左右邊界為0...x
  left, right = 0, x

  while left <= right
    mid = (left + right) / 2  # 計算中間值
    mid_squared = mid * mid  # 計算中間值的平方

    if mid_squared == x
      return mid.to_i  # 如果中間值的平方等於 x，返回中間值（轉換為整數形式）
    elsif mid_squared < x
      left = mid + 1  # 如果中間值的平方小於 x，就使用mid+1...x來比較，因此調整left的值
    else
      right = mid - 1  # 如果中間值的平方大於 x，就使用left...mid-1來比較，因此調整right的值
    end
  end

  return right.to_i  # 當 left > right 時，right 就是小於或等於平方根的整數值，因此要向下取整，所以返回右邊界（轉換為整數形式）
end
```

## Python
```py
class Solution:
    def mySqrt(self, x: int) -> int:
        # 左右邊界為0...x
        left, right = 0, x

        while left <= right:
            mid = (left + right) // 2  # 計算中間值
            mid_squared = mid * mid  # 計算中間值的平方

            if mid_squared == x:
                return mid  # 如果中間值的平方等於 x，返回中間值
            elif mid_squared < x:
                left = mid + 1  # 如果中間值的平方小於 x，調整左邊界
            else:
                right = mid - 1  # 如果中間值的平方大於 x，調整右邊界

        return right  # 因為要向下取整，所以返回右邊界
```
## JavaScript
```js
function mySqrt(x) {
    let left = 0;
    let right = x;
  
    while (left <= right) {
      const mid = Math.floor((left + right) / 2);
      const midSquared = mid * mid;
  
      if (midSquared === x) {
        return mid;
      } else if (midSquared < x) {
        left = mid + 1;
      } else {
        right = mid - 1;
      }
    }
  
    return right;
  }
```

雖然如果使用內建的方法超快就可以解出來了，但學習使用二分法也能夠在其他的條件下應用！