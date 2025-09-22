# DevFocus 架構文件

**版本**: 1.0  
**日期**: 2025-09-22  
**作者**: Winston (Architect)

## 1. 介紹 (Introduction)

本文件闡述了 DevFocus 專案的整體架構，涵蓋後端系統、共享服務以及非 UI 的特定議題。其主要目標是作為 AI 驅動開發的指導性架構藍圖，確保所選模式與技術的一致性。

### 1.1. 啟動模板或現有專案 (Starter Template or Existing Project)

N/A - 這是一個全新的 Greenfield 專案。我們將從頭開始建立，以完全掌控專案結構與依賴。

### 1.2. 變更日誌 (Change Log)

| 日期 | 版本 | 描述 | 作者 |
|------|------|------|------|
| 2025-09-22 | 1.0 | 根據 PRD v1.0 建立第一版架構 | Winston |

## 2. 高層次架構 (High Level Architecture)

### 2.1. 技術摘要 (Technical Summary)

DevFocus 採用基於 **Tauri** 的跨平台桌面應用架構，實現了高效能與輕量級的目標。前端使用 **React (Vite)** 建立現代化且反應迅速的使用者介面，而後端核心邏輯則由 **Rust** 提供支持，確保了系統的安全與穩定性。所有使用者資料在 MVP 階段將儲存於本地 **SQLite** 資料庫，透過清晰的資料倉儲模式 (Repository Pattern) 進行存取，為未來整合雲端同步服務預留了擴充介面。

### 2.2. 高層次概覽 (High Level Overview)

- **架構風格**: 採用 **Monolith** (單體應用) 架構，所有核心功能都封裝在單一的桌面應用程式中，符合 MVP 階段的需求。

- **儲存庫結構**: 採用 **Monorepo** (單一儲存庫)，未來可輕鬆擴展共用程式碼庫，例如 shared-types 或 cloud-sync-service。

- **核心流程**: 使用者透過 React 前端介面管理任務並啟動番茄鐘。Tauri 的 Rust 核心負責處理計時器邏輯、與本地 SQLite 資料庫的互動，並透過 Events 和 Commands 與前端進行通訊。

### 2.3. 高層次專案圖 (High Level Project Diagram)

```mermaid
graph TD
    subgraph User Interface (WebView)
        A[React UI Components]
        B[State Management]
        C[API/Command Layer]
    end

    subgraph Tauri Core (Rust Backend)
        D[Tauri Runtime]
        E[Application Logic]
        F[Database Module (SQLite)]
        G[Event Bus]
    end

    H[User] --> A
    A --> B
    B --> C
    C -- Commands --> D
    D -- Events --> G
    G --> C
    D --> E
    E --> F
```

### 2.4. 架構與設計模式 (Architectural and Design Patterns)

- **倉儲模式 (Repository Pattern)**: 抽象化資料存取邏輯。建立一個明確的資料層，初期實作對 SQLite 的操作，未來可輕易增加另一個實現（如 **CloudSyncRepository**），而無需更改上層的業務邏輯。

- **命令與事件驅動 (Command & Event-Driven)**: 前端透過 Tauri 的 **invoke** (Commands) 呼叫 Rust 後端的功能。後端則透過 **emit** (Events) 將狀態變更或通知傳遞給前端，實現前後端的解耦。

- **單體優先 (Monolith-First)**: MVP 階段採用單體架構，以降低複雜度並加速開發。但透過模組化的程式碼結構，為未來可能的微服務化或功能拆分預作準備。

## 3. 技術棧 (Tech Stack)

此技術棧是基於 PRD 的「技術假設」章節所做的最終決定，是整個專案的單一事實來源。

