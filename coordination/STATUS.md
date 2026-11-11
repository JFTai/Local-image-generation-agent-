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
- 以上成功不等於第二階段完整 PASS，也不等於「生成→VLM 自動審圖→必要修正／重生→模擬器回傳」已完成.

## 三、當前交接與授權門檻

**目前沒有 `codex-ready` 或 `codex-in-progress` 任務**；[#9](https://github.com/JFTai/Local-image-generation-agent-/issues/9) 已回報並審查為 PARTIAL，維持 `codex-review`，**不擴大其原授權**。[#8](https://github.com/JFTai/Local-image-generation-agent-/issues/8) 也維持 PARTIAL；[#7](https://github.com/JFTai/Local-image-generation-agent-/issues/7) 為已關閉 duplicate，不重新派工。

已確認的最小缺口是：Gateway 健康可達，但 `GET /v1/capabilities` 需 Bearer 驗證，既有授權 client 未確認可用。**等待使用者明確授權**以現有官方認證方式進行一次本機唯讀 capabilities 查詢；不讀取／列印／上傳／修改憑證，不降低驗證、不新增程式或修改全域設定。若沒有安全可用的既有 client，先報最小官方方案及影響，取得批准後才建立新 Issue。即使 capabilities 成功，也不能把 `stopping` 當 `cancelled` 或把提示中的工具上限當硬停止。此階段**不得啟動新 Hermes 推論 session／GPU／ComfyUI 提交**。

## 四、交接、環境與安全

- GitHub Issue 讀寫與本機 Codex 接手交接已在歷次 #1/#2/#4/#5/#6 回合核對；Codex 已在 #9 留接手與 PARTIAL 回報，ChatGPT 已審查；沒有新的 ready 任務，不能聲稱已有下一件 Codex 工作。
- ChatGPT 每小時檢查更新與審查後續；`codex-ready` 才是本機 Codex 排程可接手入口。監控不代表任務自動完成，回報須附實際 Issue/commit。
- **禁止佔用使用者標準 127.0.0.1:8188 的受限白名單 ComfyUI**。不得擅自停止日常 ComfyUI、變更全域設定、讀取 Open WebUI 帳密／憑證、公開私人圖片／本機路徑。
- 後續所有 GPU、生圖、模型／VLM 替換、安裝、修改工作流及自訂程式，都需獨立明確授權；按 [PROTOCOL.md](PROTOCOL.md) 控制任務權限。
- 本倉庫**僅存公開去敏交接資料**，不放原始圖片、私人參考、工作流完整本機資產、虛擬環境或原始執行日誌。

## 五、下一階段決策

先取得使用者對現有官方 API 認證方式進行**一次本機唯讀 capabilities 檢查**的明確授權，並確認不需暴露秘密、修改設定或新增程式；無法安全做到就保持 BLOCKED 並報缺口。其後仍須證實安全停止控制，不得無條件啟動新的 Hermes 推論回合。完成安全前提後，才安排有新變因、明確上限的階段 2 驗證 A/B 生成 output 來源鏈。若需要新專案指引檔、改 Gateway 設定或新增程式，先提精確差異、影響及回退，待使用者批准。**不要為了維持 codex-ready 而重複派同類失敗任務。**
