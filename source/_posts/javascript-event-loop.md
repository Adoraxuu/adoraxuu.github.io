title: 'JavaScript: 究竟什麼是 Event Loop？'
layout: post
author: Adora Xu
tags:
  - [JavaScript]
  - [Event Loop]
categories:
  - [JavaScript]
date: 2023-11-08 11:22:00
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

<img src="/images/eventLoop/blocks.jpg">
JavaScript 是單執行緒又稱為單線程（Single Thread）的設計，它一次只能執行一個任務， 而「Event Loop」就是使 JavaScript 能夠處理多個任務的秘訣

其中包含了：Call Stack（呼叫堆疊）、Event Loop（事件循環）、Callback Queqe(回調佇列)，以下會逐步介紹。

### Call Stack 呼叫堆疊

<img src="/images/eventLoop/callstack.png">

<ul>
  <li>因為 JavaScript 為單執行緒的設計，**一次只能執行一個動作**，因此等待執行的任務會被放入 Call Stack。</li>
  <li>準備執行的任務會像積木一樣，由下往上堆疊。</li>
  <li>執行時，也像積木一樣，由上往下拿取，也稱為先進後出法（LIFO, Last in First out）</li>
</ul>
<div class="em-div">但如果出現像 SetTimeout()、fetch()等等運行時間較長的函數，該怎麼辦呢？這個時候 Web API 就派上用場了！</div>

### Web APIs

<img src="/images/eventLoop/webapis.png">

<ul>
  <li>每當呼叫非同步函數時，它都會被傳送到對應的瀏覽器 API，該 API 會等待完成函數延遲的操作後再傳回進行處理。</li>
  <li>以 setTimeout()為例，當 Call Stack 執行時，setTimeout()會被傳送到 Web API，等待指定的時間完成再將該操作傳回進行處理。</li>

<div class="em-div">從 Web API 等時間跑完後，它將操作發送到哪裡？他們被發送到「事件佇列（Event Queue）」</div>

### Event Queue 事件佇列

<img src="/images/eventLoop/eventque.png">

<ul>
  <li>Event Queue 裡面是待執行的任務，其中每個訊息都與一個 function 相關聯。</li>
  <li>當堆疊中有足夠空間時，會從 Event Queue 事件佇列拿取一個任務進行處理，處理過程包含了呼叫相關聯的 function。只有當堆疊清空時，該佇列才算是完成處理。</li>
</ul>

<div class="em-div">上面分別介紹了 Call Stack、Web APIs、Event Queue，接下來可以將他們組起來了！組起來後可以更好地理解 Event Loop。</div>

### Event Loop 事件循環

<img src="/images/eventLoop/eventloop.png">

<ul>
  <li>Event Loop 它不斷檢查 Call Stack 呼叫堆疊是否為空。</li>
  <li>如果為空：則從事件佇列 Event Queue 中新增函式至 Call Stack。</li>
  <li>新增函式至 Call Stack 的步驟，又稱為非同步回調（asynchronous ）</li>
  <li>如果不為空：處理 Call Stack 內的函數。</li>
</ul>

### 範例示意

當然，如果僅以上介紹，無法完整了解，這班準備了幾個程式碼及搭配[loupe](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)，讓讀者能夠更了解其運行步驟

可以按此[loupe](http://latentflip.com/loupe/?code=Y29uc29sZS5sb2coIkhpIik7CnNldFRpbWVvdXQoZnVuY3Rpb24gY2IoKSB7CiAgY29uc29sZS5sb2coInRoZXJlIik7Cn0sIDUwMDApOwpjb25zb2xlLmxvZygiZW5kIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)了解 JavaScript 運行以上程式的步驟

### 範例示範

```javascript=
console.log("Hi");
setTimeout(function cb() {
  console.log("there");
}, 5000);
console.log("end");
```

會得到以下結果：

```
Hi
end
there
```

### 範例示範 2

```javascript=
function foo() {
  return foo();
}

foo();
```

會得到以下結果：

<img src="/images/eventLoop/pasted-0.png">

此又稱為**Stack Overflow**，當 Stack 不斷增長，直到瀏覽器記憶體耗盡。是瀏覽器在哀嚎的聲音 😂😂

以上是關於 Event Loop 的介紹，是我由以下參考資料整理+自己繪圖而成，如果您有興趣了解更多，請參考：

### 參考資料：

> What the heck is the event loop anyway? | Philip Roberts https://www.youtube.com/watch?v=8aGhZQkoFbQ

> 請說明瀏覽器中的事件循環 (Event Loop) https://www.explainthis.io/zh-hant/swe/what-is-event-loop

> What is an event loop in JavaScript? https://www.educative.io/answers/what-is-an-event-loop-in-javascript
