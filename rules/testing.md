---
paths:
  - "**/*Test.kt"
  - "**/*Tests.kt"
---

# 測試策略

## 測試分層

| 層級 | 測試類型 | 工具 | 重點 |
|---|---|---|---|
| **Domain** | Unit Test | JUnit 5, MockK | UseCase 邏輯正確性 |
| **Data** | Unit Test | JUnit 5, MockK | Mapper 轉換、Repository 快取策略 |
| **ViewModel** | Unit Test | JUnit 5, Turbine, MockK | StateFlow 發射順序與狀態轉換 |
| **UI** | UI Test | Compose UI Test | 使用者操作流程，不測實作細節 |

---

## 覆蓋要求

- 每個 UseCase 至少覆蓋：**正常路徑、錯誤路徑、邊界條件**。
- 每個 ViewModel 至少驗證：**初始狀態、載入成功、載入失敗**的 StateFlow 發射。
- Mapper 須驗證所有欄位轉換正確性，包含 nullable 欄位的預設值處理。

---

## 原則

- **Fake 優先於 Mock**：優先建立 `Fake` 實作，讓測試更穩定、可讀。僅在 Fake 成本過高時才使用 MockK。
- **不測實作細節**：UI Test 只驗證使用者可觀察的行為，不對內部狀態斷言。

UseCase / ViewModel / Fake 的測試模板詳見 `rules/templates/testing.md`。
