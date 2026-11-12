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
- [#11 Open WebUI→Hermes→P01 新生圖→回傳](https://github.com/JFTai/Local-image-generation-agent-/issues/11)：**整體 PARTIAL，`codex-review`**。2026-11-12 21:10–21:20 Codex 在測試中觀察：Open WebUI v0.11.0 一條 P01 指令確實送達 Hermes；Hermes 自行使用**官方 Skill 的 `run_workflow.py`** 提交完整 10 節點 P01（**1/2 ComfyUI 提交**），history success，Prompt ID `37951822-b0ec-4387-b78e-887a5aaf6811`，2048×2048 PNG SHA-256 `e05e6b6bb262db399ba73a5e35902bbfa285889176bc093e312a653a9a62076a`。但正向提示未加入指定韓漫風格，**同一 WebUI 對話沒有載入新圖**（範例 img naturalWidth=0）。前端 Stop 後 Hermes 工具仍執行，最後官方 `gateway stop --interrupt` 返回 0、Gateway listener 消失，ComfyUI queue 空；工具結果 26 筆。這不是完整自主 PASS。[ChatGPT 審查](https://github.com/JFTai/Local-image-generation-agent-/issues/11#issuecomment-6825032645)。
- [#12 既有 PNG 回圖修復](https://github.com/JFTai/Local-image-generation-agent-/issues/12)：**整體 FAIL，已審查**。2026-11-12 21:43:57 Gateway 啟動時先恢復 #11 中斷的 Hermes 舊回合，#12 唯一新 WebUI 指令未證明被新 Hermes 回合接收；原 PNG 的 history／SHA／HTTP 核對成功，但原對話 DOM 仍無目標圖。**重大執行偏差：#12 明定 ComfyUI 新提交 0，實際舊回合卻自行啟動 comfy.exe 並產生 1 筆未授權新提交與新圖**，Prompt ID `92624ad7-560e-486d-bcc0-f8b96a75ae2d`，21:46:16 history success。官方 `gateway stop --interrupt` 後 listener 消失、ComfyUI queue 清空，但未證明下次啟動不會重播。未回圖、不可算全自主 PASS。[ChatGPT FAIL 審查](https://github.com/JFTai/Local-image-generation-agent-/issues/12#issuecomment-6826670200)。
- [#13 舊回合重播原生機制](https://github.com/JFTai/Local-image-generation-agent-/issues/13)：**整體 PARTIAL，`codex-review`，已由 ChatGPT 審查**。Codex 唯讀核對 7 個本機檔案與 Hermes v0.22.5 官方來源一致，確認 #11/#12 路由持久化 `resume_pending=true`、`resume_reason=shutdown_timeout`、`suspended=false`；恢復標記 21:46:13，預設 3,600 秒窗口 22:46:13 到期。但本機實際生效時間窗、最後 history 時間及 DB admission/FIFO 未驗證，不能保證下一次 Gateway 啟動或新訊息不再續跑。**未找到停機下只終止特定舊回合且保留歷史的官方入口**；本輪 Gateway／新推論／GPU／ComfyUI 提交均 0，沒有修復實作。[正式審查](https://github.com/JFTai/Local-image-generation-agent-/issues/13#issuecomment-6827606615)。
- [#10 官方認證 capabilities 預檢](https://github.com/JFTai/Local-image-generation-agent-/issues/10)：Codex 原生查核 Gateway loopback listener／TCP 可達，但沒有符合本次嚴格授權的既存 capabilities-only 官方認證 client；**GET 0/1，BLOCKED，`codex-blocked`**。沒有新 HTTP API、Hermes session、推論、GPU、ComfyUI 提交或服務變更；同版官方原始碼宣告 run/status/SSE/stop 不等於運行中 API 已認證驗證。[ChatGPT 正式審查](https://github.com/JFTai/Local-image-generation-agent-/issues/10#issuecomment-6799557510)。
- 以上成功不等於第二階段完整 PASS，也不等於「生成→VLM 自動審圖→必要修正／重生→模擬器回傳」已完成.

## 三、當前主線：Open WebUI → Hermes → ComfyUI → 回圖

**依 2026-11-12 使用者最新明確指示，主線改回實際操作：** Open WebUI 送出自然語言需求 → Hermes Agent 使用既有官方 Skill／comfy-cli 及使用者完整 ComfyUI 工作流生圖 → 在同一 Open WebUI 對話顯示新圖；Codex **測試進行時**觀察 Hermes 真正執行的步驟、在可回退範圍內修正問題，查官方／GitHub／社群實例；ChatGPT 讀取 GitHub 回報並接續測試八階段。

**目前唯一新派工：[Issue #14／A 人物與 B 黑湖參考到既有完整 ComfyUI 工作流節點的唯讀對應](https://github.com/JFTai/Local-image-generation-agent-/issues/14)**，`codex-ready`，待 Codex 接手。#13 已由 Codex 回報 PARTIAL 並完成 ChatGPT 審查；舊回合恢復旗標已定位，但尚未有可證明不重播的官方終止方法。為避免再次在無新指令時誤觸 GPU，先進行**不同變因的階段 2 工作流參考節點對應**（A/B 來源 SHA、完整 workflow、輸入節點／連線、模型／Custom Nodes 依賴）。這不是重做 #5/#6 的來源分類，且不需啟動 Gateway 或 GPU。完成後選定最小可驗證的真實 Open WebUI→Hermes 參考生圖任務；另行尋找既有官方防重播／終止方法。

[#10](https://github.com/JFTai/Local-image-generation-agent-/issues/10) 的單獨已認證 `GET /v1/capabilities` 是**非主線診斷 BLOCKED**，其原有嚴格授權與 0/1 使用量保持不變；**不再將其列為整體 Open WebUI/Hermes/ComfyUI 工作的強制開工門檻**。診斷用的 `/v1`、health、Sandbox 是既有工具／執行位置概念，不是新架構，也不是必裝或必測的新元件。真正遇到阻礙時就具體處理當前故障，不能因一個無關端點阻止其他測試。

## 四、交接、環境與安全

- GitHub Issue 讀寫與本機 Codex 接手交接已驗證；#11 PARTIAL、#12 FAIL、#13 PARTIAL 均有 Codex 回報與 ChatGPT 審查。**#14 為當前唯一 `codex-ready`**；Codex 尚未留下 #14 接手證據，不能宣稱已開始執行。GitHub 每小時輪詢不能冒充本機 Codex 的即時監控。
- ChatGPT 每小時讀取 Codex 回報、審查後續並續派；`codex-ready` 是本機 Codex 交接入口。排程不應因單一診斷 Issue 被 BLOCKED 就停用；只有實質結果與直接證據才能判為完成。
- **禁止佔用使用者標準 127.0.0.1:8188 的受限白名單 ComfyUI**。不得擅自停止日常 ComfyUI、變更全域設定、讀取 Open WebUI 帳密／憑證、公開私人圖片／本機路徑。
- 使用者 2026-11-12 已明確要求進行**實際生圖及回傳測試**；#11 授權 2 次，實際使用 1 次 ComfyUI 提交；**#12 原授權新生圖 0，實際因舊回合自動續跑意外發生 1 次未授權 GPU 提交**，不能誤記為 0。#13、#14 各自為零 GPU 的不同子任務，不延長 #11/#12 額度。後續具體功能 Issue 可明列合理 GPU 上限；不得擅自下載／更換模型、安裝套件、增加自訂程式、覆蓋原完整工作流或暴露憑證。
- 本倉庫**僅存公開去敏交接資料**，不放原始圖片、私人參考、工作流完整本機資產、虛擬環境或原始執行日誌。

## 五、下一階段決策

**#13 已完成唯讀調查並正式判 PARTIAL，並非重播故障已修復。** 依本機同版程式及外部 Hermes [#46934](https://github.com/NousResearch/hermes-agent/issues/46934)、[#58933](https://github.com/NousResearch/hermes-agent/issues/58933) 等案例，啟動時恢復與新訊息進入時恢復不一定相同；外部案例版本不同，不當成使用者本機根因。時間窗過期不代表可以無條件重新啟動 Gateway，特別是 #12 已實際多生一張。

**當前 #14** 先完成 A 人物／B 黑湖參考進入原有完整 workflow 的來源與節點對應，使用既有官方 Skill 工具唯讀核對，不新增程式、不動 8188、不重啟 Gateway。完成後依結果設計一次真正 Open WebUI→Hermes 參考生圖＋回圖測試；舊回合防重播需找到可驗證、可回退的原生方法後才重啟相同 session。若一項無法解決就留 BLOCKED 並研究同版官方／網路案例，不停止其他八階段工作。

