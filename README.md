# Android Dev — Claude Profile

這是一套專為 Android 工程師設計的 Claude Code 設置組合，讓 Claude 遵循 Modern Android Development (MAD) 最佳實踐，提供具備架構判斷力的 AI 協作。

---

## 目錄結構

```
android-dev/
├── CLAUDE.md                          # 主要設定：Tech Stack、設計原則、回應規則
├── rules/
│   ├── architecture.md                # Clean Architecture × 多模組依賴規則
│   ├── coding.md                      # Kotlin 程式慣例（命名、Null Safety、不可變性）
│   ├── solid.md                       # SOLID 原則 Kotlin 範例
│   ├── testing.md                     # 測試策略與 UseCase/ViewModel 測試模板
│   ├── workflow.md                    # 任務分級與 Architect / Practitioner Mode
│   └── templates/
│       ├── domain.md                  # Domain 層程式碼模板
│       ├── data.md                    # Data 層程式碼模板
│       ├── presentation.md            # Presentation 層程式碼模板
│       └── testing.md                 # 測試模板
└── skills/
    ├── android-cli/                   # android CLI 工具操作指引
    └── jetpack-compose-audit/         # Jetpack Compose 品質審查技能
```

---

## Tech Stack

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

## 安裝

將此目錄複製（或 symlink）至 `~/.claude/` 下，使規則全域生效：

```bash
# 複製
cp -r android-dev ~/.claude/

# 或 symlink（推薦，方便追蹤版本）
ln -s /path/to/android-dev ~/.claude/android-dev
```

如需限定特定專案生效，將 `CLAUDE.md` 與 `rules/` 複製至專案根目錄的 `.claude/` 下。

---

## 規則說明

### `rules/architecture.md`

定義 Clean Architecture 三層（Presentation / Domain / Data）的依賴方向，以及對應的多模組拓撲：

```
:app → :feature:* → :core:domain ← :core:data
                  → :core:ui
                  → :core:common
```

包含 Feature 模組的完整目錄結構範本。

### `rules/coding.md`

Kotlin 程式慣例（`paths: **/*.kt, **/*.kts` 自動載入）：

- 元件命名表（UseCase / Repository / ViewModel / UiState / UiEvent）
- 禁止 `!!`、禁止在 ViewModel 中吞掉例外
- `val` 優先、`internal`/`private` 預設可見性
- 副作用隔離規則

### `rules/solid.md`

五大原則的 Kotlin 範例，含 ✅ 正確示範與 ❌ 反模式。

### `rules/testing.md`

測試分層（Domain Unit → Data Unit → ViewModel Unit → Compose UI），包含：

- UseCase 測試模板（正常路徑、錯誤路徑、邊界條件）
- ViewModel 測試模板（StateFlow 發射驗證）
- Fake 優先於 Mock 的理由與範例

### `rules/workflow.md`

依**業務重要性 / 技術耦合度 / 領域複雜度**三維評估任務，對應兩種模式：

| 模式 | 觸發條件 | 行為 |
|---|---|---|
| **Architect Mode** | 任一維度為「高」 | 釐清需求 → 架構提案（含 trade-offs）→ 技術債標示 → 實作指引 |
| **Practitioner Mode** | 全部為「低」 | 確認目標 → 直接給出完整方案 → 簡要說明 |

---

## Skills

### `android-cli`

> **前置需求**：此 skill 依賴 `android` CLI 工具，使用前須先安裝。
> 安裝方式請參考官方文件：[Android CLI for AI agents](https://developer.android.com/tools/agents/android-cli)

操作 `android` CLI 工具的完整指引，涵蓋：

- **SDK 管理**：`android sdk install / update / remove / list`
- **專案建立**：`android create <template> --name --output`
- **裝置互動**：部署、截圖、Layout Inspection
- **模擬器管理**：`android emulator create / start / stop / list`
- **文件搜尋**：`android docs search` — 查詢 Android 官方知識庫

觸發方式：使用者要求操作 Android 裝置、建立專案、管理 SDK，或詢問 `android` CLI 指令。

### `jetpack-compose-audit`

> 來源：https://github.com/hamen/compose_skill

對 Compose 程式庫進行嚴格的品質審查，產出評分報告（`COMPOSE-AUDIT-REPORT.md`）。

**審查四大類別**（各 0–10 分）：

| 類別 | 重點 |
|---|---|
| Performance | 組合期昂貴運算、可跳過率、Lazy list key |
| State Management | 狀態提升正確性、單一事實來源 |
| Side Effects | Effect API 選擇、effect key、stale lambda |
| Composable API Quality | `modifier` 位置、參數順序、可重用性 |

**關鍵特性**：
- 自動執行 Compose Compiler Report（透過 `--init-script` 注入，不修改任何專案檔案）
- 每個扣分項目必須附上官方文件 URL
- 有量化天花板機制：`skippable%` 直接限制 Performance 分數上限

觸發方式：「audit this Compose repo」、「score this codebase」、「review state hoisting」等。

---

## 回應語言與格式

- 回應使用**繁體中文（台灣用語）**；程式碼、變數名稱、專有名詞保留英文。
- 涉及規範時引用對應規則檔（如「依 `architecture.md` §2」）。
- 效能或敏感資料場景主動提出風險建議。
