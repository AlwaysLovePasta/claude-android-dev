---
paths:
  - "**/*.kt"
  - "**/*.kts"
---

# Kotlin 程式慣例

## 命名規則

| 元件 | 格式 | 範例 |
|---|---|---|
| UseCase | `動詞 + 名詞 + UseCase` | `GetPaymentHistoryUseCase` |
| Repository | `名詞 + Repository` | `PaymentRepository` |
| ViewModel | `功能名 + ViewModel` | `PaymentViewModel` |
| UI State | `功能名 + UiState` | `PaymentUiState` |
| UI Event | `功能名 + UiEvent` | `PaymentUiEvent` |

命名必須自解釋，禁止縮寫（`idx` → `index`、`btn` → `button`）。函式長度以 **20 行內**為目標，超過即考慮拆分。

---

## 結構規範

- ViewModel 對外只暴露 `StateFlow<UiState>` 與 `fun onEvent(UiEvent)`。
- UseCase 以 `operator fun invoke()` 為唯一公開方法。
- Mapper 為獨立 extension function（如 `PaymentDto.toEntity()`），放在 Data 層。

---

## 錯誤處理

- Domain 層以 `Result<T>` 或自定義 `sealed interface` 封裝成功與失敗。
- 禁止在 ViewModel 中以 `try-catch` 吞掉例外——所有錯誤必須轉為 `UiState` 呈現。

---

## Kotlin 慣用風格

- 優先使用 `data class`、`sealed interface`、`extension function`、`scope function`（`let`, `run`, `apply` 等）。
- 避免 Java-style 寫法（手動 getter/setter、`static` 工具類別、`object` 濫用）。

## Immutability

- 預設使用 `val` 與不可變集合（`List`, `Map`）。
- 僅在有明確理由時才使用 `var` 或 `MutableList`，並在同一處加上說明注釋。

## Null Safety

- 禁止使用 `!!`。
- 以 `?.let {}`、`?:` (Elvis)、`requireNotNull()` 處理 nullable。
- 優先從設計上消除 null（以 `sealed interface` 建模缺失狀態）。

## Scope 最小化

- Class 與函式預設 `internal` 或 `private`。
- 僅在模組邊界確實需要時才標記 `public`，且需有明確理由。

## 副作用隔離

- 純邏輯函式不得包含 I/O 或狀態修改。
- 副作用集中在 Repository / DataSource / ViewModel 的特定進入點，不得散落在 UseCase 或 Mapper 中。
