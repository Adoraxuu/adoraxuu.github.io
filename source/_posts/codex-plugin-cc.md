---
title: 用 Claude Code + Codex 當你的「背景資深 Reviewer」：codex-plugin-cc 使用心得
date: 2026-04-01 15:44:54
tags: [AI, Claude Code, Codex, Code Review, Plugin]
---

![cover](cover.webp)

最近在玩 [codex-plugin-cc](https://github.com/openai/codex-plugin-cc)，在 Claude Code 裡裝上 OpenAI 的 Codex plugin，讓 Codex 變成一個「在背景默默幫你審查、修 bug 的資深同事」。

這篇想簡單介紹一下這個 plugin 在做什麼、實際使用起來的感覺，以及它改變了我寫程式的哪些地方。

<!-- more -->

## 這個 plugin 到底在做什麼？

一句話版本：讓你在 Claude Code 裡，用一組 `/codex:*` 指令把工作丟給 Codex，請它幫你做 code review 或修 bug，而且可以背景跑，不打斷你手上的開發。

比較展開一點，它提供了幾個主要能力：

- **`/codex:review`：一般 code review。**
  直接對你的目前變更或「某個 branch 對 main 的 diff」做審查，給出很正常、偏保守的建議。適合你想要多一雙眼睛幫你看漏網之魚。

- **`/codex:adversarial-review`：唱反調模式的 review。**
  這個模式會刻意質疑你的設計和假設，而不是只看表面的 bug。你可以在指令後面接一句自然語言去「下指令」，例如：
  `/codex:adversarial-review --base main challenge whether this was the right caching and retry design`
  它就會專心幫你拆你這個設計是不是想得太美。

- **`/codex:rescue`：把一個問題外包給 Codex。**
  像是：
  - 「幫我調查為什麼測試開始 fail」
  - 「用最小安全改動修掉這個 bug，順便加一個回歸測試」
  - 「幫我設計一組測試，專門測某個 edge case」

通常會搭配 `--background`，讓它在後台慢慢啃；你則用 `/codex:status` 看進度，用 `/codex:result` 收成。

整體上，這個 plugin 不是要取代 Claude Code，而是把 Codex 變成「另一個後端服務」：Claude 跟你聊天、陪你設計，Codex 在旁邊當計算與審查引擎。

## 安裝與設定：幾乎是 plug-and-play

前提很簡單：你只要有 ChatGPT 帳號（有訂閱就更好），再加上 Node.js 18.18+ 環境，就可以開始玩。

![codex-plugin-cc 安裝流程](codex-plugin-cc-install.webp)

1. 用 npm 在本機裝 Codex CLI，然後 `codex login`，用你現有的 ChatGPT 帳號登入。
2. 在 Claude Code 裡加入 marketplace 的 `openai/codex-plugin-cc`，安裝 `codex@openai-codex` plugin。
3. 跑 `/reload-plugins` 之後，用 `/codex:setup` 確認 Codex 有裝好也有登入。

如果一切順利，你會看到一堆新的 `/codex:*` 指令可以用，還多出一個 `codex:codex-rescue` subagent。

這點我滿喜歡的：**它不是去弄一套額外的雲端認證與設定，而是直接吃你本機原本的 Codex CLI 狀態和 config。** 你本來怎麼用 Codex，它就沿用那一套。

## 實戰：抓出一個資料遺失 bug

今日測試使用 `/codex:adversarial-review`，來檢查我的專案的新功能——可以將 AI 整理好的 Notion 的單字表匯出成 Anki、CSV 格式。

![adversarial-review 背景執行](adversarial-review-background.webp)

結果用 Codex 來抓 bug，還真的抓到一個 Notion 匯出時「悄悄吃資料」的 bug。

情境大概是這樣：把單字匯出成 CSV / Anki 的流程，table 有時有 header、有時沒有。原本的實作很直覺，直接對 rows 做 `slice(1)`，假設第一列一定是標題。

Codex 的 adversarial review 直接點破問題：**headerless 的 Notion table 會被默默丟掉第一列，導致第一個單字永遠不見。**

![adversarial-review 結果](adversarial-review-result.webp)

也就是說：

- `findVocabTables` 接受沒有 header 的 table。
- 但 `collectVocab` 一律跳過 `rows[0]`。
- 結果 headerless table 的第一筆 vocab 會靜悄悄消失。

接著我用 `/codex:rescue` 丟了一句「用最小安全改動修掉這個 bug，順便加個回歸測試」，讓它在背景幫我想修法。

最後它給出一個很小但漂亮的 patch：

![codex-rescue 結果](codex-rescue-result.webp)

1. 先讀 Notion table 的 `has_column_header`。
2. 只有在 `has_column_header === true` 時才 `rows.slice(1)`，否則保留所有 rows。
3. 把抓資料的函式改成可注入的 fetchers，測試可以用假的 Notion 回應。
4. 多一個 regression test，專門驗證 headerless table 不會再吃掉第一列。

改動範圍小、邏輯單純、還順便補了測試，完美符合「請你幫我修，但拜託不要大翻修」的期待。

## 背景工作的體驗怎麼樣？
這個 plugin 最打中我的點，就是**可以把 Codex 當一個真正的 background worker**。

平常如果你想找第二個模型幫忙看 code，很典型的流程是：切視窗、開網頁、貼 code、等回應。你的人會被硬拉出「寫程式的 zone」，然後回來時 momentum 已經散掉一半。

這裡的操作模式完全不同：
![code-review 背景執行](code-review-background.webp)
![codex-rescue 背景執行](codex-rescue-background.webp)

1. 下指令：`/codex:review --background` 或 `/codex:rescue --background ...`
2. 繼續寫你的 code，好像什麼事都沒發生。
3. 偶爾丟 `/codex:status` 看一下：哪些 review / rescue 還在跑、哪些完成了。
4. 對某個 task 下 `/codex:result task-xxxx`，一次看完它的總結與建議。

Claude Code 還是照樣跟你 pair programming，回答你的問題。Codex 則像是你辦公室角落那個安靜的資深工程師，被你丟了一句：「幫我檢查一下這整段改動有沒有哪裡心裡不踏實。」然後過一陣子回頭說：「欸，有幾個點你可能想再看一下。」

這種「非同步審查」對開發節奏的幫助，比我預期的大很多。你不是停下來等 AI 想完，而是讓 AI 在背景幫你「拉高信心」。

## 使用心得：優點與現實成本

優點其實你應該已經感覺到了，但我還是整理一下我自己的感受。

**優點：**

- **真的像多了一個 model 在幫你唱反調。**
  Claude Code 一般會跟你一起把東西「做出來」，Codex 的 adversarial review 會問：「你確定嗎？」這種角色分工在工程上剛好很互補。

- **背景執行讓你保留專注力。**
  這點太重要了。你只要接受「等一下再看結果」，你的開發 flow 完全不用被中斷。

- **小修 bug 的體驗很好。**
  用 `/codex:rescue` + 對「最小安全改動」的要求，通常會得到邏輯精準、改動範圍小的 patch，再加上對應的回歸測試 proposal。你可以自己決定要不要全部採用。

- **沿用既有 Codex 設定。**
  不需要再搞一套 API base URL、key、config，plugin 直接把本機 Codex CLI 當單一真實來源，這種設計對開發者真的很貼心。

**現實成本：**

- **Token 雙重消耗：Claude Code + Codex。**
  你其實是請了兩個 AI 同事：一個跟你 pairing，一個在背景幫你審查。用量一定會比較高，所以這套比較適合用在「你真的在意品質」或「真的覺得有風險」的改動上，而不是每一個 typo 都叫 Codex 看。

- **長任務有時候會跑比較久。**
  尤其是 `/codex:rescue` 幫你做比較深的調查或設計時，可能會跑幾分鐘。好在你可以隨時 `/codex:cancel task-xxx`，再開一個範圍更小、敘述更具體的任務。

- **它不是你腦子的替代品。**
  Codex 會給出看起來合理的 patch，但你還是要負責最後的判斷。真正理想的狀態，是把它當作「多一個角度的 reviewer」，而不是「幫我想完所有東西」。

## 什麼情境特別適合用這個 plugin？

我自己目前會在幾種狀況下特別愛用：

- 快要 merge 一個比較大的 feature branch 前，先跑一次 `/codex:adversarial-review --base main`，看它會不會指出一些你沒想到的風險。
- 遇到行為怪怪、但你一時抓不到問題的 bug，用 `/codex:rescue` 把「調查＋最小修正＋回歸測試設計」打包給它。
- 想幫自己建立一點「工程不安」的 buffers——讓另一個模型提醒你：「你在這裡假設太多東西了。」

如果你本來就有在用 Claude Code，而且也願意讓另一個 AI 模型進來你的開發流程，codex-plugin-cc 是那種「一裝上就會開始懷疑自己以前為什麼沒有用這套」的插件。

它不會讓你變成完美工程師，但會讓你比較難在看似無害的角落留下小炸彈——尤其是那種「不會 crash、只是悄悄吃掉第一筆資料」的 bug。那些通常最久才被發現，而這類工作剛好就是最適合交給 Codex 這種背景 reviewer 的。
