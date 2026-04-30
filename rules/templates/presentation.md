---
paths:
  - "**/presentation/**/*.kt"
---

# Presentation 層模板

## ViewModel + UDF

```kotlin
@HiltViewModel
class PaymentViewModel @Inject constructor(
    private val getHistory: GetPaymentHistoryUseCase
) : ViewModel() {

    private val _uiState = MutableStateFlow<PaymentUiState>(PaymentUiState.Loading)
    val uiState: StateFlow<PaymentUiState> = _uiState.asStateFlow()

    init { loadHistory() }

    fun onEvent(event: PaymentUiEvent) = when (event) {
        is PaymentUiEvent.Refresh -> loadHistory()
        is PaymentUiEvent.ItemClicked -> { /* navigate */ }
    }

    private fun loadHistory() {
        viewModelScope.launch {
            getHistory()
                .catch { _uiState.value = PaymentUiState.Error(it.message.orEmpty()) }
                .collect { items ->
                    _uiState.value = if (items.isEmpty()) PaymentUiState.Empty
                    else PaymentUiState.Success(items)
                }
        }
    }
}
```

## UiState / UiEvent

```kotlin
sealed interface PaymentUiState {
    data object Loading : PaymentUiState
    data object Empty : PaymentUiState
    data class Success(val items: List<Payment>) : PaymentUiState
    data class Error(val message: String) : PaymentUiState
}

sealed interface PaymentUiEvent {
    data object Refresh : PaymentUiEvent
    data class ItemClicked(val id: String) : PaymentUiEvent
}
```
