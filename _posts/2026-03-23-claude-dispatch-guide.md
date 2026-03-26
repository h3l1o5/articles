---
title: "Claude Dispatch 指南：用手機操控 AI 的 48 小時"
date: 2026-03-23
categories: [AI]
tags: [claude, dispatch, cowork, productivity, pm]
image:
  path: /assets/img/posts/2026-03-23-claude-dispatch-guide/banner.jpg
  alt: "The Claude Dispatch Guide"
---

> 原文：[The Claude Dispatch Guide: 48 Hours Running AI From My Phone](https://x.com/PawelHuryn/status/2036058594433519790) by **Paweł Huryn** (@PawelHuryn)
> 發佈日期：2026 年 3 月 23 日

---

Claude 現在有 4 種方式可以從手機上運行。大多數人會嘗試其中一種，碰壁後就放棄了。以下是真正有效的方法，以及它如何改變了我安排一天的方式。

Dispatch 是最新的介面——也是最有可能改變你身為 PM 工作模式的一個。不是因為它最強大，而是因為它把你一天中的每個空檔都變成了指揮真正工作的窗口。遛狗、喝咖啡、坐在副駕駛座、站在彈跳床旁邊看孩子——這些時刻都變得有生產力，但又不需要「隨時待命」。

我連續測試了 48 小時，用手機建立真正的 PM 工作流程。你正在讀的是我發現的一切——真正有效的工作流程、沒人提醒你的痛點，以及比 Anthropic 發布的任何單一功能都重要的架構洞見。

## 你會學到什麼

- Dispatch 到底是什麼——以及它和「手機上的 Claude 聊天」有什麼差別
- 如何設定並啟動你的第一個 Dispatch session
- 真實的 48 小時時間線——非同步指揮如何重塑你的一天
- 我遇到的每個坑，附上驗證過的解法
- 何時該用 Dispatch、Web Sessions、Channels 還是 Code
- 為什麼知識層比任何單一介面都重要
- PM 使用 AI 時，槓桿效應最高的單一投資是什麼

## 1. Dispatch 是什麼（和不是什麼）

Dispatch 不是「手機上的 Claude 聊天」。那個你已經有了。

Dispatch 是一個 **orchestrator**。從手機上的單一對話中，你可以啟動並管理多個同時在桌面上運行的 Cowork task session。每個 session 獨立運行——有自己的 context、自己的檔案存取權限、自己的 connector。

你的手機是指揮台。你的桌面負責重活。

想像傳訊息請人幫忙，和坐在控制室裡面對多個螢幕的差別。每個螢幕是一個在桌面上運行的 task session。你的手機從一條對話 thread 指揮所有 session。

**Phone → Orchestrator → Task Sessions → Desktop**

對 PM 來說：這映射到你已經在做的事——同時推進多個工作流。一個分析師在拉競品資料。另一個在起草 stakeholder 信件。第三個在整理研究筆記。Dispatch 就是這樣，只是分析師是你桌面上的 AI session，指揮台在你的口袋裡。

每個你在 Cowork 上設定的 connector——Gmail、Notion、Slack 等等——都能透過 Dispatch 運作，因為 Dispatch 是把任務委派給 Cowork。

## 2. 如何設定 Claude Dispatch

**Step 1：** 在桌面上設定好 Cowork，連接你使用的 connector——Gmail、Notion、Slack 等。所有東西必須先在桌面設定好。然後，保持桌面喚醒狀態並讓 Claude app 持續運行。

**Step 2：** 打開 Claude 手機 app。你會看到 Dispatch 分頁。開始對話並告訴它執行一個 Cowork 任務。

**Step 3：** 從小規模開始。一個任務（例如「摘要上週未回覆的信件」）。看看輸出。然後嘗試在同一個對話中跑兩個任務，感受並行工作流。

**Step 4：** 要處理檔案的話，需要授予資料夾存取權——自然地描述資料夾（「go to workspace/editor」）或使用你定義好的 shortcut。從放有 CLAUDE.md 和知識檔案的資料夾開始。

**Step 5：** 提早設定你的 workaround：

- 如果你的 workspace 透過 Google Drive/Dropbox/iCloud 同步，確認檔案能在手機上看到——這直接解決檔案傳輸問題
- 定義資料夾 shortcut（「editor mode」、「workspace」）這樣你就能自然地描述資料夾，而不用打路徑

## 3. 用 Claude Dispatch 實作真正的 PM 工作流（48 小時測試）

Dispatch 沒有填滿我的空檔時間。它**改變了我安排一天的方式**。我帶孩子去彈跳床樂園，因為我可以在旁邊非同步地指揮工作。模式不是「利用空檔硬幹」，而是「重新設計你的一天，因為工作不需要你坐在電腦前就能跑」。

以下是實際的樣子：

**早晨在家喝咖啡。** 孩子還沒起床。啟動 Task 1：「拉最新的競品動態，摘要上週以來的變化。」啟動 Task 2：「用 Notion 資料庫起草贊助商合作頁面。」兩個任務在另一個房間的桌面上跑。我在喝咖啡。

**遛狗。** 在手機上看 Task 1——競品摘要完成了。「加一個和我們目前 roadmap 的比較表。」重新指派只花 10 秒，單手操作。Task 2 還在跑。狗不在乎。

**老婆開車，我坐副駕。** 檢查贊助商 Notion 頁面草稿。「太正式了。拉上次活動的 engagement metrics，把 value prop 寫得更有力。」啟動 Task 3：文章草稿的 gap analysis。三個並行任務在家裡跑。我在看車窗外的風景。

**孩子在彈跳床樂園。** 站在旁邊，手機在手，45 分鐘。這是 infographic 迭代發生的地方。「把 icon 往左移。」「改第三區塊的顏色。」「標題加粗。」四輪視覺素材的 creative direction——在彈跳床旁邊完成的。

**回到書桌前。** 一切都在等我。Review、調整、出貨。所有這些空檔的實際指揮時間：大概總共 25 分鐘。Claude 並行執行的工作量：3 小時以上。

48 小時結束時，我請 Claude 摘要我們建造了什麼。

核心機制是：在單一 Dispatch 對話中，你啟動 Task 1（研究），然後立刻啟動 Task 2（起草），然後查看 Task 1 進度，重新指揮 Task 2，啟動 Task 3——全部從手機上的一條 thread。每個任務在自己的 sandboxed session 中運行。它們不互相干擾。除非你刻意橋接，否則不共享 context。

**對 PM 來說：** 你不會一次只做一件事。你在追蹤競品動態的同時等 stakeholder 回覆，同時準備 sprint 素材。Dispatch 讓 AI 配合這個節奏，而不是強迫你進入順序模式。瓶頸不是你的產出速度——而是你能同時指揮多少條並行軌道。

這裡是 session 摘要——60 多個 task session 從手機指揮，清楚劃分了我做的和 Claude 做的。

重點不是「通勤時用 Dispatch」。而是從手機非同步指揮，會重塑你安排一天的方式。有些產出我在書桌前 review 和精修。其他的——X 貼文、infographic、留言回覆——完全從手機出貨，從未坐下來。

## 4. Dispatch 的坑（48 小時測試驗證）

每個工具都有摩擦力。以下是 Dispatch 的摩擦力真正在哪裡——經過 48 小時測試，不是 48 分鐘。每個坑都有 workaround 或明確承認目前還沒解法。

### 4.1 資料夾存取需要打路徑

在桌面 Cowork 上，你點資料夾 icon 就能授予檔案存取權。在 Dispatch 上，手機端沒有資料夾選擇器 UI。你要打路徑：「Give yourself access to ~/Desktop/Workspace/Editor。」

**Workaround：** 自然地描述資料夾——「go to workspace/editor」就行。你也可以定義 shortcut，像「editor mode」，Claude 會跨 session 記住。不用記精確路徑。這個摩擦很快就消失了。

### 4.2 每個子任務各自需要資料夾存取

Dispatch 委派的每個子任務都會在桌面上請求資料夾存取。你需要逐一批准。沒有 workaround——這是安全模型的設計。把手機放口袋前記住這一點。

### 4.3 檔案傳輸還沒有內建

你無法從手機附加檔案到 Dispatch，Dispatch 也無法直接把產出送到手機。兩個問題，一個解法。

輸入端（手機截圖、照片），我開始把它們 email 給自己，然後讓 Claude 從 Gmail 撈。輸出端，我很早就注意到 Dispatch 無法把檔案送到手機——所以我開始看 Google Drive。

然後我靈光一閃：把 Cowork workspace 資料夾和 Google Drive 同步。Claude 存的所有東西自動出現在手機上。你從手機丟到那個資料夾的東西自動出現在桌面上。一個同步解決兩個方向。

### 4.5 CLAUDE.md 委派需要特定順序

如果你直接開始委派任務，orchestrator 可能會對子任務下達不精確的指令——它還沒內化你的規則。

**修正方法：** 委派任何任務之前，先請 Dispatch 讀你的 CLAUDE.md。一旦它載入你的規則，它為每個子任務撰寫的指令就會更精確。

## 5. Claude Dispatch vs. Remote Control vs. Web Sessions vs. Channels：何時用哪個

Anthropic 在 5 個月內發布了 4 個遠端存取介面，最後 3 個在大約 23 天內推出。以下是快速決策框架——不是完整深入分析，只是你現在需要的、選擇正確介面的資訊。

自從 Dispatch 在 X 上被討論以來，有兩點修正需要公開說明：

1. **Channels 是雙向的。** 它們把事件推送進正在運行的 session，也把回應透過 Telegram 或 Discord 送回。我之前的描述不夠精確。
2. **Channels 支援 scheduled tasks**——但僅限於活躍的 session 內。事件只在 session 運行時才會到達。

另外，有人注意到 Dispatch 也允許你運行 Code session。我測試了，目前還不太行。流程還沒完全串起來（Dispatch 自己的說法）。要做遠端 coding/prototyping，我推薦 Web Sessions。

## 6. 讓每個 Claude 介面都能複利的知識層

Anthropic 在大約 23 天內發布了三個新的遠端介面——Dispatch、Channels 和 Remote Control——加上十月的 Web Sessions。這是四種不用坐在書桌前就能跑 Claude 的方式。如果你的 AI 設定只活在單一介面裡，每出一個新的就意味著從頭開始。

而且他們更新的速度極快。就在昨天（週日）：Claude Code 兩個更新——7 天 `/loop` 和改進的 `/init` 指令。

**修正方法：** 把你的知識存在 GitHub repo 裡。CLAUDE.md（你的指令檔——語氣、規則、工作流程）加上知識檔案（模板、假設、研究）。每個介面都從你的機器讀取。Web Sessions 則 clone 它。

**介面會變。你的知識層會複利。**

想像一下：兩個 PM。同一家公司。同樣的 AI 工具。同樣的存取權限。一個在工具周圍建造知識系統——CLAUDE.md、skill library、每次使用都在進化的結構化工作流程。另一個只在卡住時才用。幾個月後，一個人的出貨速度是另一個的 5 倍。

完整的架構——CLAUDE.md 結構、知識檔案組織、自我修正迴圈——在 Self-Improving System 那篇文章裡。

## 7. PM 使用 AI 時，槓桿效應最高的投資

以下是那 48 小時的實際分工——誰貢獻了什麼：

| | 你 | AI (Claude) |
|---|---|---|
| **思考** | 90%——角度、立場、要說什麼 | 10%——提供選項讓你挑 |
| **觀點和立場** | 100%——每個立場都是你的 | 0%——我提案，你決定 |
| **研究** | 10%——你送連結、指出要研究什麼 | 90%——抓取、閱讀、綜合 |
| **格式化/執行** | 10%——你指定樣式修正 | 90%——HTML、infographic、檔案管理 |

看這個模式。思考：90% 是我。觀點和立場：100% 是我（Claude 可能有誇大）。研究和格式化：90% 是 Claude。AI 處理速度和廣度。我處理判斷和方向。

> 這是大多數人跳過的部分。用 AI 來放大你的思考，不是取代它。

寫作上，如果你在打開 Claude 之前沒有什麼要說的，你得到的就是精美的廢話。

對 PM 來說，道理相同。建造知識系統是一種投資。CLAUDE.md 檔案、skill library、工作流程模式——需要時間。但它們從你身上學習。你做的每次修正、加的每條規則、砍掉的每份草稿，都會複利成一個越用越銳利的系統。

現在——今年、明年——槓桿效應最高的單一投資不是學一個新的 AI 工具。而是建造判斷力、系統和領域知識，讓你成為更好的 orchestrator，不管下一個工具是什麼。

這篇文章中的每張 infographic 都是透過 Dispatch 創建、迭代和匯出的——從我的手機。

測試、觀點、什麼重要、和方向，都是我的。

開始建造。別再空想。

P.S. 如果你想要完整系統——CLAUDE.md 指令、知識架構、自我修正迴圈——那在 The Self-Improving Claude System 那篇文章裡。這篇給你 Dispatch。那篇給你讓 Dispatch 真正複利的系統。
