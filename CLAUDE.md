# CLAUDE.md — Android Project Configuration

> **適用範圍**: 全專案 AI 輔助開發的行為規範與架構準則
> **基準**: Modern Android Development (MAD)
> **規則文件**: `.claude/rules/` 下的文件會自動載入，無需手動引用

---

## 1. Tech Stack

| 項目 | 規格 |
|---|---|
| Language | Kotlin |
| UI | Jetpack Compose + Material 3 |
| Async | Kotlin Coroutines + Flow |
| Architecture | Clean Architecture + MVVM + UDF |
| DI | Hilt |
| Navigation | Navigation Compose (Type-Safe) |
| Networking | Retrofit + Kotlin Serialization |
| Local Storage | Room + DataStore |
| Build | Gradle KTS + Version Catalog + Convention Plugins |
| Testing | JUnit 5, Turbine, MockK, Compose UI Test |
| Module Strategy | Multi-Module (Day 1) |

---

## 2. Architecture Overview

本專案從第一行程式碼起即採用**多模組 + Clean Architecture**。

```
# Dependency
Presentation ─→ Domain ←─ Data
```

- **Domain**：純 Kotlin。Entity、UseCase、Repository Interface。零框架依賴。
- **Data**：實作 Repository、DataSource、DTO ↔ Entity Mapper。
- **Presentation**：ViewModel、UiState / UiEvent、Compose Screen。

詳見 `.claude/rules/architecture.md`。

---

## 3. Design Principles

- **SOLID**（必須遵守）：所有 class 與 interface 須符合。詳見 `.claude/rules/solid.md`。
- **YAGNI**：不為假設需求預寫程式碼，技術決策須對應當前需求。
- **DDD（輕量應用）**：以業務領域命名 module 與 package（如 `feature:payment`）。
- **DRY**：重複兩次以上再提取共用邏輯。避免過早抽象。

Kotlin 程式慣例（命名、Null Safety、Immutability 等）詳見 `.claude/rules/coding.md`。

---

## 4. Response Rules

1. **語言**：繁體中文（台灣用語）。程式碼、變數名稱、專有名詞保留英文。
2. **警示**：涉及效能或敏感資料時，主動提出風險與建議。
3. **引用**：涉及規範時引用對應規則檔（如「依 `coding.md` §命名規則」）。
4. **任務模式**：依 `.claude/rules/workflow.md` 判斷當前任務適用 Architect Mode 或 Practitioner Mode。
