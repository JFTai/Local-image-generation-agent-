# 本地生圖 Agent｜目前狀態

**更新：2026-11-11（台灣時間）**。長期方向與每階段驗收門檻見 [八階段正式藍圖](ROADMAP_8_STAGES.md)；GitHub Issue 才是 Codex 可執行的工作授權，本文不是本機指令。

## 一、總目標與目前階段

最終目標：劇情模擬器 LLM／Open WebUI 提出需求 → Hermes 生圖 Agent 選正確素材及既有工作流 → ComfyUI 生成 → 自動審圖 → 必要時有限次修正／重生 → 回傳 Open WebUI 及模擬器 LLM。

| 八階段 | 進度 | 判定範圍 |
| --- | --- | --- |
| 1 原生文生圖閉環 | **基礎 PASS** | P01 新圖與官方 comfy-cli 同回合成功；不代表全自主交付 |
| 2 人物、場景與參考素材管理 | **PARTIAL，現在進行** | A/B 來源分類與跨 session output 來源鏈仍有缺口 |
| 3 自主選擇及操作多種生圖工作流 | 尚未正式驗收 | 不先行提交 GPU |
| 4 多角色與跨場景一致性 | 尚未正式驗收 | 依賴可信來源與工作流能力 |
| 5 自動審圖與必要時重新生成 | 尚未正式驗收 | 後續比較真正具視覺能力的 VLM 及可靠推論模型 |
| 6 正式連接劇情模擬器 LLM | 尚未正式驗收 | 必須由劇情 LLM 自主委派，不只是人工在 WebUI 生圖 |
| 7 長時間自主運作與故障恢復 | 尚未正式驗收 | 基礎安全／8188 保護為全程共通 |
| 8 完整模擬器情境測試與正式完成 | 尚未開始 | 須模擬器端到端證據與使用者確認 |

## 二、已驗證成果與未完成缺口

