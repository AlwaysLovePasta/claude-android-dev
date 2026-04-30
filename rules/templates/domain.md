---
paths:
  - "**/domain/**/*.kt"
---

# Domain 層模板

## UseCase

```kotlin
class GetPaymentHistoryUseCase @Inject constructor(
    private val repo: PaymentRepository
) {
    operator fun invoke(): Flow<List<Payment>> = repo.getHistory()
}
```

## Repository Interface

```kotlin
// domain/repository/PaymentRepository.kt
interface PaymentRepository {
    fun getHistory(): Flow<List<Payment>>
}
```
