layout: post 
title: 'JavaScript: 使用 var, let, const 宣告究竟差在哪呢？' 
tags:
- [JavaScript]
- [var]
- [let]
- [const]
categories:
- [JavaScript]
date: 2023-11-15 15:29:55
description: '比較 JavaScript 的 var、let、const 宣告方式，深入解析 scope、hoisting、重新賦值等關鍵差異，選對變數宣告讓程式碼更安全清晰。'

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

###

<img src="/images/var-let-const/banner.png">

在 JavaScript 中，經常使用`var`, `let`, `const`這三個數來宣告，究竟這三個有什麼不同呢？下面來詳細說明(^\_^)

<div class="em-div2">
註：此篇文章有多個程式碼輔助解釋，篇幅較長，您也可以拉到最底部看比較表格。
</div>

## 由來

> `var`, `let`, 以及 `const` 都是在 JavaScript 用來做變數宣告的保留字，在 JavaScript 早期只有 `var`，直到 ES2015 (ES6) 時才加入了 `let` 與 `const` 。

## 關於 var

### 重點

<div class="em-div2">

- `var`為**函數作用域(function scope)**，若在函數外宣告則為**全作用域(global scope)**
- 僅有 function scope 能夠關住 var（容易污染）
- var 宣告的變數，可以重新被宣告 & 賦值
- 有<a href="https://adora-xu.com/2023/11/18/hoisting/">變數提升</a>，若先提取變數，再使用 var 宣告，會出現 undefined
</div>

接著會用四個範例題目，來幫助解釋這四個重點 ٩(ˊᗜˋ )و

### var 為全作用域

`var`可以為**全作用域（global scope）**或**函數作用域（function scope）**。這代表在函式**外面**定義的變數可以**全域訪問**，在特定函數內部定義的變數可以在函數內部存取。(補充：未宣告的變數也會放在全域變數)

- 範例 1：全域宣告變數`a`，我們可以在程式的**任何地方**抓到`a`

```javascript
var a = 100;
function fn() {
  console.log(a);
}
fn();
console.log(a);
```

<div class="em-div">
output:
10
10
</div>

- 範例 2：僅有 function scope 能夠關住 var，變數“a”在函數內使用 var 宣告。如果使用者在函數之外存取，則會顯示`ReferenceError: a is not defined`。

```javascript
function fn() {
  var a = 10;
  console.log(a);
}
fn();
console.log(a); //var無法在function外被存取;
```

<div class="em-div">
output：
10 <br>
ReferenceError: a is not defined
</div>

- 範例 3：var 宣告的變數，可以重新被宣告&賦值

```javascript
var a = 10;
var a = 8;
a = 7;
console.log(a);
```

<div class="em-div">
output: 7
</div>

- 範例 4：如果使用者在宣告之前使用 var 變數，因為<a href="https://adora-xu.com/2023/11/18/hoisting/">變數提升（Hosting）</a>的關係。在執行函數之前，會優先將 var 變數放入記憶體。要注意的是：這只是在記憶體有這變數的空間，但**尚未初始化**。這也是為何會拿到 undefined 的原因。

```javascript
console.log(a);
var a = 10;
```

<div class="em-div">
output: undefined
</div>

## 關於 let

### 重點

<div class="em-div2">

- `let`為<b>區塊作用域(block scoped)</b>，無法在`{block}`之外存取它
- 有<a href="https://adora-xu.com/2023/11/18/hoisting/">變數提升（Hosting）</a>，但因為 let 不會幫忙初始化，而是存在**暫時執行死區（TDZ）**，因此`let`不能在宣告前使用，不能多次宣告。
</div>
- 範例 1：正常運行的樣子

```javascript
let a = 10;
function f() {
  let b = 9;
  console.log(b);
  console.log(a);
}
f();
```

<div class="em-div">
output:9</br>
10
</div>

- 範例 2: `let`宣告無法在`{block}`之外存取變數`b`，因此會出現`ReferenceError: b is not defined`

```javascript
function fn() {
  let b = 9;
  console.log(b);
}
fn();
console.log(b);
```

<div class="em-div">
output:9</br>
ReferenceError: b is not defined
</div>

- 範例 3: `let`宣告的變數不能重複宣告，但可以改變其值

```javascript
let a = 10;
let a = 10;
```

<div class="em-div">
output:Identifier 'a' has already been declared
</div>

- 範例 4: `let`宣告的變數不能重複宣告，但可以改變其值

