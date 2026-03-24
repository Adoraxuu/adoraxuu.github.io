---
layout: post
title: 'Ruby on Rails: 什麼是Abstraction layer'
date: 2024-03-31 23:19:47
author: Adora Xu
tags:
- [abstraction layer]
- [ruby]
- [rails]
- [layered design]

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

###
<img src="/images/ruby-on-rails-abstraction-layer/banner.jpg">

> 圖片來源: [Usplash - Clark Van Der Beken](https://unsplash.com/photos/orange-pink-and-teal-illustration-Tk0B3Dfkf_4)

##

首圖的layer好美٩(๑❛ᴗ❛๑)۶，不過對於程式的世界，Layer的意義非常地遼闊，來看下維基百科對於Layer的定義：

<div class="em-div2">

[物件導向設計](https://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%AE%BE%E8%AE%A1)中的**層**（layer）是一群和其他[模組](https://zh.wikipedia.org/wiki/%E8%BB%9F%E9%AB%94%E6%A8%A1%E7%B5%84)有類似[耦合性](https://zh.wikipedia.org/wiki/%E8%80%A6%E5%90%88%E6%80%A7_(%E8%A8%88%E7%AE%97%E6%A9%9F%E7%A7%91%E5%AD%B8))的[類別](https://zh.wikipedia.org/wiki/%E7%B1%BB_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6))。層也可以說是一組可[復用](https://zh.wikipedia.org/wiki/%E5%A4%8D%E7%94%A8)的軟體組件，可以在類似的環境下復用。

各層的模組會用[樹](https://zh.wikipedia.org/wiki/%E6%A0%91_(%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84))狀的階級架構來規劃，各層模組之間的依賴關係會用連接來表示。各層之間的依賴關係可能是[繼承](https://zh.wikipedia.org/wiki/%E7%BB%A7%E6%89%BF_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6))、[複合](https://zh.wikipedia.org/w/index.php?title=%E7%89%A9%E4%BB%B6%E8%A4%87%E5%90%88&action=edit&redlink=1)或聚合（aggregation）關係，不過也有可能是其他的關係。

分層是許多書籍中都會提到的[架構模式](https://zh.wikipedia.org/wiki/%E6%9E%B6%E6%9E%84%E6%A8%A1%E5%BC%8F)，例如《[Pattern-Oriented Software Architecture](https://zh.wikipedia.org/w/index.php?title=Pattern-Oriented_Software_Architecture&action=edit&redlink=1)》書中就有提到。

</div>

看到了裡面的`類別`、`繼承`，好像很熟悉，在Ruby on Rails中有使用到，但是整個組起來，看懂一點點又好像沒看懂？

接著來看看維基百科對於[Abstraction layer](https://zh.wikipedia.org/wiki/%E6%8A%BD%E8%B1%A1%E5%B1%A4)的定義：

<div class="em-div2">

**抽象層**（英語：abstraction layer， abstraction level，或 a layer of abstraction）是一種隱藏獨特功能執行細節的方法。使用抽象層的[軟體](https://zh.wikipedia.org/wiki/%E8%BB%9F%E9%AB%94)模型包含[OSI](https://zh.wikipedia.org/wiki/OSI%E6%A8%A1%E5%9E%8B)網路協定七層模型、[OpenGL](https://zh.wikipedia.org/wiki/OpenGL)繪圖函式庫及以Unix為原本的輸入輸出（I/O）模型，並適用於大部分新一代的作業系統。

在Unix作業系統中，大部分的輸入輸出形式被視為從裝置讀寫的串流值。而串流值模型為了提供獨立裝置被檔案及終端機I/O所使用。為了在應用層讀寫裝置，程式呼叫[函式](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B8)開啟像是[終端機](https://zh.wikipedia.org/wiki/%E7%B5%82%E7%AB%AF%E6%A9%9F)的實體裝置或是[網路](https://zh.wikipedia.org/wiki/%E9%9B%BB%E8%85%A6%E7%B6%B2%E8%B7%AF)、[檔案](https://zh.wikipedia.org/wiki/%E6%AA%94%E6%A1%88)系統的[虛擬](https://zh.wikipedia.org/wiki/%E8%99%9B%E6%93%AC)裝置。裝置的[實體](https://zh.wikipedia.org/wiki/%E5%AF%A6%E9%AB%94)特徵是被允許程式設計師讀寫資料的作業系統所調用。接著[作業系統](https://zh.wikipedia.org/wiki/%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1)操作正確的讀寫轉換。

大部分的[圖形庫](https://zh.wikipedia.org/wiki/%E5%9B%BE%E5%BD%A2%E5%BA%93)，像是OpenGL，提供抽象圖形裝置模型。這種函式庫的主要功能在於轉換[程式設計師](https://zh.wikipedia.org/wiki/%E7%A8%8B%E5%BC%8F%E8%A8%AD%E8%A8%88%E5%B8%AB)的指令到[圖形元件](https://zh.wikipedia.org/w/index.php?title=%E5%9C%96%E5%BD%A2%E5%85%83%E4%BB%B6&action=edit&redlink=1)。這個特殊的繪圖指令與對傳統陰極射線管銀幕的指令不同，而是隱藏了抽象介面的細節。

在[資訊學](https://zh.wikipedia.org/wiki/%E8%B3%87%E8%A8%8A%E7%A7%91%E5%AD%B8)，抽象層是[模型](https://zh.wikipedia.org/wiki/%E6%A6%82%E5%BF%B5%E6%A8%A1%E5%9E%8B_(%E8%B3%87%E8%A8%8A%E7%A7%91%E5%AD%B8))或[演算法](https://zh.wikipedia.org/wiki/%E6%BC%94%E7%AE%97%E6%B3%95)的推廣。

</div>

完蛋了，完全看不懂 (ఠ్ఠ ˓̭ ఠ్ఠ)。

其實抽象層很常出現在Ruby on Rails裡面，Ruby on Rails的所有的東西都是物件，而我們所熟悉的MVC，也是物件。`view object` / `controller object` / `model object` 彼此傳遞著訊息，**這些物件在位階上都屬於抽象層。**

網頁的生命週期分成兩個階段：
1. bootstrap phase (configuration and initialization)
- 載入應用程式碼
- 初始化和配置框架元件
2. serving phase
- 執行許多獨立的Web Request
    

而我們先暫時聚焦在serving phase上，因為網頁請求(Web Request)是獨立的，因此服務階段可以被視為一條流水線，我們將請求資料（raw material）放在輸送帶上，中間通過許多工作站，並在最後得到回應

試想像，要怎麼讓流水線提高效率呢？就要把工作站拉出來，看有什麼可以改進的，而抽象層，就可以想像成流水線上的工作站。把工作站拉出來的這個過程（extraction of abstraction layers），也可以稱作分層（layering），而這個架構模式可以提高軟體的品質。

而一個抽象層應該要有什麼屬性呢？

- [單一職責](https://zh.wikipedia.org/wiki/%E5%8D%95%E4%B8%80%E5%8A%9F%E8%83%BD%E5%8E%9F%E5%88%99)（職責可以很廣泛，但不應重複，遵循[關注點分離](https://zh.wikipedia.org/wiki/%E5%85%B3%E6%B3%A8%E7%82%B9%E5%88%86%E7%A6%BB)）
- 各層應該是鬆散耦合的，並且沒有循環或反向依賴關係。
    - 如果我們從上到下繪製請求處理流程，層間連接永遠不應該向上，並且我們應該盡量減少層間連接數。（可以想像成流水線，很少會流到一半倒回去做）
- 抽像不應洩漏其內部結構
    - 提取抽象的主要想法是將介面與實作分離
- 應該可以單獨提取抽象層

如果遵循以上的這些原則，可以讓開發者易於重構、debug和測試。

在現實生活中，衡量效率的標準是速度； 在軟體開發中，速度也是關鍵——發布新功能的速度，而要怎麼能夠快速地發布新功能？這取決於許多因素，而最主要的因素就是程式碼的**可維護性**，其中包括：添加新功能和對現有功能進行更改、除錯的容易程度，應用軟體設計模式和提取抽象層是保持高可維護性的兩個主要工具。

而抽象層不是越多越好，主要看整個應用程式的架構，基本的Rails僅包含了三個抽象層概念：Model、Controller、View(也就是我們所常說的MVC)

至於要如何逐步引入新的抽象層呢？

可以參考本書：[Layered Design for Ruby on Rails Applications](https://www.packtpub.com/product/layered-design-for-ruby-on-rails-applications/9781801813785)

未來也會將本書提到的有趣的概念整理分享，今天就先到這裡٩(●˙▿˙●)۶
本篇文章是我由以下參考資料整理而成，如果您有興趣了解更多，請參考：

### 參考資料：
> [Layered Design for Ruby on Rails Applications](https://www.tenlong.com.tw/products/9781801813785)**

> [Wiki](https://zh.wikipedia.org/wiki/Wikipedia:%E9%A6%96%E9%A1%B5)

