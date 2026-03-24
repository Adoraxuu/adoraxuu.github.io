layout: draft
title: 'JavaScript: 變數提升 hoisting 到底是什麼？'
author: Adora Xu
tags:
- [JavaScript]
- [hoisting]
- [變數提升]

categories:
- [JavaScript]

date: 2023-11-18 21:58:00

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

<img src="/images/hoisting/banner.png">
之前在<a href="https://adora-xu.com/2023/11/15/var-let-const/">使用var, let, const宣告究竟差在哪呢？</a>，裡面有談到變數提升(Hoisting)。

變數提升(hoisting)是在 ECMAScript® 2015 Language Specification 裡面找不到的專有名詞，
它是一種釐清 JaveScript 在執行階段內文如何運行的思路（尤其是在創建和執行階段）。

究竟hoisting 是什麼呢？讓我們來一探究竟 ٩(๑❛ᴗ❛๑)۶

首先先來看看[MDN](https://developer.mozilla.org/zh-TW/docs/Glossary/Hoisting)解釋**hoisting**的重點

這段話裡面有幾個重點：

<div class="em-div2">

1. hosting 是一種釐清 JaveScript 在執行階段內文`如何運行的思路`（尤其是在創建和執行階段）。

2. `並非`單純地將變數和函式宣告，移動到程式的區塊頂端

3. 變數和函數的宣告會在`編譯階段就被放入記憶體`，但實際位置和程式碼中完全一樣。
</div>

## JavaScript 執行過程的兩大階段

要理解 Hoisting，首先先知道 JavaScript 執行兩大階段：

<img src="/images/hoisting/01.png">

<div class="em-div2">

- Creation Phase

  - 註冊名稱(Declaration)
  - 初始化（Initialisation）

- Execution Phase
  - 賦值(Assignment) & 執行

</div>

---

這樣就能夠很清楚解釋以下例子：

<img src="/images/hoisting/02.png">

```javascript
console.log(a);
var a = 1;
```

<div class="em-div">
undefined
</div>

解釋：

- 在 Creation Phase，會進行變數的註冊名稱和 undefined 的初始化。
- 在 Execution Phase，當程式碼逐行執行時，undefined 被印出。然後賦值 a = 1 。

## let & const 的變數提升

但這個情況在 let 宣告和 const 宣告時，卻變得不一樣：

```js
console.log(a);
let a = 1;
```

<div class="em-div">
ReferenceError: Cannot access 'a' before initialization
</div>
備註：const宣告的結果和let一樣

這時我們來看看 let & const 宣告時，JS 的兩階段圖釋： <img src="/images/hoisting/03.png">

解釋：

- 在使用 let & const 宣告的變數直到初始化前，都會將變數存在 TDZ 暫時死區中
- 如果在初始化＆賦值前取得此變數時，便會收到錯誤

透過以上圖片解釋，應該更好地理解之前提到的狀況 ٩(●˙▿˙●)۶，本篇文章都是參考以下資料整理+自己繪圖而成，如果有任何想法，歡迎留言給我！

<div class="em-div2">
參考資料：

[MDN - Hoisting](https://developer.mozilla.org/zh-TW/docs/Glossary/Hoisting)

[MDN - let](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/let)

[Hoisting in Javascript](https://www.scaler.com/topics/hoisting-in-javascript/)

[JavaScript Tutorial #20 | Variable & Function Hoisting in JavaScript ](https://www.youtube.com/watch?v=EvfRXyKa_GI)

</div>
