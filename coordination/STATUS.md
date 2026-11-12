# 本地生圖 Agent｜目前狀態

**更新：2026-11-12（台灣時間）**。長期方向與每階段驗收門檻見 [八階段正式藍圖](ROADMAP_8_STAGES.md)；GitHub Issue 才是 Codex 可執行的工作授權，本文不是本機指令。

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

## 三、當前主線：Open WebUI → Hermes → ComfyUI → 回圖

**依 2026-11-12 使用者最新明確指示，主線改回實際操作：** Open WebUI 送出自然語言需求 → Hermes Agent 使用既有官方 Skill／comfy-cli 及使用者完整 ComfyUI 工作流生圖 → 在同一 Open WebUI 對話顯示新圖；Codex **測試進行時**觀察 Hermes 真正執行的步驟、在可回退範圍內修正問題，查官方／GitHub／社群實例；ChatGPT 讀取 GitHub 回報並接續測試八階段。

**目前可執行任務：[Issue #11／端到端生圖及回傳實測](https://github.com/JFTai/Local-image-generation-agent-/issues/11)**，已發布 `codex-ready`，待 Codex 留接手證據。這是階段 1 回歸以及進入階段 2 前的**聯調基線**，不是重跑 #3/#5/#6 的舊測試。Issue #11 已獨立列明本輪使用者直接授權的實際生成範圍與防無限重試額度；只代表該 Issue 可執行，不自動授權其他模型或工作流的生成。

[#10](https://github.com/JFTai/Local-image-generation-agent-/issues/10) 的單獨已認證 `GET /v1/capabilities` 是**非主線診斷 BLOCKED**，其原有嚴格授權與 0/1 使用量保持不變；**不再將其列為整體 Open WebUI/Hermes/ComfyUI 工作的強制開工門檻**。診斷用的 `/v1`、health、Sandbox 是既有工具／執行位置概念，不是新架構，也不是必裝或必測的新元件。真正遇到阻礙時就具體處理當前故障，不能因一個無關端點阻止其他測試。

## 四、交接、環境與安全

- GitHub Issue 讀寫與本機 Codex 接手交接已驗證；#9、#10 已完成回報與審查，#11 為新的功能實測 ready。**Codex 未留接手回條前不可宣稱已開始觀察或生圖**；即時觀察需由執行中的本機 Codex 實際做到並回報，GitHub 定時讀取不能冒充毫秒級即時監控。
- ChatGPT 每小時讀取 Codex 回報、審查後續並續派；`codex-ready` 是本機 Codex 交接入口。排程不應因單一診斷 Issue 被 BLOCKED 就停用；只有實質結果與直接證據才能判為完成。
- **禁止佔用使用者標準 127.0.0.1:8188 的受限白名單 ComfyUI**。不得擅自停止日常 ComfyUI、變更全域設定、讀取 Open WebUI 帳密／憑證、公開私人圖片／本機路徑。
- 使用者 2026-11-12 已明確要求進行**實際生圖及回傳測試**；本輪可用 GPU 已界定於 #11 的 P01 流程與提交額度。後續每階段測試應透過具體 Issue 明列內容及適當資源範圍，不能用一般診斷阻礙任務。不得擅自下載／更換模型、安裝套件、增加自訂程式、覆蓋原完整工作流或暴露憑證。
- 本倉庫**僅存公開去敏交接資料**，不放原始圖片、私人參考、工作流完整本機資產、虛擬環境或原始執行日誌。

## 五、下一階段決策

**先執行 #11 真正端到端生圖與前端回傳。** Codex 在現有 Windows 原環境使用 Open WebUI、Hermes Agent、官方 Skill／comfy-cli、原有完整 ComfyUI 工作流；檢查服務是否在線，必要時按原先成功的方式啟動，於測試期間觀察行為。能修復的實際整合錯誤就在可逆、零自訂程式的範圍內修復；不能修則查官方文件及 GitHub／Reddit／技術論壇實例，再不行便報告 BLOCKED/PARTIAL，轉到其他有價值的工作。

驗收須區分 Hermes 自主提交新圖、ComfyUI 執行成功、Hermes 自主回傳、Open WebUI 真實顯示。下一件依八階段規劃**人物／黑湖參考及來源一致性**測試，持續直到模擬器 LLM 主動委派生圖與收圖。與主線無關的 #10 Auth GET 診斷不再是先決條件，也不能以它阻止已明確授權的功能測試。

