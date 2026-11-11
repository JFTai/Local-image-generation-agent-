# Local image generation agent

本倉庫作為 ChatGPT 與本機 Codex 的任務及進度交接入口。它不是本機執行環境的完整備份。

- [八階段開發藍圖](coordination/ROADMAP_8_STAGES.md)
- [八階段開發藍圖與驗收門檻](coordination/ROADMAP_8_STAGES.md)
- [協作規則](coordination/PROTOCOL.md)
- [目前狀態](coordination/STATUS.md)
- [建立任務](https://github.com/JFTai/Local-image-generation-agent-/issues/new/choose)

預期流程：ChatGPT 透過已授權的 GitHub 寫入工具建立 Issue → 本機 Codex 接收任務 → Codex 在同一 Issue 回報 → ChatGPT 讀取回報。

**僅設定 Git 遠端不會啟用自動通訊。** GitHub 寫入授權、ChatGPT 端的寫入能力，以及本機 Codex 的官方排程／觸發方式都必須分別驗證。實際啟用狀態以 STATUS.md 為準；當前只執行有 `codex-ready` 標籤的明確 Issue，路線圖本身不是 GPU 或安裝授權。

本倉庫不應包含憑證、`.env`、本機資料庫、私人圖片、模型、虛擬環境、原始聊天或大量執行日誌。需要公開的證據應先整理成不含機密的摘要。
