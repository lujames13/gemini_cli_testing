# 5. 原始碼樹狀結構 (Source Tree)

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
