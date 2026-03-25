---
layout: post
title: '用手機遙控電腦開發？我用 Claude Dispatch 試了一把 🚀'
date: 2026-03-24 15:00:00
tags: [AI, Claude, Dispatch, Workflow, AI-Assisted Development, GitHub, Productivity]
categories: AI
description: '實測用手機透過 Claude Dispatch 遠端操控電腦執行開發任務，介紹 orchestrator 模式的 AI 代理框架如何實現任務隔離、調度與 permission gate 機制。'
---

今天使用 Claude Dispatch 用手機遙控電腦跑整個開發流程٩(●ᴗ●)۶

<p align="center">
  <img src="/images/claude-dispatch-mobile-workflow/dispatch-screenshot.jpg" width="500" alt="Dispatch 手機操作截圖">
</p>

<!-- more -->

## Dispatch 是什麼？

簡單說，Claude Dispatch 是一個 **orchestrator 模式**的 AI 代理框架。你下指令，它負責把任務分解、派發給不同的子 session 執行，每個 task 都在隔離的 worktree 環境中跑，不會互相污染。

架構上有幾個特點讓我印象深刻：

- **Task session 隔離**：每個任務都跑在獨立的 git worktree，改壞了也不怕影響主線
- **Orchestrator 調度**：主 session 負責協調，子 agent 負責執行，分工清楚
- **Permission gate**：每個有副作用的動作（push code、開 issue、建 PR）都會先問你確不確定，不會偷偷幫你做危險的事

這個設計其實很符合現代軟體開發的思路——**關注點分離 + 最小權限原則**，只是搬到 AI workflow 上了。

## 等等，我坐在電腦前直接用 Claude Code 不也差不多？

說實話，一開始我也這樣想。搞什麼手機遙控，我人都在電腦前了，開個 terminal 直接跑不就好了？

但用了之後才發現——差別在**多工**。

當 Dispatch 在背景跑任務的時候，我可以繼續用電腦做自己的事。手機負責下指令、確認進度，電腦那邊 Claude Dispatch 默默把 issue 開好、把 code 寫好，我的主力工作完全不用中斷。兩條線同時走，效率真的不一樣。

## 從構想到 PR，全程用手機完成

今天的實驗是在我的 side project [vocabot](https://github.com/Adoraxuu/vocabot) 上開了一個 issue 和一個 PR。

整個流程大概是：

1. 📱 手機打開 Claude，連到已開著的電腦
2. 🧠 跟 AI 說「幫我想這個功能要解決什麼問題」→ 讓 AI 發想 issue 內容
3. ✅ 確認 issue 描述沒問題
4. 🚀 Dispatch 呼叫 GitHub CLI 開 issue：[vocabot#1](https://github.com/Adoraxuu/vocabot/issues/1)
5. 💻 繼續指示開發，Dispatch 在隔離 worktree 裡寫 code
6. 📤 審核 diff，確認沒問題後發 PR：[vocabot#2](https://github.com/Adoraxuu/vocabot/pull/2)

以前這一套流程至少要打開電腦、切視窗、開 terminal、手動下一堆指令。現在坐在沙發上滑手機就完成了，而且每一個有影響的動作都有確認機制，不用擔心 AI 亂做事。

## 說沒擔心是騙人的

過程中還是有那麼一刻在想：Token 會不會就這樣被燒光？有些指令跑超久，會不會其實自己動手做還比較快？

但「解放雙手、只需要下命令」這個感覺實在太爽了，擔心歸擔心，手還是沒停下來繼續下指令 😆

## 技術棧觀察

Dispatch 底層用到的工具鏈挺有趣的：

- **GitHub CLI (`gh`)**：開 issue、建 PR 都走 `gh` 指令，不是直接打 API
- **Git worktree**：隔離環境的核心，每個任務跑在獨立分支和目錄
- **MCP servers**：擴充 AI 能操作的工具範圍，例如 Notion MCP 可以讓 AI 直接讀寫你的筆記

這讓我想到最近 AI-assisted development 的趨勢：不是「AI 幫你寫更快」，而是**把 AI 嵌進整個 dev workflow 的節點**——設計、規格、實作、review、部署都有 AI 參與，工程師的角色更像是在做**方向把關和品質審核**。

## 想再試的功能

這次只碰到了皮毛，後面想繼續實驗的：

- **開發工作流自動化**：從 issue 到 PR 到 merge 的完整流程，看能自動化到什麼程度
- **跨工具協調**：讓 AI 同時操作 GitHub + Notion + Slack，跨平台資訊同步
- **排程任務**：定時跑測試、更新依賴、生成報告之類的

老實說，我對「AI 幫你想功能」這件事一開始有點抗拒——感覺像在偷懶。但今天實驗下來，我覺得更準確的描述是：**AI 幫你快速把模糊的想法轉成結構化的規格**，你還是要判斷方向對不對、內容合不合理。創意和判斷還是在你身上，AI 只是幫你省掉「把想法寫清楚」這個耗時的步驟。

這個工具目前還很新，但已經讓我看到一些有趣的可能性。繼續觀察中。🔍

## 延伸閱讀

想知道更多可以參考 Claude 的介紹影片：https://youtu.be/NAauIR6JFps

Anthropic 現在讓 Claude 可以「直接控制你的電腦」，幫你做事，例如：打開你硬碟上的檔案、用瀏覽器或開發工具操作、甚至透過像 Google Calendar、Slack 這類連動的 app 來完成任務。若沒有現成的 connector，它會直接模擬鍵盤滑鼠，自己點來點去、打字操作。

這功能目前是「研究預覽版」，只開放給 Claude Pro 和 Claude Max，用 macOS 的電腦才可以用。Claude 在執行前都會先跟你確認，你也可以隨時中止。

因為這類「agentic AI」可以很快做出大動作，也可能被惡意人士入侵利用，所以安全風險是重點。Anthropic 表示有做安全防護，比如掃描提示注入（prompt injection）等攻擊，不過也自己提醒使用者：功能還很新，可能有錯，建議不要拿來操作敏感資料，某些敏感 app 也預設是關掉的。

這項「電腦控制」功能可以和 Anthropic 的 Dispatch 搭配，用手機就能指派電腦上的任務，像是每天幫你看 email、幫你開啟 Claude Cowork 或 Claude Code session，甚至幫你產出早晨簡報或跑測試。不過因為都還在初期，較複雜的任務可能會失敗，Anthropic 也藉由這次預覽版收集回饋來改進。
