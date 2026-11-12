# 本地生圖 Agent｜八階段開發藍圖

**版本：2026-11-11。** 依照「Harness問題分析」先前提出的**八個階段名稱與原始順序**制定本藍圖；下面的詳細驗收門檻及狀態是按現有實測**重新設計**，不宣稱為當年逐字原文。

> **2026-11-12 使用者最新開發指示（優先於 11/11 對測試門檻的過度保守解讀）**：必須以 Open WebUI → Hermes Agent → 原有完整 ComfyUI 工作流生圖 → 圖片回傳 Open WebUI 的真實操作作為日常主線；Codex 在生圖測試**執行當時**觀察 Hermes，遇錯先修、修不成先查同版官方文件／GitHub／社群案例，再無解就回報並繼續其他項目；ChatGPT 與 Codex 透過 GitHub Issue 指令與回報。API `/v1`、`health`、`capabilities` 及 Sandbox 只是查錯工具/環境概念，並非先完成才能生圖的全局門檻。**[#11](https://github.com/JFTai/Local-image-generation-agent-/issues/11) 是新的端到端功能測試任務**。這段更新不修改過往任務額度及歷史測試結果。\
> 原則仍是保留現有完整工作流、官方 Hermes ComfyUI Skill + comfy-cli、不增加自訂程式及保護日常 ComfyUI。

## 0. 核心目標、架構與不可變限制

**最終目標**：劇情模擬器 LLM 或 Open WebUI 接收生圖需求 → 生圖 Agent 理解需求、找到正確人物／場景參考 → 選擇原有完整工作流 → 經 ComfyUI 生成 → 自動審圖 → 必要時有限次修正／重生 → 圖片與結果回傳劇情模擬器 LLM 及 Open WebUI。

**架構原則**：Open WebUI 作聊天介面、Hermes Agent 作生圖代理，優先官方 Hermes ComfyUI Skill、comfy-cli 與既有 ComfyUI 工作流。禁止在未獨立論證與批准前新增自訂程式、資料庫、Publisher、MCP、Harness 或常駐服務。**不得把多份原始完整工作流擅自簡化成一份。**

**本檔不是無限操作授權**。使用者 2026-11-12 已明確要求實際生圖回傳與八階段測試；單一 Issue 記載該次功能測試的情境與 GPU 提交上限即可執行，不必再因獨立認證端點被 BLOCKED 而停工。未授權的模型切換、登入憑證讀取、自訂 Python 程式、常駐服務或覆蓋既有工作流仍不得擅自執行。

**本機共用環境**：使用者一般 ComfyUI 是 127.0.0.1:8188。Codex 應先檢查真正的 API 健康與啟動方式；不得用限制 Custom Nodes 白名單的測試實例佔用使用者日常 8188，不得終止使用者服務。需隔離連接埠時先取得授權。當次按需啟動與健康檢查可行，但跨回合 PID 消失本身不代表故障。

**共通驗收方法**：每階段記錄來源、Prompt ID／工作流版本、HTTP／檔案 SHA、真實聊天圖片 DOM 或模擬器接收、GPU 次數、工具次數、失敗回退及 PASS／PARTIAL／FAIL／BLOCKED；不能用 Codex 手動代查冒充 Hermes 自主完成。超過兩輪的同類問題應先研究官方文件、GitHub、Reddit 與技術論壇的適用案例，禁止不改變條件就重跑。提示詞的「最多 N 次工具」不是硬限制；若失控，必須取得真實取消終態，不得憑按下停止按鈕宣稱取消。

## 1. 八階段正式藍圖

### 階段 1｜原生文生圖閉環
- **目標**：Hermes 依新提示詞修改既存 P01，以官方 Skill／comfy-cli 提交、等待、下載並在**同一回合**顯示新圖片。
- **完成門檻**：真實新圖片可追溯 ComfyUI 提交、Prompt ID、下載與顯示；尺寸、檔案證據一致；新增自訂程式 0。
- **現況：基礎 PASS，僅限已測 P01**。曾完成 2048×2048 文生圖；但 [#3](https://github.com/JFTai/Local-image-generation-agent-/issues/3) 的舊圖片交付雖在 Open WebUI DOM 成功，曾需一次 Markdown 格式提醒，故**完全自主交付仍 PARTIAL**，列作後續回歸缺口。

### 階段 2｜人物、場景與參考素材管理
- **目標**：新 Hermes session 可靠辨識、選取及取得原附件、工作副本、ComfyUI input、生成 output；同時能找回工作流、Prompt ID、保存的 history 及其關聯。
- **完成門檻**：A 人物、B 黑湖等既有素材由 Hermes **自行**在新 session 找到；原件／副本／服務 input／生成 output 角色分類正確、真實路徑可讀、SHA 與 run/history/workflow 相互對應；不使用舊 final 答案或 Codex 代尋。若有明示來源入口，須稱「引導式」，不得稱完全自主發現。
- **現況：PARTIAL，本輪主線**。B 場景參考 P02 曾成功；A 參考人物測試未全部通過；[#5](https://github.com/JFTai/Local-image-generation-agent-/issues/5) 跨對話來源分類失敗。[#6](https://github.com/JFTai/Local-image-generation-agent-/issues/6) 引導式核對 12/12 原附件／工作副本／input，卻未核對 2 筆生成 output；原附件曾被錯稱為 Hermes 對話附件，14 次 terminal 超出提示 8 次，停止操作未取得真正 Gateway cancelled 證據。**不得把 #6 判為整階段 PASS。**
- **下一步**：以 #11 的 Open WebUI → Hermes → ComfyUI → 回圖實測先恢復主線，Codex 同步觀察；接著直接用 A 人物與 B 黑湖參考執行階段 2 驗收，有實際障礙時才調查 Context Files、來源 checks→run→history→PNG、停止工具。可使用既有 `source-verification.json` 的 `checks[].path`、`expected_sha256`、`compiled.json`、`run.json`、`history.json`；不猜資料夾或建自訂 Registry。

### 階段 3｜自主選擇及操作多種生圖工作流
- **目標**：依場景需求選用、參數化原有完整文生圖／圖生圖／人物參考／Inpaint／Outpaint／多人物等工作流，透過官方 schema/dependencies 驗證。
- **完成門檻**：為各已授權情境提供工作流選擇理由、保留原工作流完整節點、記錄實際修改與生成證據；缺模型或節點明列 BLOCKED，不宣稱可用。
- **現況：未正式驗收**。可在獨立具體功能 Issue 的合理生成範圍內測試原有工作流；階段 2 的不足應記作相關風險，不是全面禁止測試。

### 階段 4｜多角色與跨場景一致性
- **目標**：正確選用人物三視圖與場景參考，維持角色身份、髮型、服裝、比例、風格、多人位置及跨場景連續性。
- **完成門檻**：單角色、雙人／多人及跨場景對照樣例，具可追溯參考來源、人物區別、視覺比較與失敗分析；不能因一張相似就宣稱一致。
- **現況：未正式驗收**。依賴階段 2 來源正確性及階段 3 工作流能力。

### 階段 5｜自動審圖與必要時重新生成
- **目標**：用實際視覺模型或官方圖片審查能力檢查指令符合度、參考一致性、畫面瑕疵；必要時有限次修改參數、Inpaint 或重生。
- **完成門檻**：真實生成圖送審、可追溯審查依據、錯誤分類與改動、復驗改善；預先批准 GPU／重試次數，超限即停。不得以文字猜測冒充看圖。
- **模型候選評估**：包含 NVIDIA LocateAnything-3B，以及使用者指定列入的 [DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic](https://huggingface.co/xtools-at/DeepSeek-V4-Pro-Qwen3.5-9B-UNCENSORED-heretic)。先確認模型**實際支援的模態**、授權／相容性、Ollama/Hermes 工具呼叫與 VRAM；不假定後者有 VLM 能力、不擅自更換。必要時同條件 A/B 比較推論模型。優先序列調度：推理／必要讀圖 → 釋放模型 → ComfyUI → 釋放生成模型 → 審圖。
- **現況：未正式驗收**。

### 階段 6｜正式連接劇情模擬器 LLM
- **目標**：**模擬器 LLM 主動判斷並委派**生圖需求，Agent 以正確素材／工作流生圖審圖，結果回到**模擬器 LLM**，並可在 Open WebUI 顯示；不是只讓使用者手動 prompt。
- **完成門檻**：模擬器自行發出可追蹤任務與接收圖片／錯誤，普通對話不誤觸 GPU；官方委派優先，若需自訂交接程式則另審核。
- **現況：未正式驗收**；可提前唯讀設計介面需求，不改執行環境。

### 階段 7｜長時間自主運作與故障恢復
- **目標**：處理 API 斷線、空 history、重開機／新 session、受限工具 CWD、重複提交、GPU/VRAM 競爭、超時及收圖失敗；保持安全可恢復。
- **完成門檻**：至少涵蓋中斷／恢復／防雙重提交及安全停止的故障注入測試，提供程序終態、資源使用、可回滾記錄；不需常駐自訂 Agent。
- **現況：未正式驗收**。**基礎安全措施跨越所有階段立即適用**，不能等到階段 7 才開始保護 8188、憑證與提交次數。

### 階段 8｜完整模擬器情境測試與正式完成
- **目標**：連續真實劇情情境測試「模擬器委派 → 資產／工作流 → 生成 → 審圖 → 必要修正 → 圖片及狀態回傳」，涵蓋多人、場景、跨對話與故障。
- **完成門檻**：事先核准情境／GPU 測試矩陣，按實際模擬器收圖與 Open WebUI DOM、來源證據及有限修正結果判 PASS；需使用者確認，不能以單張成功圖宣稱專案完成。
- **現況：未開始**。

## 2. 前置依賴、里程碑與當前工作

**主序**：階段 1 基礎 PASS → **階段 2 PARTIAL（目前）** → 3 → 4 → 5 → 6 → 7 → 8。階段 7 的一般安全約束是全程共通；階段 6 可先做唯讀委派契約設計，正式端到端驗收仍需前面能力。

- [#3 圖片交付](https://github.com/JFTai/Local-image-generation-agent-/issues/3)：舊圖片 DOM 顯示 PASS，但有格式提醒，完全自主 PARTIAL，2/2 額度已用完，待審。
- [#4 Hermes 工具診斷](https://github.com/JFTai/Local-image-generation-agent-/issues/4)：codex-done，已結案。
- [#5 跨對話來源找回](https://github.com/JFTai/Local-image-generation-agent-/issues/5)：PARTIAL，2/2 新 session 用完，待審。
- [#6 四類來源與路徑](https://github.com/JFTai/Local-image-generation-agent-/issues/6)：PARTIAL；引導式 12/12 參考核對，但 output 0/2、停止／工具次數問題，1/1 session 用完，待審。
- **當前下一步**：[#11](https://github.com/JFTai/Local-image-generation-agent-/issues/11) 真正由 Open WebUI 委派 Hermes 使用既有 P01 生圖、回傳並顯示；Codex 在執行中觀察，之後階段 2 A/B 人物場景與來源繼續。#10 單一認證檢查 BLOCKED 不是整體主線關卡。後續活動的 `codex-ready` Issue 與最新結果以 [STATUS.md](STATUS.md) 為準。

## 3. 交接規則、審查與後續方向

- 以 [PROTOCOL.md](PROTOCOL.md) 為任務授權門；只接 JFTai 建立、open、`codex-ready` 的明確 Issue；已有 in-progress 不再競爭接手，建議同時最多一項活動 ready。
- 每回合審查：**階段定位與依賴 → 真實功能操作與已核實證據 → 官方修復方法 → 記錄 GPU／工具使用及停止結果 → 不干擾 8188、私有素材／憑證 → 有效驗收與下一件功能任務**。問題部分 BLOCKED 就保留且研究網路案例；其他獨立可測項目繼續，不能把單一診斷失敗當全局禁止。
- 路線圖只管理方向；`STATUS.md` 管理當前進度；Issue 是交接與 Codex 回報的唯一執行入口。GitHub 上的文件**不代表 Hermes Gateway 自動載入**，須以本機實際 session 驗證 context file 的路徑、時點與優先序。
- 每小時 ChatGPT 監控：有新實質結果就審查及續派不重複的具體功能 Issue。使用者已授權正常生圖測試，應為每個 Issue 設合理 GPU 提交額度；額外模型安裝／切換、讀取憑證、自訂程式或重大變更仍應單獨批准。GitHub 每小時輪詢不能冒充本機 Codex 在執行中的即時觀察。

## 4. 參考資料

- 使用者先前「Harness問題分析」八階段名稱，與本倉庫 [#3](https://github.com/JFTai/Local-image-generation-agent-/issues/3)、[#5](https://github.com/JFTai/Local-image-generation-agent-/issues/5)、[#6](https://github.com/JFTai/Local-image-generation-agent-/issues/6) 的實際驗收。
- [Hermes 官方 Context Files](https://hermes-agent.nousresearch.com/docs/user-guide/features/context-files) 與 [Persistent Memory](https://hermes-agent.nousresearch.com/docs/user-guide/features/memory)：只能支持調查方法，不能取代本機 v0.22.5 入口證據。
- [MSYS2 路徑](https://www.msys2.org/docs/filesystem-paths/)；[Hermes CWD 類似個案 #136842](https://github.com/NousResearch/hermes-agent/issues/136842)；[Reddit Hermes 記憶討論](https://www.reddit.com/r/hermesagent/comments/1v1r310/how_do_you_manage_longterm_memory_when_multiple/)；[Unix & Linux 多檔路徑案例](https://unix.stackexchange.com/questions/639383/concatenating-files-from-a-directory-using-cat-gives-no-such-file-or-directory)。外部案例僅供比對，不直接判為本機根因。
