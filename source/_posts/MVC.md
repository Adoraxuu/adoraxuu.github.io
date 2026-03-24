---
layout: post
title: "Ruby on Rails: MVC是什麼?"
date: 2023-12-02 14:11:56
author: Adora Xu
tags:
  - [Ruby]
  - [Rails]
  - [MVC]

categories:
  - [Ruby on Rails]
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

<img src="/images/MVC/banner.png" alt="">

### MVC 定義：

依據[維基百科](https://zh.wikipedia.org/zh-tw/MVC)的介紹：

<div class="em-div2">
MVC模式（Model–view–controller）是軟體工程中的一種軟體架構模式，
把軟體系統分為三個基本部分：模型（Model）、視圖（View）和控制器（Controller）。
</div>

MVC 模式最早由 Trygve Reenskaug 在 1978 年提出，是全錄帕羅奧多研究中心（Xerox PARC）在 20 世紀 80 年代為程式語言 Smalltalk 發明的一種軟體架構。

我們今天使用的網站是很複雜的應用程序( ˘•ω•˘ )，為了簡化程式碼並使其更方便使用，許多工程師選擇使用**MVC**來佈局他們的專案。

將大型應用程式分解為較小的部分，再各自負責專門的事 o(_^▽^_)o

## MVC 各自負責什麼？

理解完 MVC 的背景了，接下來看看，MVC 各自負責什麼工作內容：

- 模型（Model） - 程式設計師編寫程式應有的功能（實現演算法等等）、資料庫專家進行資料管理和資料庫設計(可以實現具體的功能)。
- 視圖（View） - 介面設計人員進行圖形介面設計。
- 控制器（Controller）- 負責轉發請求，對請求進行處理。

參考資料：[維基百科 MVC](https://zh.wikipedia.org/zh-tw/MVC)

## MVC 當前趨勢

MVC 架構已經變得如此流行，以至於現在大多數流行的框架都遵循 MVC 設計模式來開發應用程式。

目前 MVC 設計模式的流行框架有： <img src="/images/MVC/mvcframwork2.png" alt="">

## Ruby on Rails 的 MVC 架構

看完了以上三項解釋，應該還是很難明白到底 MVC 的實際應用，以下將用 Ruby on Rails 裡的Ｍ運作流程，搭配圖片來講解 d(`･∀･)b

可以透過以下圖片來理解： <img src="/images/MVC/01.png" alt="">

### 1. Routes

 <!-- <img src="/images/MVC/02.png" alt=""> -->

當有使用者輸入網址，連到網站的時候，第一關會遇到的是 routes Rails 會根據使用者輸入的網址及參數，去尋找相對應 Controller 上的 Action。

### 2. Controller

<!-- <img src="/images/MVC/03.png" alt=""> -->

可以想像成控制處理中心 or 指揮官， Controller 會和 model 要資料、View 要畫面，最後提供給 routes，再讓使用者看到網頁畫面。 Controller 通常會有一個以上的 Action，這些 Action 就是 Ruby 裡的方法（method）。

### 3. Model

<!-- <img src="/images/MVC/04.png" alt=""> -->

Model 本身並不是資料庫，你可以把它想像成翻譯官，它可以幫你把你跟 Model 說的「人話」轉成資料庫看得懂的資料庫查詢語言（Structured Query Language，簡稱 SQL），透過 SQL，Model 可以跟資料庫取得你想要的資料。

### 4. View

<!-- <img src="/images/MVC/05.png" alt=""> -->

View 為使用者最後看到的畫面，雖然 Controller/Action 拿到資料了，但目前這包東西還沒美化、整理過，還不適合給使用者看，所以 Controller/Action 還需要跟 View 借一下畫面，讓資料更適合使用者閱讀。

通常畫面的呈現會寫在這裡，使用的是`.html.erb`檔案。

詳細內容可以參考龍哥的[為你自己學 Ruby on Rails](https://railsbook.tw/chapters/10-mvc#mvc-flow)

---

本篇文章是參考以下資料，透過自己繪圖+整理而成，如果有什麼問題歡迎與我討論！(_^▽^_)

<div class="em-div2">
參考資料：

[為你自己學 Ruby on Rails](https://railsbook.tw/chapters/10-mvc#mvc-flow)

[維基百科 MVC](https://zh.wikipedia.org/zh-tw/MVC)

[MVC Architecture – Detailed Explanation](https://www.interviewbit.com/blog/mvc-architecture/)

</div>
