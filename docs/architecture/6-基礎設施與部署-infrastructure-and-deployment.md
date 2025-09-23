# 6. 基礎設施與部署 (Infrastructure and Deployment)

在 MVP 階段，DevFocus 是一個純本地的桌面應用，不涉及複雜的雲端基礎設施。

- **部署策略**: 透過 Tauri 的 build 命令，為 Windows (.msi)、macOS (.dmg) 和 Linux (.AppImage, .deb) 產生對應平台的安裝包。

- **CI/CD**: 設定 GitHub Actions，在程式碼推送到 main 分支時，自動為三個目標平台建置應用程式並產生草稿發行版 (Draft Release)。
