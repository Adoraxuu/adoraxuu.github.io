---
layout: post
title: '用手機遙控電腦開發？我用 Claude Dispatch 試了一把 🚀'
date: 2026-03-24 15:00:00
tags: [AI, Claude, Dispatch, Workflow, AI-Assisted Development, GitHub, Productivity]
categories: AI
---

今天做了一件以前覺得「幹嘛這樣搞」、現在覺得「超香」的事——**用手機遙控電腦跑整個開發流程**。

工具：Claude Dispatch。體驗：出乎意料地順。

<!-- more -->

## Dispatch 是什麼？

簡單說，Claude Dispatch 是一個 **orchestrator 模式**的 AI 代理框架。你下指令，它負責把任務分解、派發給不同的子 session 執行，每個 task 都在隔離的 worktree 環境中跑，不會互相污染。

架構上有幾個特點讓我印象深刻：

- **Task session 隔離**：每個任務都跑在獨立的 git worktree，改壞了也不怕影響主線
- **Orchestrator 調度**：主 session 負責協調，子 agent 負責執行，分工清楚
- **Permission gate**：每個有副作用的動作（push code、開 issue、建 PR）都會先問你確不確定，不會偷偷幫你做危險的事

這個設計其實很符合現代軟體開發的思路——**關注點分離 + 最小權限原則**，只是搬到 AI workflow 上了。

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