- [#3 圖片交付](https://github.com/JFTai/Local-image-generation-agent-/issues/3)：兩張已存在 PNG 在已登入 Open WebUI 的真實 DOM 顯示、HTTP 200、SHA 一致；仍需一次 Markdown 格式提醒，**完全自主交付 PARTIAL**。原 2/2 次驗收額度已用盡，`codex-review`。
- [#4 Hermes 工具診斷](https://github.com/JFTai/Local-image-generation-agent-/issues/4)：唯讀診斷 PASS、`codex-done` 已結案。
- [#5 新 session 跨對話來源找回](https://github.com/JFTai/Local-image-generation-agent-/issues/5)：找到工作流、prompt_id／history／PNG，但原附件及工作副本分類不準；**PARTIAL**、2/2 次用盡、`codex-review`。
- [#6 四類來源與路徑](https://github.com/JFTai/Local-image-generation-agent-/issues/6)：**引導式** 12/12 原附件／工作副本／ComfyUI input 的實體 SHA 核對；兩筆生成 output 來源鏈 **0/2**，原附件出處有錯，terminal 14 次超出提示上限 8 次，WebUI 停止未取得 Gateway cancelled 證據；**PARTIAL**、1/1 session 用盡、`codex-review`。
- [#8 官方控制面／Context Files 核查](https://github.com/JFTai/Local-image-generation-agent-/issues/8)：本機同版來源、A/B output 的 Codex 獨立核對已有證據；但當次 Hermes Gateway 8642 無 listener，無法驗證真正運行的 profile／CWD／capabilities。官方 stop 需確認最終 `cancelled`，沒有已證明的 terminal 硬性總呼叫上限。**PARTIAL，`codex-review`**；無新 Hermes session、GPU 或自訂程式。
- [#9 Gateway Runtime 預檢](https://github.com/JFTai/Local-image-generation-agent-/issues/9)：既有隔離環境以官方方式啟動 Gateway **1 次**，在可連 localhost 環境 `/health`、`/v1/health` 均 HTTP 200；預設沙箱仍 HTTP 000，不代表服務停機。**健康子項 PASS**；`/v1/capabilities` 回 401／`gateway_auth_failed`，缺已授權本機 client，控制能力與硬工具上限仍未實測。整體 **PARTIAL，`codex-review`**；無新 Hermes session／模型推論／GPU／自訂程式。已見 [ChatGPT 審查](https://github.com/JFTai/Local-image-generation-agent-/issues/9#issuecomment-6789768636)。
- [#10 官方認證 capabilities 預檢](https://github.com/JFTai/Local-image-generation-agent-/issues/10)：Codex 原生查核 Gateway loopback listener／TCP 可達，但沒有符合本次嚴格授權的既存 capabilities-only 官方認證 client；**GET 0/1，BLOCKED，`codex-blocked`**。沒有新 HTTP API、Hermes session、推論、GPU、ComfyUI 提交或服務變更；同版官方原始碼宣告 run/status/SSE/stop 不等於運行中 API 已認證驗證。[ChatGPT 正式審查](https://github.com/JFTai/Local-image-generation-agent-/issues/10#issuecomment-6799557510)。
- 以上成功不等於第二階段完整 PASS，也不等於「生成→VLM 自動審圖→必要修正／重生→模擬器回傳」已完成.

## 三、當前交接與授權門檻

**目前無可安全接手的 `codex-ready`／`codex-in-progress` 任務。** [#10](https://github.com/JFTai/Local-image-generation-agent-/issues/10) 已由 Codex 接手並回報 **BLOCKED，`codex-blocked`**：使用者已授權使用本機**已配置的官方認證方式**進行一次 `GET /v1/capabilities`，但沒有找到能在不接觸憑證、不建立新 client／程式的情況下，僅發出該單一 GET 的現有官方 client。**實際 GET 0/1，授權未消耗但也不可自行放寬**；目前只有 listener／TCP 可達證據，沒有本輪已認證 API 回應。

已核對 Hermes v0.22.5 官方 API Server 與 peer CLI：靜態來源列有 `run_status`／`run_events_sse`／`run_stop`，但 `peer run` 會接著提交新推論，不能作為安全 capabilities-only 查詢。**下一步需要使用者提供既有安全官方 client 的名稱與操作方式；若沒有，需另行批准一項明確的新認證操作方案**。不得要求使用者貼出金鑰，也不得自行讀取／拼裝 Bearer header 或擴大權限。無安全入口就維持 BLOCKED，不建立重複的 `codex-ready`。

[#9](https://github.com/JFTai/Local-image-generation-agent-/issues/9) 仍為 PARTIAL／`codex-review`；[#8](https://github.com/JFTai/Local-image-generation-agent-/issues/8) 仍為 PARTIAL／`codex-review`；[#7](https://github.com/JFTai/Local-image-generation-agent-/issues/7) 已關閉 duplicate。**階段 2 仍 PARTIAL**。即使日後 capabilities 回 HTTP 200，也不代表硬性 terminal 工具呼叫上限或真正安全停止已驗證，不能因此擅自啟動新的 Hermes 推論 session、GPU 或 ComfyUI 提交。

## 四、交接、環境與安全

- GitHub Issue 讀寫與本機 Codex 接手交接已在歷次 #1/#2/#4/#5/#6 回合核對；Codex 已在 #9 留 PARTIAL 回報，#10 留 BLOCKED 回報，ChatGPT 已分別審查；目前沒有 ready/in-progress 任務，不能宣稱已安排下一件 Codex 執行工作。
- ChatGPT 每小時檢查更新與審查後續；`codex-ready` 才是本機 Codex 排程可接手入口。監控不代表任務自動完成，回報須附實際 Issue/commit。
- **禁止佔用使用者標準 127.0.0.1:8188 的受限白名單 ComfyUI**。不得擅自停止日常 ComfyUI、變更全域設定、讀取 Open WebUI 帳密／憑證、公開私人圖片／本機路徑。
- 後續所有 GPU、生圖、模型／VLM 替換、安裝、修改工作流及自訂程式，都需獨立明確授權；按 [PROTOCOL.md](PROTOCOL.md) 控制任務權限。
- 本倉庫**僅存公開去敏交接資料**，不放原始圖片、私人參考、工作流完整本機資產、虛擬環境或原始執行日誌。

## 五、下一階段決策

**#10 已完成審查且因認證 client 缺口 BLOCKED**；不再重複派相同 GET 測試。使用者的原授權仍限「既有官方認證方式、一次 GET、不讀取／顯示／上傳金鑰、不改設定、不模型推論／GPU」。若沒有符合條件的現成 client，需等待使用者另行批准精確不同方案，不能自行建立新 client、讀取秘密或執行 `peer run`。官方原始碼的靜態 capabilities 與歷史 `cancelled` 只能當設計／歷史證據，不可冒充本輪 Gateway 實測。

完成安全的官方認證控制面及真正停止保障後，才可另案安排具有新變因、明確上限的階段 2 A/B 生成 output 來源鏈自主驗證。任何新專案指引、Gateway 設定、自訂程式或 GPU 操作均需個別授權。**不得為了維持 codex-ready 而重複派工。**