| 分類 | 技術 | 版本 | 用途 | 理由 |
|------|------|------|------|------|
| 應用框架 | Tauri | ~1.6 | 核心跨平台桌面應用框架 | 高效能、安全 (Rust)、輕量級安裝包 |
| 前端框架 | React | ~18.2 | 使用者介面開發 | 龐大的生態系統與社群支援 |
| 前端建置工具 | Vite | ~5.0 | 開發伺服器與打包工具 | 提供極速的開發體驗 |
| 後端語言 | Rust | ~1.79 | Tauri 核心與業務邏輯 | 記憶體安全、高效能，是 Tauri 的基礎 |
| 本地資料庫 | SQLite | ~3.45 | 本地資料持久化 | 輕量、無伺服器、易於嵌入桌面應用 |
| 資料庫互動 | rusqlite / sqlx | latest | Rust 的 SQLite 驅動程式 | 在 Rust 中提供安全、高效的 SQLite 操作介面 |
| 儲存庫結構 | Monorepo (npm workspaces) | - | 統一管理前後端程式碼 | 簡化依賴管理，促進程式碼共享 |
| 測試框架 | Vitest, Testing Library | latest | 單元與整合測試 | 與 Vite 生態無縫整合，提供全面的測試能力 |

## 4. 資料模型 (Data Models)

### 4.1. Task (任務)

- **用途**: 代表使用者建立的單一待辦事項。
- **關鍵屬性**:
  - `id`: Integer - 主鍵
  - `title`: String - 任務標題
  - `status`: String - 狀態 (pending, completed)
  - `created_at`: Timestamp - 建立時間
- **關聯**: 一個任務可以有多個 TimeLog。

### 4.2. TimeLog (時間紀錄)

- **用途**: 代表一個已完成的番茄鐘專注時間段。
- **關鍵屬性**:
  - `id`: Integer - 主鍵
  - `task_id`: Integer - 外鍵，關聯至 Task
  - `duration_minutes`: Integer - 持續時間（分鐘）
  - `completed_at`: Timestamp - 完成時間
- **關聯**: 每個 TimeLog 屬於一個 Task。

## 5. 原始碼樹狀結構 (Source Tree)

基於 Monorepo 結構，專案的資料夾組織如下：

```
devfocus-monorepo/
├── apps/
│   └── desktop/
│       ├── src/          # React 前端源碼
│       │   ├── components/
│       │   ├── pages/
│       │   ├── hooks/
│       │   └── main.tsx
│       ├── src-tauri/    # Tauri Rust 後端源碼
│       │   ├── src/
│       │   │   ├── main.rs
│       │   │   ├── commands.rs
│       │   │   ├── state.rs
│       │   │   └── db.rs
│       │   ├── build.rs
│       │   └── tauri.conf.json
│       └── package.json
├── packages/
│   └── shared-types/     # (可選) 用於前後端共享的 TypeScript 類型
├── docs/
│   ├── prd.md
│   └── architecture.md
└── package.json          # 根目錄
```

## 6. 基礎設施與部署 (Infrastructure and Deployment)

在 MVP 階段，DevFocus 是一個純本地的桌面應用，不涉及複雜的雲端基礎設施。

- **部署策略**: 透過 Tauri 的 build 命令，為 Windows (.msi)、macOS (.dmg) 和 Linux (.AppImage, .deb) 產生對應平台的安裝包。

- **CI/CD**: 設定 GitHub Actions，在程式碼推送到 main 分支時，自動為三個目標平台建置應用程式並產生草稿發行版 (Draft Release)。

## 7. Post-MVP 擴展性考量：雲端同步

為了支援未來的雲端同步功能，架構上已預留以下擴充點：

**模組化的資料層**: `db.rs` 模組透過**倉儲模式**進行設計。目前的 `LocalRepository` 直接與 SQLite 互動。未來可以新增一個 `CloudRepository`，它負責與遠端 API 進行通訊。應用程式的上層邏輯將透過一個通用的 Repository Trait (介面) 進行互動，從而無縫切換或組合本地與雲端資料。

**狀態同步邏輯**: 新增一個同步引擎 (Sync Engine)，負責比較本地與遠端資料的差異，並處理數據合併與衝突解決。這個引擎將在背景運行，定期與雲端服務進行通訊。

**API 設計**: 未來的雲端服務將提供 RESTful 或 GraphQL API，用於使用者認證、資料上傳與下載。API 設計將與本地的資料模型保持一致。
