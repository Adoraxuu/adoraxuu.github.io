title: 'JavaScript: defer vs async'
layout: post
author: Adora Xu
tags:
  - [JavaScript]
  - [defer]
  - [async]
  - [Web]
  - [rendering]
categories:
  - [JavaScript]
date: 2023-11-18 22:00:00
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

###

<img src="/images/async_vs_defer/title.png">

在製作網頁時，通常會用三個方法來將 JavaScript 載入網頁：

1. 將`<script>`放在`</body>`前一行
2. `async`
3. `defer`

這樣你的網頁才能夠順利動起來，為什麼要這樣做呢？究竟這三種方法有什麼差別呢？以下將會逐一說明(ㆁωㆁ\*)

要先理解`async`,`defer`的差別前，要先了解，瀏覽器是怎麼轉譯網頁的，這樣能夠更理解 JavaScript 使用不同方法載入的差別

## 瀏覽器是怎麼繪轉譯頁的呢？

<img src="/images/async_vs_defer/1webdev.png">

> 圖片來源[web.dev](https://web.dev/articles/critical-rendering-path/constructing-the-object-model?hl=zh-tw)

### 網頁的轉譯流程：

- Bytes ➡️ Characters ➡️ Tokens ➡️ Nodes ➡️ DOM

### 流程解釋：

1. 從伺服器接收原始資料位元組(raw bytes of data)並將其轉換為人類看得懂的字元(Characters)。
2. 解析字元以提取 HTML 結構，此步驟稱為標記化（tokenization），它會產生標記（tokens）。
3. 將標記（tokens）轉換為節點（nodes）- 具有屬性的不同物件。
4. 連結節點（nodes）以建立資料結構，稱為 **DOM**（文件物件模型）和 CSSOM（CSS 物件模型）。

以上流程確保 HTML 和 CSS 快速地傳遞到瀏覽器(ㆁωㆁ\*)

> 要了解更多請參考：[Web.dev 建構物件模型](https://web.dev/articles/critical-rendering-path/constructing-the-object-model?hl=zh-tw)

這邊先幫大家畫個重點，「**建立 DOM 是瀏覽器轉譯網頁的最後一步驟**。」而「**在瀏覽器引擎完成建置 DOM 之前使用者不會看到網站**」

## JavaScript 是怎麼被引入到網頁呢？

如果任意把`<script>`放到 html 檔案內會發生什麼事？大家都知道，網頁很有可能會出問題( ͡° ͜ʖ ͡°)，不過究竟是為什麼呢？

在**建立 DOM 結構的過程中**，瀏覽器將發送請求，以獲取網站連結到的任何相關資源。（包括 JavaScript 在內）

為了更好地理解，這裡準備了一個範例，將`<script>`放在`<head>`內：

```css=
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="demo.js"></script>
</head>
<body>
    <div class="container">網頁內容</div>
</body>
</html>
```

這時建構的時間圖如下： <img src="/images/async_vs_defer/2rendering-time.png">

這會造成什麼樣的問題？因為 DOM 尚未建置好，所以 JavaScript 抓不到 DOM，進而無法進行操作。（辛苦寫的 JavaScript 無法發揮作用 இдஇ）

延遲 DOM 就緒狀態的不僅僅是 JavaScript。還有 CSS、圖像、影片等。瀏覽器引擎必須先載入所有資源才能完成具象。

當然睿智的工程師們早就知道這個問題的破解方法(๑˃̵ᴗ˂̵)ﻭ 這裡就要提到在開頭說的第一個方法

## 將`<script>`放在`</body>`前一行

<img src="/images/async_vs_defer/3rendering-time-endofbody.png">

- 瀏覽器會先解析完整個 HTML，再下載 JavaScript 檔案、解析，因此使用者可以在第一時間看到畫面。
- Script 有可能會異動到 HTML 元素，因此將` <script>` 置於`<body>`的底部，可以確保 HTML 元素已經完全載入，減少異動元素造成錯誤的風險。
- 直到 DOM 完全建置好後（但會在 DOMContentLoaded 事件前）才被「執行」

但這是好方法嗎？一切都看網頁的內容而定，如果 JavaScript 寫得很多檔案很大，或是由外部資源載入，可能就會導致網頁跑很慢(｡ŏ﹏ŏ)，使用者可能會覺得網頁壞掉 ಠ_ಠ

但如果想要網頁載入更快一點怎麼辦？這時候`async`就派上用場啦 🤩

## async

async 的意思是 **asynchrnous**，意即**非同步** 使用的範例如下：

```css
<script src="demo1.js" async></script>
<script src="demo2.js" async></script>
```

可以用圖片理解： <img src="/images/async_vs_defer/4rendering-time-async.png">

- 解析 HTML 與載入腳本，是非同步進行。因此`async`會告訴瀏覽器，在解析 HTML 時不用等`<script>`腳本的下載與執行。
- async 的腳本載入與 HTML 解析是彼此獨立，async**下載完就會馬上執行**。
- 除了跟 DOM 的建構是彼此獨立之外，帶有 async 屬性的腳本跟其他的腳本也是**彼此獨立**，哪個先下載完成就先執行。
- 雖然在下載時不會暫停 HMTL 的解析，但在執行時其他的 Script 時會暫停解析（例如範例的 demo1 會先執行，接著再解析 demo2）
- 通常使用在 Script 載入跟 DOM、其他腳本是沒有相互依賴關係時，例如 Google Analytics 這類分析用的腳本。
- async 若放在 html 中間，則拿不到後面解析的 DOM。

async 真是省時間 ʕ•ᴥ•ʔ，但這是最好的方法嗎？並不是，一切也要看網頁的內容而定，如果有很多相互依賴的 Script 或是 Script 需要取得 DOM 元素就不適合。

所以就有了第三種解決這問題的方法啦！那就是`defer`

## 什麼是 defer?

```css
<script src="demo1.js" defer></script>
<script src="demo1.js" defer></script>
```

<img src="/images/async_vs_defer/5rendering-time-defer.png">

- defer 會告訴瀏覽器，不用等腳本的下載與執行，可以繼續完成 HTML 的解析與 DOM 的建構；在建構 DOM 的同時，會在背景中載入腳本，因此 defer 不會擋住畫面的具像（渲染）。
- 如果腳本在 HTML 解析完成前就下載好，會等到 HTML 都**完全解析**後，才會執行。因此如果有腳本是需要等 HTML 解析完、DOM 完整建立後才能載入，那麼會需要選 defer。
- 如果同時有多個帶有 defer 屬性的`<script>`資源，瀏覽器會同步下載，只是會依照在 HTML 中的順序執行。
- 有些時候，可能某個腳本會依賴另一個腳本，例`demo1.js`依賴`demo2.js`，在範例中使`defer`可以確保`demo1.js`會在`demo2.js`之前執行。

## 放在`</body>`前 v.s. Async v.s. Defer

了解這三種不同的方法時，自己也頭昏眼花了一陣子，所以特別製作了表格來幫助大家釐清： <img src="/images/async_vs_defer/6differ-table.png">

本篇文章是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> [curiosum：Async vs Defer](https://curiosum.com/blog/seo-speed-script-tags-async-vs-defer)

> [ExplainThis：script 標籤應該放在 HTML 的什麼位置？link 呢？](https://www.explainthis.io/zh-hant/swe/script-link-in-html)

> [ExplainThis：script 的 async 與 defer 有什麼不同？](https://www.explainthis.io/zh-hant/swe/fe-script-async-defer-difference)

> [PJchender：[JS] Async, defer attributes](https://pjchender.dev/javascript/js-async-defer/)
