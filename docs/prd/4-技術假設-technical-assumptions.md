# 4. 技術假設 (Technical Assumptions)

## 儲存庫結構 (Repository Structure): Monorepo
* **理由**: 採用 Monorepo（單一儲存庫）結構能為未來擴展（如增加雲端同步服務、Web 版或行動版應用）提供極大的便利性，簡化依賴管理並促進程式碼共享。

## 服務架構 (Service Architecture): Monolith (單體應用)
* **理由**: 對於 MVP 階段的桌面應用程式而言，採用單體架構是最直接、最高效的方式，完全符合「輕量級」和「本地優先」的產品定位。

## 測試需求 (Testing Requirements): Unit + Integration (單元測試 + 整合測試)
* **理由**: 這是確保 MVP 品質與穩定性的最佳平衡點。**單元測試**確保每個獨立功能的正確性，**整合測試**則驗證核心工作流程中不同部分之間的協作是否順暢。

## 其他技術假設與請求 (Additional Technical Assumptions and Requests)
* **應用程式框架**: **Tauri**。理由：基於其高效能、高安全性（Rust 後端）和輕量級安裝包的優勢，完美符合跨平台桌面應用的需求。
* **前端框架**: **React (使用 Vite)**。理由：React 擁有龐大的生態系統和社群支援，而 Vite 則提供了極速的開發體驗。
* **本地資料庫**: **SQLite**。理由：作為一個輕量級的無伺服器資料庫，非常適合嵌入桌面應用程式中，實現快速、可靠的本地資料儲存。

---
