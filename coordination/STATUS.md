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
- 以上成功不等於第二階段完整 PASS，也不等於「生成→VLM 自動審圖→必要修正／重生→模擬器回傳」已完成。

## 三、唯一目前可執行任務

**[#8｜八階段 S2：官方 Context Files、停止控制與來源鏈的唯讀核查](https://github.com/JFTai/Local-image-generation-agent-/issues/8)**：open、`codex-ready`。這是階段 2 可信來源與可執行停止能力的準備工作；**新 Hermes session = 0、GPU = 0、ComfyUI POST /prompt = 0、新增自訂程式 = 0**。優先研究本機 Gateway 的 Context File 何時載入、output→run→history→workflow→SHA 的官方欄位、以及 stop/turn budget 是否真正生效；提出必要的最小修改供後續授權，不在 #8 自行改設定或新建本機指引。

同時建立的 [#7](https://github.com/JFTai/Local-image-generation-agent-/issues/7) 是重複任務，**已關閉為 duplicate**，不得接手。#8 是唯一 canonical 交接任務；兩者的創建／去重是 GitHub 文件操作，不是 Codex 本機 GPU 或 Hermes session 執行證據。

## 四、交接、環境與安全

- GitHub Issue 讀寫與本機 Codex 接手交接已在歷次 #1/#2/#4/#5/#6 回合核對；新的 #8 **只有 GitHub ready，尚未因此宣稱 Codex 已接手**。
- ChatGPT 每小時檢查更新與審查後續；`codex-ready` 才是本機 Codex 排程可接手入口。監控不代表任務自動完成，回報須附實際 Issue/commit。
- **禁止佔用使用者標準 127.0.0.1:8188 的受限白名單 ComfyUI**。不得擅自停止日常 ComfyUI、變更全域設定、讀取 Open WebUI 帳密／憑證、公開私人圖片／本機路徑。
- 後續所有 GPU、生圖、模型／VLM 替換、安裝、修改工作流及自訂程式，都需獨立明確授權；按 [PROTOCOL.md](PROTOCOL.md) 控制任務權限。
- 本倉庫**僅存公開去敏交接資料**，不放原始圖片、私人參考、工作流完整本機資產、虛擬環境或原始執行日誌。

## 五、下一階段決策

先完成 #8 的唯讀原生控制面核查並審查報告。若確定可用官方 Context Files 與真正可停止的執行方式，才設計新的、有明確上限的階段 2 測試驗證 A/B 生成 output 來源鏈。若需要新專案指引檔、改 Gateway 設定或新增程式，先提精確差異、影響及回退，待使用者批准。**不要為了維持 codex-ready 而重複派同類失敗任務。**
