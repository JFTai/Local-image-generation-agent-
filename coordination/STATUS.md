# 目前狀態

更新日期：**2026-11-11（台灣時間）**。本頁是專案當前執行面板；長期階段依據 [八階段藍圖](ROADMAP_8_STAGES.md)，任務授權依據 [PROTOCOL.md](PROTOCOL.md)。**本頁與路線圖本身不授權 GPU，也不表示 Hermes 自動讀取了 GitHub 文件。**

## 八階段里程碑

| 階段 | 狀態 | 下一個通關要求 |
|---|---|---|
| 1 原生文生圖閉環 | 基礎 PASS（P01） | 後續回歸須證實無提醒交付，不能推論整體自主 |
| **2 人物、場景與參考素材管理** | **PARTIAL／目前主線** | A/B 來源四類及 output→run→history→workflow→SHA 由 Hermes 自主核對；工具停止與回合限制可靠 |
| 3 多種生圖工作流自主選擇 | 未正式驗收 | 既有完整工作流、schema／依賴與獲授權情境的選擇證據 |
| 4 多角色／跨場景一致性 | 未正式驗收 | 人物與場景參考跨圖的可重現對照 |
| 5 自動審圖／有限次修正 | 未正式驗收 | 真實 VLM 審圖、改動及有限重試的證據 |
| 6 劇情模擬器 LLM 正式委派 | 未正式驗收 | 模擬器主動發起並接收圖像／失敗 |
| 7 長時間自主運作／故障恢復 | 未正式驗收 | 中斷、超時、恢復、避免重複提交 |
| 8 完整模擬器情境驗收 | 未開始 | 完整端到端情境矩陣及使用者驗收 |

## 當前 Codex 工作

**[#8：階段 2 的官方 Context Files／生成來源鏈／停止能力唯讀核對](https://github.com/JFTai/Local-image-generation-agent-/issues/8)**

- **GitHub 標籤**：發布時為 `codex-ready`；**尚未有 Codex 接手證據**，實際狀態以 Issue 最新留言／標籤為準。
- **範圍**：只讀檢查本機 Hermes v0.22.5 API Server 的 context file 載入優先序與工作目錄、既有 A/B output/run/history/PNG 來源鏈、真正工具預算／取消機制；提出最小且可回滾的官方方案。
- **上限**：新 Hermes session **0**、GPU **0**、ComfyUI POST /prompt **0**、圖片交付 **0**、新增自訂程式 **0**；只允許必要的唯讀檢查，不擴大為 Hermes 新 session。不得停止或替換使用者正常 ComfyUI `127.0.0.1:8188`，不得改設定、模型、Skill、工作流、參考圖。
- **完成定義**：只讀證據與可行性報告 PASS，不等於階段 2 PASS。下一個 Hermes 新 session 或任何實際修復需另設任務並審查。

**防重複交接**：[#7](https://github.com/JFTai/Local-image-generation-agent-/issues/7) 與 #8 同時建立且範圍重複，已取消 #7 的 codex-ready 並以 duplicate 結案，**#8 是唯一有效的執行任務**；不得把 #7 結案當作技術 PASS。

## 近期驗收紀錄

| Issue | 已核對結果 | 資源／限制 |
|---|---|---|
| [#1](https://github.com/JFTai/Local-image-generation-agent-/issues/1)、[#2](https://github.com/JFTai/Local-image-generation-agent-/issues/2) | GitHub 交接與 ChatGPT→Codex 往返測試完成 | 已結案 |
| [#3](https://github.com/JFTai/Local-image-generation-agent-/issues/3) | Open WebUI 既有圖片 DOM 顯示 PASS；無提醒完整自主交付 PARTIAL | 2/2 測試已用完，codex-review |
| [#4](https://github.com/JFTai/Local-image-generation-agent-/issues/4) | Hermes API Server 工具診斷完成 | codex-done |
| [#5](https://github.com/JFTai/Local-image-generation-agent-/issues/5) | 跨對話來源找回 PARTIAL | 2/2 session 用完，codex-review |
| [#6](https://github.com/JFTai/Local-image-generation-agent-/issues/6) | 引導式 12/12 原附件／工作副本／input SHA 相符；output 來源鏈 0/2、工具 14 次超出提示 8 次、Stop 後未證實取消；[ChatGPT 正式審查](https://github.com/JFTai/Local-image-generation-agent-/issues/6#issuecomment-6789156770) | 1/1 session 用完，codex-review，GPU 0／自訂程式 0 |

## 交接與執行事實

- 指定儲存庫：JFTai/Local-image-generation-agent-。GitHub 連接器**已實際建立 Issues 與寫入審查留言**；不是僅有讀取權限。
- GitHub 只存放協作規則、去敏證據、階段藍圖及任務；**不是**本機工作流／圖片／模型／完整執行環境備份。
- Codex 是否接手、任務是否執行，以 `codex-in-progress`／接手留言與正式回報判定；GitHub 已建立 Issue 不代表 Codex 已執行。ChatGPT 每小時監控啟用不保證每次執行成功。
- 任務入口只接受 JFTai 作者、open、`codex-ready`；已存在 ready／in-progress 時不建立競爭 Issue；codex-review 先審查後派下一個**明確授權的零 GPU／無自訂程式**小任務。
- **使用者日常 ComfyUI 8188 不得被受限 Custom Nodes 白名單實例長期佔用或擅自終止。**
- 仍待驗證的關鍵缺口：Hermes 真正自主新對話找回、輸出鏈核對、實際停止保證、無需提醒的圖片交付、後續自動審圖及模擬器 LLM 委派。禁止把基礎 P01 成功或 Codex 手動核對當成整體閉環完成。

## 相關研究

官方 [Hermes Context Files](https://hermes-agent.nousresearch.com/docs/user-guide/features/context-files/)／[API Server](https://hermes-agent.nousresearch.com/docs/user-guide/features/api-server/)，上游 [中斷案例 #84207](https://github.com/NousResearch/hermes-agent/issues/84207)、[#65298](https://github.com/NousResearch/hermes-agent/issues/65298)，[Reddit Hermes 長期記憶經驗](https://www.reddit.com/r/hermesagent/comments/1wjm700/i_tried_5_ways_to_give_my_hermes_agent_longterm/) 與 [Unix & Linux 相對路徑討論](https://unix.stackexchange.com/questions/639383/concatenating-files-from-a-directory-using-cat-gives-no-such-file-or-directory)。外部案例需比對本機 v0.22.5，不是本機根因證明。