```javascript
let a = 1;
a = 2;
console.log(a);
```

<div class="em-div">
output:2
</div>

- 範例 5:`let`作用域僅在`{block}`內，因此在`{block}`外可以使用同個變數名稱宣告

```javascript
let a = 10;
if (true) {
  let a = 9;
  console.log(a);
}
console.log(a);
```

<div class="em-div">
output:9</br>
10
</div>

- 範例 6: 在`let`宣告前先提取變數`a`，但因為`let`不會幫忙初始化，而是**存在 TDZ**，因此會出現`Cannot access 'a' before initialization`

```javascript
console.log(a);
let a = 10;
```

<div class="em-div">
output: Cannot access 'a' before initialization
</div>

## 關於 const

> 常數（constant）又稱定數，是指一個數值固定不變的數，例如圓周率，與之相反的是變數。

### 重點

<div class="em-div2">

- `const`為<b>區塊作用域(block scoped)</b>，無法在`{block}`之外存取它
- 使用`const`宣告的變數不能改變，亦不能重複宣告。
- 若使用 `const` 宣告，不能改變其屬性，但能夠改變內含的東西。</div>

相對其他的宣告方式，`const`規定相對嚴格，所以不像`var`會有很多花裡胡哨的例外行為，是個很乖巧的好孩子呢(๑¯◡¯๑)。

- 範例 1: 使用`const`宣告的`a`，不能夠重新賦值，會出現`TypeError`

```javascript
const a = 10;
function fn() {
  a = 9;
  console.log(a);
}
fn();
```

<div class="em-div"> output: TypeError: Assignment to constant variable.</div>

- 範例 2: `const`同樣為<b>區塊作用域(block scoped)</b>，在`{block}`外不能存取，會出現 `ReferenceError: a is not defined`

```javascript
function fn() {
  const a = 9;
}
console.log(a);
```

<div class="em-div">
output:
ReferenceError: a is not defined
</div>

- 範例 3:使用 `const` 宣告，不能改變物件，但能夠替換物件內的東西。

```javascript
const a = {
  prop1: 10,
  prop2: 9,
};
a.prop1 = 3;

console.log(a);
```

<div class="em-div">
output:
{ prop1: 3, prop2: 9 }
</div>

- 範例 4:`const`宣告不能改變物件

```javascript
const a = {
  prop1: 10,
  prop2: 9,
};

a = {
  prop1: 20,
};

console.log(a);
```

<div class="em-div">
output: TypeError: Assignment to constant variable.
</div>

範例 3 和 4 可以想像成，乖寶寶`const`宣告了 a 是一盒工具箱，你可以往裡面加把手、螺絲起子等等工具，也可以拿走裡面的工具，但你不能再說工具箱是醫藥箱，乖寶寶`const`宣告了醫藥箱就是醫藥箱！(除非被{...}包住了)

我自己是這樣記憶的，會比較好懂 ٩(ˊᗜˋ )و

---

看了上面這麼多範例，感覺就缺少一個東西，那就是比較表格！我特別整理了一個表格給大家參考 o(^▽^)o

### var, let, const 比較表格

<img src="/images/var-let-const/table2.png">

### 宣告準則（建議）

為了防其他的程式碼被宣告所污染到處都拿得到 var 宣告的變數，就像任何人都可以把警察局說成是教堂，把板手說成是棒棒糖，想起來有點可怕 ╰(°▽°)╯，所以建議的宣告順序如下：

1. const
2. let
3. var

當然實際還是依照需求而定，希望今天的文章有幫助到您！

另外在文章內提到的變數提升，也歡迎參考我另外一篇文章的解釋：<a href="https://adora-xu.com/2023/11/18/hoisting/">JavaScript: 變數提升 hoisting 到底是什麼？</a>

文章內容是根據以下參考資料整理＋自己繪圖而得，如果有任何建議都歡迎提供唷 (´･∀･｀)

參考資料：

> [五倍學院 - 為你自己學 JavaScript 課程內容](https://5xcampus.com/courses/js101)

> [ExplainThis：在 JavaScript 中用 var, let, 以及 const 有什麼差別？什麼時候該用哪個？](https://www.explainthis.io/zh-hant/swe/js-var-let-const-in-javascript)

> [Difference between var, let and const keywords in JavaScript](https://www.geeksforgeeks.org/difference-between-var-let-and-const-keywords-in-javascript/)
