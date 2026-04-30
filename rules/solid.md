---
paths:
  - "**/*.kt"
  - "**/*.kts"
---

# SOLID 原則

## S — Single Responsibility Principle

一個 class 只對一個變更理由負責。ViewModel、UseCase、Repository 各自只處理自己層的職責，不跨層兼任。

常見違反：ViewModel 直接持有 DataSource 或 API，或同時負責資料取得與格式化。

```kotlin
// ❌ ViewModel 越層存取 DataSource，且兼任格式化
class PaymentViewModel(
    private val api: PaymentApi,
    private val formatter: CurrencyFormatter
) : ViewModel() { /* 同時負責資料取得與顯示格式 */ }
```

---

## O — Open/Closed Principle

對擴展開放，對修改關閉。以 `sealed interface` 建模狀態與事件，新增變體只需加一個子型別，不改動既有的處理邏輯。

```kotlin
// ✅ 新增狀態只需加一行，不改動既有邏輯
sealed interface PaymentUiState {
    data object Loading : PaymentUiState
    data class Success(val items: List<Payment>) : PaymentUiState
    data class Error(val message: String) : PaymentUiState
    data object Empty : PaymentUiState          // ← 新增擴展
}
```

---

## L — Liskov Substitution Principle

子型別必須能完全替代父型別，不改變程式正確性。Repository 實作須完全遵守 interface 定義的契約，包含回傳型別語意。

常見違反：實作類別拋出 interface 未定義的例外，而非以 `Result.failure` 回傳。

```kotlin
// ❌ 違反契約：拋出介面未定義的例外
class BrokenRepository : PaymentRepository {
    override suspend fun getById(id: String): Result<Payment> {
        if (id.isBlank()) throw IllegalStateException("blank id")  // 應回傳 Result.failure
        // ...
    }
}
```

---

## I — Interface Segregation Principle

介面按功能拆分，不強迫消費者依賴不需要的方法。唯讀的 ViewModel 不應被迫依賴含有寫入、刪除方法的胖介面。

常見違反：單一 Repository interface 同時包含查詢、寫入、刪除、匯出，導致只需讀取的消費端也必須依賴全部方法。

```kotlin
// ❌ 胖介面：查詢場景被迫依賴 delete、export
interface PaymentRepository {
    fun observeHistory(): Flow<List<Payment>>
    suspend fun submit(payment: Payment): Result<Unit>
    suspend fun delete(id: String): Result<Unit>
    suspend fun export(): Result<File>
}
```

---

## D — Dependency Inversion Principle

高階模組不依賴低階模組的具體實作，兩者都依賴抽象。Presentation 層只能透過 Domain 層的 interface 存取資料，絕不直接引用 Data 層的具體類別。

常見違反：ViewModel 的建構子直接注入 `PaymentRepositoryImpl` 而非 `PaymentRepository`，或 UseCase 直接持有 `RetrofitApi` 實例。

正確的 Repository interface / Impl / Hilt Binding 寫法詳見 `rules/templates/data.md`。
