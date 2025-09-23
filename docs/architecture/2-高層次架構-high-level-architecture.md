# 2. 高層次架構 (High Level Architecture)

## 2.1. 技術摘要 (Technical Summary)

DevFocus 採用基於 **Tauri** 的跨平台桌面應用架構，實現了高效能與輕量級的目標。前端使用 **React (Vite)** 建立現代化且反應迅速的使用者介面，而後端核心邏輯則由 **Rust** 提供支持，確保了系統的安全與穩定性。所有使用者資料在 MVP 階段將儲存於本地 **SQLite** 資料庫，透過清晰的資料倉儲模式 (Repository Pattern) 進行存取，為未來整合雲端同步服務預留了擴充介面。

## 2.2. 高層次概覽 (High Level Overview)

- **架構風格**: 採用 **Monolith** (單體應用) 架構，所有核心功能都封裝在單一的桌面應用程式中，符合 MVP 階段的需求。

- **儲存庫結構**: 採用 **Monorepo** (單一儲存庫)，未來可輕鬆擴展共用程式碼庫，例如 shared-types 或 cloud-sync-service。

- **核心流程**: 使用者透過 React 前端介面管理任務並啟動番茄鐘。Tauri 的 Rust 核心負責處理計時器邏輯、與本地 SQLite 資料庫的互動，並透過 Events 和 Commands 與前端進行通訊。

## 2.3. 高層次專案圖 (High Level Project Diagram)

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

## 2.4. 架構與設計模式 (Architectural and Design Patterns)

- **倉儲模式 (Repository Pattern)**: 抽象化資料存取邏輯。建立一個明確的資料層，初期實作對 SQLite 的操作，未來可輕易增加另一個實現（如 **CloudSyncRepository**），而無需更改上層的業務邏輯。

- **命令與事件驅動 (Command & Event-Driven)**: 前端透過 Tauri 的 **invoke** (Commands) 呼叫 Rust 後端的功能。後端則透過 **emit** (Events) 將狀態變更或通知傳遞給前端，實現前後端的解耦。

- **單體優先 (Monolith-First)**: MVP 階段採用單體架構，以降低複雜度並加速開發。但透過模組化的程式碼結構，為未來可能的微服務化或功能拆分預作準備。
