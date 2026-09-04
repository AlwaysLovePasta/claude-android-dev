# Android Dev — Claude Profile

這是一套專為 Android 工程師設計的 Claude Code 設置組合，讓 Claude 遵循 Modern Android Development (MAD) 最佳實踐，提供具備架構判斷力的 AI 協作。

---

## 目錄結構

```
claude-android-dev/
├── CLAUDE.md         # 主要設定：Tech Stack、設計原則、回應規則
├── detect.json        # 專案類型偵測條件（供 hook 泛化偵測使用）
├── rules/              # 各主題規則，依 paths: frontmatter 按需載入
└── skills/             # 可呼叫的工作流（android-cli、jetpack-compose-audit）
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

## 使用方式

這個 repo 是 [`claude-global-configuration`](https://github.com/AlwaysLovePasta/claude-global-configuration) 的 submodule（`profiles/android`）。實際生效流程：

1. Global config 的 `SessionStart` hook 讀取 `detect.json`，偵測目前專案是否為 Android 專案
2. 命中後，把這裡的 `CLAUDE.md`／`rules/*.md`／`skills/*` symlink 進**當前專案**的 `.claude/` 目錄
3. Claude Code 依原生機制決定何時載入：`rules/*.md` 靠 `paths:` frontmatter 依檔案類型觸發，`skills/*` 靠 description 比對

不需要手動複製或 symlink 這個目錄；只要裝好 `claude-global-configuration` 就會自動生效。

---

## 規則一覽

| 檔案 | 內容 |
|---|---|
| `rules/architecture.md` | Clean Architecture 三層與多模組依賴規則 |
| `rules/coding.md` | Kotlin 程式慣例（命名、Null Safety、不可變性） |
| `rules/solid.md` | SOLID 五大原則的 Kotlin 範例 |
| `rules/testing.md` | 測試分層策略與模板 |
| `rules/workflow.md` | 任務分級，判斷 Architect / Practitioner Mode |
| `rules/templates/*.md` | 供上述規則引用的詳細程式碼範例 |

## Skills 一覽

| Skill | 用途 |
|---|---|
| `android-cli` | 操作 `android` CLI（SDK 管理、建立專案、裝置互動、模擬器）。需先安裝該 CLI，見 [官方文件](https://developer.android.com/tools/agents/android-cli) |
| `jetpack-compose-audit` | 對 Compose 程式碼做量化品質審查，產出評分報告。來源：https://github.com/hamen/compose_skill |

---

## 回應語言與格式

- 回應使用**繁體中文（台灣用語）**；程式碼、變數名稱、專有名詞保留英文。
- 涉及規範時引用對應規則檔（如「依 `architecture.md` §2」）。
- 效能或敏感資料場景主動提出風險建議。
