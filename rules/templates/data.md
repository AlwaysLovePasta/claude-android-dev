---
paths:
  - "**/data/**/*.kt"
---

# Data 層模板

## RepositoryImpl

```kotlin
// data/repository/PaymentRepositoryImpl.kt
class PaymentRepositoryImpl @Inject constructor(
    private val remote: PaymentRemoteDataSource,
    private val local: PaymentLocalDataSource
) : PaymentRepository {
    override fun getHistory(): Flow<List<Payment>> =
        local.observeAll().map { entities -> entities.map { it.toDomain() } }
}
```

## Mapper

```kotlin
// data/mapper/PaymentMapper.kt
fun PaymentDto.toDomain(): Payment = Payment(
    id = this.id,
    amount = this.amount,
    date = Instant.parse(this.dateString)
)
```

## Hilt Binding

```kotlin
// data/di/PaymentDataModule.kt
@Module
@InstallIn(SingletonComponent::class)
abstract class PaymentDataModule {
    @Binds
    abstract fun bindRepo(impl: PaymentRepositoryImpl): PaymentRepository
}
```
