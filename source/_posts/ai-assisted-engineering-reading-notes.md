---
layout: post
title: '閱讀筆記：How to Do AI-Assisted Engineering'
date: 2026-03-24 10:22:40
tags: [AI, AI-Assisted Engineering, Software Engineering, Code Review, Workflow, Reading Notes]
categories: AI
---

![cover](cover.png)

整理自 [How to Do AI-Assisted Engineering](https://newsletter.eng-leadership.com/p/how-to-do-ai-assisted-engineering) 一文，這篇文章訪問了 15 位工程師與技術主管，分享他們實際在工作中如何運用 AI 輔助工程，從構想到上線的完整流程。

## 最大效益不在「寫程式更快」

多位工程師強調，AI 帶來的最大價值不是加速打字，而是讓你能花更多時間在**設計、規格與規劃**上。他們會先用 AI 產生架構設計、資料模型、API 規格，再反覆質疑、修正設計，確認取捨與邊界條件後才開始寫程式。

實作階段則讓 AI 依照明確規格產生程式碼、測試與文件，工程師專注在**判斷方向對不對、實作是否合理**，而不是每一行都自己打。

## 把 AI 當「專家團隊」

許多人不把 AI 當單一助手，而是當作一個專家團隊來使用：

- TypeScript 問題 → 找 TS 專家
- React 架構 → 找前端專家
- Infra 問題 → 找 DevOps 專家

生成與驗證由不同「專家」負責，避免同一個 AI 自己寫、自己審。測試也常由另一個 agent 負責設計 edge cases、找出壞的處理流程，並將發現的 bug 轉成回歸測試。

## 標準化工作流程

很多人不再臨時亂 prompt，而是建立固定的工作流程：

- 標準化的 PR review 流程
- 需求拆解模板
- 規格產生與規劃步驟
- 用 `AGENTS.md`、`CLAUDE.md` 等檔案，把開發風格、命名規則、測試要求、PR 模板都寫死

有人用 AI 自動幫 PR 寫摘要、測試計畫、風險說明；有人用 AI 做 nightly scan，自動找 bug、開 ticket 與 PR。

## 工具選擇與搭配

受訪者會依情境分工使用不同工具：

- **Claude Code**：互動式工程夥伴
- **OpenAI Codex**：背景掃描與自動修正
- **Cursor / Windsurf**：IDE 整合開發
- 搭配 GitHub Actions、Figma、Jira、Slack 等，把 AI 深度嵌進 CI/CD 與工作流程

## 四個共通結論

1. **AI 是倍增器**：會放大你原本的流程好壞，流程混亂的話，技術債只會長更快。
2. **設計文件與規格是最重要的產出**：AI 會直接依照這些東西生成程式，品質取決於規格的品質。
3. **AI 不會把菜鳥變成老手**：它讓有經驗的工程師產能暴增，但仍需要深厚的工程判斷力。
4. **最大風險是缺乏嚴格 review**：太快接受第一版輸出，會迅速累積大量技術債。

## 一句話總結

> 先用嚴謹的設計和規格把問題框好，讓 AI 負責大量實作與檢查，再用人類的經驗做多輪嚴格審查，這樣才能又快又穩地產出高品質軟體。
