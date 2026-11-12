# 本地生圖 Agent｜目前狀態

**更新：2026-11-13（台灣時間）**。長期方向與每階段驗收門檻見 [八階段正式藍圖](ROADMAP_8_STAGES.md)；GitHub Issue 才是 Codex 可執行的工作授權，本文不是本機指令。

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
- [#14 A 人物／B 黑湖完整工作流唯讀對應](https://github.com/JFTai/Local-image-generation-agent-/issues/14)：**本 Issue PASS，已由 ChatGPT 審查**（[審查](https://github.com/JFTai/Local-image-generation-agent-/issues/14#issuecomment-6830093779)）。Codex 本機核對 A/B 來源各 6/6 SHA、原完整 workflow A `M04-P03.json` 15/15 節點與 B `M03-P02.json` 17/17 節點、既有 history／PNG SHA；已裝 Skill 5.1.0 `extract_schema.py`／`check_deps.py --strict` 回報依賴 ready。A/B 的既有生成歷史可追溯，**本輪沒有 Hermes 新回合、Gateway 啟動、GPU 或 ComfyUI 新提交**；不能當作自主來源選取、全流程回圖或階段 2 整體 PASS。B 的 input 副檔名 .png 與實際 JPEG 格式不一致，但歷史曾成功載入，未在本輪更改。\n- [#15 B 黑湖 P02 真實生圖與回圖](https://github.com/JFTai/Local-image-generation-agent-/issues/15)：**整體 FAIL，Codex 已回報、ChatGPT 已正式審查**（[審查](https://github.com/JFTai/Local-image-generation-agent-/issues/15#issuecomment-6832863685)）。2026-11-13 01:09:41 新 Open WebUI 對話指令送達 Hermes；Hermes 以官方 comfy-cli 提交完整 B `M03-P02.json` 17/17 節點、三張參考連線不變，只有節點 5 正向提示更改；來源名稱由指令提供，屬**引導式**。本輪觀察沒有 #12 舊回合重播。**2 次 CLI 生成嘗試，僅 1 次 ComfyUI 接收並 GPU 執行（原 1/1 額度用盡）**；ComfyUI 在文字編碼器／文字模型載入階段中止，0 張完成 PNG、0 次 Open WebUI 顯圖；26 次模型呼叫、27 次工具呼叫。01:17:27 官方停止 Gateway，ComfyUI 原程序已退出，WebUI/Ollama 仍在。**精確 exception／traceback 尚未取得，不能猜為 Unicode、OOM 或模型缺陷**。本輪未獨立證明 Skill 載入。下一步 #16 查實故障並在可逆修復後一次真實回歸。
- [#16 #15 文字編碼器崩潰原生定位](https://github.com/JFTai/Local-image-generation-agent-/issues/16)：**整體 BLOCKED（修復與回歸），故障分類 PARTIAL、工作流一致性 PASS，ChatGPT 已審查**（[審查](https://github.com/JFTai/Local-image-generation-agent-/issues/16#issuecomment-6833417129)）。Codex 由 #15 日誌確認 ComfyUI 0.38.0／動態顯存 0.5.5 在節點 5 的 `ModelPatcherDynamic.load`／`weight.to(..., device=device_to)` 發生 `Fatal Python error: Aborted`；同時段 NVIDIA 事件 153、Windows LiveKernelEvent 0x141／0x117（GPU 引擎／驅動逾時）。**同時段關聯已確認，底層根因未確認**；無 JSON/Unicode 或明確 CUDA OOM/pagefile exception。`--disable-dynamic-vram` 是官方可逆候選，但未證實對本機 TDR 有效，WATCHDOG dump 存取受限，故未冒險重試。本輪 0 WebUI/Hermes/ComfyUI/GPU、0 PNG、0 新程式或設定修改。ComfyUI/Gateway 未監聽、WebUI/Ollama 仍在。**#16 原 1/1 GPU 額度未使用，不轉移至其他 Issue**。
- [#17 既有 P01 PNG 新 Hermes 回合自主回圖](https://github.com/JFTai/Local-image-generation-agent-/issues/17)：**整體 FAIL，已由 ChatGPT 正式審查**（[審查](https://github.com/JFTai/Local-image-generation-agent-/issues/17#issuecomment-6834996926)）。2026-11-13 03:25:20 新 Open WebUI 指令真正進入 Hermes；Codex 執行中觀察，未見舊回合重播。#11 原 PNG 2048×2048、7,105,631 bytes、SHA-256 `e05e6b6bb262db399ba73a5e35902bbfa285889176bc093e312a653a9a62076a`，Codex 以正確 `GET /view?filename=…&subfolder=&type=output` 取得 HTTP 200/image/png；但 Hermes **3 次錯用 `/view/<filename>` 均 HTTP 404**，同對話 DOM 目標圖片 0，故自主取圖／回圖 FAIL。1 條 WebUI 指令、1 新 Hermes 回合、10 次模型呼叫、10 次 terminal、9 筆工具回條；**ComfyUI 新提交 0、GPU 生圖 0**，03:28:11 官方停止 Gateway，ComfyUI queue 0。此次定位出**具體 API 路由組裝錯誤**，不是 GPU 故障，也未證明在正確 URL 下 Markdown/DOM 已可顯示。
- [#10 官方認證 capabilities 預檢](https://github.com/JFTai/Local-image-generation-agent-/issues/10)：Codex 原生查核 Gateway loopback listener／TCP 可達，但沒有符合本次嚴格授權的既存 capabilities-only 官方認證 client；**GET 0/1，BLOCKED，`codex-blocked`**。沒有新 HTTP API、Hermes session、推論、GPU、ComfyUI 提交或服務變更；同版官方原始碼宣告 run/status/SSE/stop 不等於運行中 API 已認證驗證。[ChatGPT 正式審查](https://github.com/JFTai/Local-image-generation-agent-/issues/10#issuecomment-6799557510)。
- 以上成功不等於第二階段完整 PASS，也不等於「生成→VLM 自動審圖→必要修正／重生→模擬器回傳」已完成.

## 三、當前主線：Open WebUI → Hermes → ComfyUI → 回圖

**2026-11-12 使用者最新指示仍優先**：Open WebUI 真正指揮 Hermes，Hermes 使用官方 Skill／comfy-cli 與既有完整工作流生圖並回傳，Codex 在實測中觀察，ChatGPT 透過 GitHub 審查／續派八階段。能修就修，修不成查同版官方及社群案例，再不行就回報並繼續其他功能；不增加自訂程式或無關診斷關卡。

**最新派工：[Issue #18](https://github.com/JFTai/Local-image-generation-agent-/issues/18) 已建立 `codex-ready`，尚待本機 Codex 接手**。#17 的新 WebUI→Hermes 回合與原 P01 PNG 來源驗證已 PASS，但 Hermes 三次錯用 `/view/<filename>` 導致 404、DOM 無圖，故整體 FAIL。#18 將**已核對 HTTP 200、hash 一致的正確 `GET /view?filename=…&subfolder=…&type=output` URL 明確提供在單一新 Open WebUI 指令中**，讓 Hermes 自己 GET 並在同對話輸出真正可載入圖片，**ComfyUI 新 GPU／新生成 = 0**。即使成功也僅為「引導式 URL 回圖」子項 PASS，不代表 Hermes 自主發現路由、同回合新生圖＋回圖已完全 PASS。#16 GPU TDR 仍獨立未解。

[#10](https://github.com/JFTai/Local-image-generation-agent-/issues/10) capabilities-only 401/BLOCKED 仍是非主線診斷，**不是任何生圖或回圖功能的強制先決條件**。已知舊回合恢復及 GPU TDR 是實際觀察到的風險，只針對會受影響的操作處理，不阻擋其他可執行工作。

## 四、交接、環境與安全

- GitHub Issue 讀寫與本機 Codex 接手交接已驗證；#11 PARTIAL、#12 FAIL、#13 PARTIAL 均有 Codex 回報與 ChatGPT 審查。**#14 已 PASS；#15 FAIL；#16 BLOCKED 已審查；#17 已由 Codex 回報 FAIL 並由 ChatGPT 正式審查；#18 是唯一新 `codex-ready`**，尚未有 #18 接手證據，不能宣稱回圖已修復。GitHub 每小時輪詢不能冒充本機 Codex 的即時監控。
- ChatGPT 每小時讀取 Codex 回報、審查後續並續派；`codex-ready` 是本機 Codex 交接入口。排程不應因單一診斷 Issue 被 BLOCKED 就停用；只有實質結果與直接證據才能判為完成。
- **禁止佔用使用者標準 127.0.0.1:8188 的受限白名單 ComfyUI**。不得擅自停止日常 ComfyUI、變更全域設定、讀取 Open WebUI 帳密／憑證、公開私人圖片／本機路徑。
- 使用者 2026-11-12 已明確要求進行**實際生圖及回傳測試**；#11 授權 2 次，實際使用 1 次 ComfyUI 提交；**#12 原授權新生圖 0，實際因舊回合自動續跑意外發生 1 次未授權 GPU 提交**，不能誤記為 0。#13、#14 各自為零 GPU 的不同子任務，不延長 #11/#12 額度。後續具體功能 Issue 可明列合理 GPU 上限；不得擅自下載／更換模型、安裝套件、增加自訂程式、覆蓋原完整工作流或暴露憑證。
- 本倉庫**僅存公開去敏交接資料**，不放原始圖片、私人參考、工作流完整本機資產、虛擬環境或原始執行日誌。

## 五、下一階段決策

**已發布 [#18](https://github.com/JFTai/Local-image-generation-agent-/issues/18)**：依 #17 已證實的具體錯誤，這次**只改 URL 路由提示**，使用原 P01 PNG、Codex 已驗證的正確 ComfyUI `GET /view?filename=…&subfolder=…&type=output` 完整 URL，讓 Hermes 在一個新的 Open WebUI 回合自己 GET 並在同對話輸出 Markdown 圖片；Codex 在實際操作期間觀察 Hermes、HTTP、DOM、停止與 GPU 提交。**不新增生圖、工作流、程式、服務、模型或套件。** 不把 Codex 提供 URL 說成 Hermes 自主發現；只有 Hermes GET 與同對話 DOM 可見才能判引導式回圖 PASS。若仍失敗，按已觀察到的 URL／Markdown／瀏覽器問題用既有官方功能查修，對照同版官方／GitHub／論壇案例，記錄結果並續做其他功能。

**#16 GPU TDR 保持獨立未解**：ComfyUI 0.38.0／動態顯存 0.5.5 在 P02 文字編碼載入發生 fatal abort，Windows NVIDIA 153／LiveKernelEvent 0x141／0x117 同時段出現；僅證實時間關聯，未證實根因。不能推定顯卡損壞，亦不擅自改驅動、Windows TDR、全域設定或模型。先完成零 GPU 的回圖、來源及工具流程，之後再以有實質新修復變因的方式恢復 P02 生圖，持續八階段開發。

