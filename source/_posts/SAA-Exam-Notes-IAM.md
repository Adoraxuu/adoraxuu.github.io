---
layout: post
title: 'AWS SAA 學習筆記：IAM'
date: 2025-11-18 20:00:00
tags: [AWS, SAA, IAM]
categories: AWS
---

AWS Solutions Architect Associate (SAA-C03) 考試準備筆記，聚焦於 IAM 相關的考點與易混淆概念。

> **學習資源：** Udemy - [Ultimate AWS Certified Solutions Architect Associate SAA-C03](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/) by Stephane Maarek

## IAM Security Tools

### 題目：Which of the following is an IAM Security Tool?

**錯誤選項：** `IAM Security Advisor`
**正確答案：** `IAM Access Analyzer`

### AWS 官方 IAM Security Tools（共 4 個）

| 工具 | 功能 | 考試重點 |
|------|------|----------|
| **IAM Credentials Report** | 帳號層級的使用者憑證狀態報告 | 審計密碼、Access Key、MFA 啟用狀態 |
| **IAM Access Advisor** | 顯示使用者/角色最近存取的服務 | 識別並移除過度授權（Least Privilege） |
| **IAM Policy Simulator** | 模擬策略執行結果 | 驗證特定 Action 是否被允許 |
| **IAM Access Analyzer** | 分析資源共用與跨帳號存取 | **高頻考點**：識別 public、cross-account 資源風險 |

**注意事項：**
- AWS 不存在 `IAM Security Advisor`、`IAM Resource Inspector`、`IAM Admin Assistant` 等服務
- SAA 考試常使用類似命名的干擾選項

---

## IAM Policy Statement 結構

### 題目：Statement consists of the following, EXCEPT:

**錯誤選項：** `Action`
**正確答案：** `Version`（或其他 Policy 層級的 metadata）

### Statement 必要與選填欄位

| 欄位 | 必要性 | 說明 |
|------|--------|------|
| **Effect** | 必須 | `Allow` 或 `Deny` |
| **Action / NotAction** | 必須 | 指定允許或拒絕的 API 操作 |
| **Resource / NotResource** | 必須 | 指定受影響的 ARN |
| **Condition** | 選填 | 條件限制（IP、MFA、時間等） |
| **Principal** | 特定情境 | 僅用於 Trust Policy（資源型策略） |

### 關鍵概念

`Version` 是 **Policy 層級** 的 metadata，**不屬於 Statement** 的組成部分。

IAM Policy 結構範例：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

---

## 重點整理

1. **IAM Security Tools 僅有 4 個官方工具**，其他類似命名皆為干擾選項
2. **IAM Access Analyzer** 是 SAA 高頻考點，用於識別資源外部存取風險
3. **Policy 與 Statement 結構需明確區分**：`Version` 位於 Policy 層級，非 Statement 內部欄位
4. **Statement 核心三要素**：Effect + Action + Resource
