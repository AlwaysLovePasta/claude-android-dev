---
paths:
  - "**/*Test.kt"
  - "**/*Tests.kt"
---

# 測試模板

## UseCase 測試

```kotlin
class GetPaymentHistoryUseCaseTest {

    private val fakeRepo = FakePaymentRepository()
    private val useCase = GetPaymentHistoryUseCase(fakeRepo)

    @Test
    fun `emits payment list on success`() = runTest {
        fakeRepo.emit(listOf(samplePayment))

        useCase().test {
            val result = awaitItem()
            assertEquals(1, result.size)
            assertEquals(samplePayment.id, result.first().id)
            awaitComplete()
        }
    }

    @Test
    fun `emits empty list when no data`() = runTest {
        fakeRepo.emit(emptyList())

        useCase().test {
            assertEquals(emptyList<Payment>(), awaitItem())
            awaitComplete()
        }
    }

    @Test
    fun `propagates error from repository`() = runTest {
        fakeRepo.emitError(IOException("network failure"))

        useCase().test {
            awaitError()
        }
    }
}
```

---

## ViewModel 測試

```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class PaymentViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private val fakeUseCase = FakeGetPaymentHistoryUseCase()

    @Test
    fun `initial state is Loading then transitions to Success`() = runTest {
        fakeUseCase.result = flowOf(listOf(samplePayment))
        val vm = PaymentViewModel(fakeUseCase)

        vm.uiState.test {
            assertEquals(PaymentUiState.Loading, awaitItem())
            assertEquals(PaymentUiState.Success(listOf(samplePayment)), awaitItem())
        }
    }

    @Test
    fun `emits Error state on failure`() = runTest {
        fakeUseCase.result = flow { throw IOException("fail") }
        val vm = PaymentViewModel(fakeUseCase)

        vm.uiState.test {
            assertEquals(PaymentUiState.Loading, awaitItem())
            assertIs<PaymentUiState.Error>(awaitItem())
        }
    }
}
```

---

## Fake 實作

優先建立 `Fake` 實作，讓測試更穩定、可讀。僅在 Fake 成本過高時才使用 MockK。

```kotlin
class FakePaymentRepository : PaymentRepository {
    private val flow = MutableSharedFlow<List<Payment>>()

    suspend fun emit(data: List<Payment>) = flow.emit(data)
    suspend fun emitError(e: Throwable) { throw e }

    override fun getHistory(): Flow<List<Payment>> = flow
}
```
