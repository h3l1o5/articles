---
title: "2026 年 Claude 發布的所有功能，以及如何真正使用它們"
date: 2026-03-28
categories: [AI]
tags: [Claude, Claude Code, Cowork, Anthropic]
image:
  path: /assets/img/posts/2026-03-28-everything-claude-shipped-2026/banner.jpg
  alt: "everything claude has shipped in 2026 and how to actually use it"
---

> 原文：[everything claude has shipped in 2026 and how to actually use it](https://x.com/kloss_xyz/status/2036356467629162772) by **klöss** (@kloss_xyz)
> 發佈日期：2026 年 3 月 24 日

---

Anthropic 的發布節奏快到連大多數 power user 都跟不上。幾乎每天都有新版本，從一月起大約每兩週就有一次重大發布。新模型、新工具、新整合，還有全新的產品類別。如果你在錯誤的時間眨了眼，或是休了幾週假，你錯過的可能比你想承認的還多。而 Claude 確實影響並改變了你的工作方式。句號。

這是一份完整指南。截至 2026 年 3 月 23 日，Claude 上每一個你現在可以使用的主要功能。如何實際設定每一項、何時使用什麼，以及那些把這些工具從「覺得很酷」變成「真正圍繞它們重建工作流程」的 best practices。

你會想要把這篇收藏起來，之後再回來看。分享給你的團隊或朋友。這是我當初開始用時就希望存在的參考文件。

## 模型：你正在使用的東西

Claude 4.6 家族有三個模型層級。

![Everything Claude Shipped in 2026 timeline](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/models.jpg)

以下是每個模型的用途和使用時機：

**Claude Opus 4.6** 是天花板。2026 年 2 月 5 日推出，擁有 100 萬 token context window（定價變更稍後會提）。在 1M token 的 MRCR v2 上得分 78.3%，是同長度下 frontier model 中最高的。在法律、金融和程式任務的 benchmark 中稱霸。Anthropic 報告的任務完成時間為 14.5 小時，是 frontier model 中最長的。API 定價為每百萬 token $5/$25。最大 128K output token。支援 adaptive thinking，新增 "max" effort level 以達到最高能力。

**何時使用 Opus：** 跨大型 context 的複雜分析、codebase 重構、深度研究、高風險交付物、嚴肅的內容製作——任何品質比成本更重要的場景。

**何時不用 Opus：** 任何你一天會跑好幾次的東西。以每百萬 token $5/$25 的價格，重度 Opus 工作流一天可能燒掉 $50-100。預設用 Sonnet，只在 Sonnet 產出不夠好時才升級到 Opus。

**Claude Sonnet 4.6** 在僅僅 12 天後的 2 月 17 日推出。這是大多數人應該預設使用的模型。100 萬 token context window（3 月 13 日起 GA）。在 coding、computer use、長 context 推理、agent 規劃、知識工作和設計方面全面升級。早期測試者大約 70% 的時間更偏好它勝過 Sonnet 4.5。用戶在 59% 的情況下選擇它而非前代旗艦 Opus 4.5。claude.ai 上 Free 和 Pro plan 的預設模型。每百萬 token $3/$15。最大 64K output。比 Sonnet 4.5 快 30-50%。

**何時使用 Sonnet：** 日常工作、快速草稿、標準 coding 任務、需要速度但不想犧牲太多智能的 agent workflow。它在許多辦公任務上與 Opus 相當（Anthropic 的 OfficeQA benchmark 顯示它在某些方面甚至超越 Opus），成本卻低約 40%。

**Claude Haiku 4.5** 是面向 API 開發者的快速、便宜選項，適合高流量 pipeline 和你想要低成本唯讀 worker 的 subagent 模型分配。一個重要注意事項：Haiku 沒有 prompt injection 防護。如果你在 agentic 設定中讓它處理不受信任的輸入，部署前請仔細閱讀文件並了解風險。

### 標準定價的 1M Context Window

之前，超過 200K token 的請求會以溢價計費（Opus 每百萬 $10/$37.50）。從 3 月 13 日起，這個加價完全取消了。一個 900K token 的請求現在與 9K 的每 token 費率相同。沒有乘數、沒有附帶條款、不需要 beta header。

大約 750,000 個字的 context。整個 codebase。完整的法律合約。大量的資料集。數月的文件。全部同時保持在 working memory 中。媒體限制也跳升到每個請求 600 張圖片或 PDF 頁面，比之前的 100 增加了 6 倍。在 Claude Platform、Microsoft Foundry 和 Google Cloud Vertex AI 上皆可使用。

之前必須分塊 context、建構摘要 pipeline、管理 rolling window 的團隊，現在可以直接載入所有東西。有公司報告，將 context 從 200K 提高到 500K 實際上減少了總 token 使用量，因為模型花更少時間重新讀取和重新處理之前的資訊。

## 該使用哪種 Claude 模式

Claude 有四種模式，大多數人只知道一種。以下是快速說明：

![Claude's Four Modes](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/models-2.jpg)

**Chat** 是你可能已經知道的瀏覽器/行動介面。適合快速提問、腦力激盪、寫草稿。每次對話從空白開始。一直是你在主導。

**Cowork** 是桌面 agent。它讀寫你實際的檔案、自主執行多步驟任務，並將完成的工作交付到你的資料夾。當你想要委派工作而不是進行對話時使用。

**Code** 是開發者工具。在你的終端機中運行，看見你的 codebase，寫程式碼、執行命令、管理 git。如果你寫程式碼，這是最深槓桿效果所在。

**Projects** 是保存的工作空間，你上傳檔案和指令一次就好。該 project 中的每個新對話都以完整 context 開始。用於定期性工作，如每週 newsletter 或客戶交付物，context 在 session 之間不太變化。

**快速規則：** Chat 用於快速問題。Cowork 用於在你的檔案上做真正的工作。Code 用於開發。Projects 用於有穩定 context 的重複性工作。

### Memory 和個人化

截至 2026 年 3 月 2 日，所有用戶（包括免費版）都可以使用 Claude 從聊天歷史中產生的 memory。Claude 記住你對話中的相關 context，並產生跨 session 攜帶的 memory 摘要。你可以在 Settings > Capabilities 中查看、編輯和刪除 memory。你也可以匯入和匯出你的完整 memory，這在你實驗變更前備份或將 context 轉移到新帳號時很有用。無痕模式讓你可以將特定對話排除在 memory 之外。

**該做的事：** 現在就去 Settings > Memory 看看 Claude 已經記住了關於你的什麼。編輯任何錯誤或過時的內容。加入它應該知道的 context。你的 memory 設定檔越準確，你在 session 間重複自己的次數就越少。

Cowork session 不會在 session 之間攜帶 memory。你的 context 檔案是替代方案（在「限制」部分會詳述）。

## Claude Cowork：知識工作者的作業系統

Cowork 改變了遊戲規則。它於 1 月 12 日作為 research preview 為 macOS 上的 Claude Max 訂閱者推出。1 月 16 日擴展到 Pro，1 月 23 日到 Team 和 Enterprise。Windows 隨後跟進。投資者嚇壞了。SaaS 股票在幾天內市值蒸發數千億美元。華爾街看到了即將到來的東西。

別再把這個想成聊天介面了。Cowork 是任務委派。你描述「完成」是什麼樣子。Claude 制定計畫、拆解成子任務、在你的實際電腦上自主執行，然後將完成的檔案交付到你的資料夾。你走開。你回來看到已完成的工作。

Anthropic 用大約 10 天時間，完全使用 Claude Code 建構了 Cowork。

### 改變一切的設定

那些在 Cowork 上掙扎的人，每次任務都寫冗長、詳細的 prompt，得到不一致的結果。那些搞懂的人，花了一個下午建構他們的 context 設定（context 檔案、global instructions、資料夾結構），然後用 10 個字的 prompt 就能產出客戶級交付物。

ChatGPT 訓練你寫更好的 prompt。Cowork 獎勵更好的檔案。一個是隨著模型改進而越來越不那麼有用的技能。另一個會複利累積。

**第一步：建立你的工作空間資料夾。**

在你的電腦上為 Cowork 建立一個專用資料夾。不要把它指向你的整個 Documents 目錄。如果出了問題（可能會），你希望損害被限制住。Cowork 對你分享的任何資料夾都有真正的讀寫權限。

![Cowork Folder Architecture](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/cowork-features.jpg)

```
CLAUDE COWORK/
├── CONTEXT/        # 你的身份和偏好（.md 檔案）
├── PROJECTS/       # 進行中的工作，每個專案一個子資料夾
├── TEMPLATES/      # 可重複使用的驗證過結構
└── OUTPUTS/        # Cowork 交付完成工作的地方
```

這保持了組織性並限制了 Claude 可以看到的範圍。每個 power user 的每份指南都收斂到這個相同的基本結構。資料夾名稱不重要。區隔才重要。

**第二步：建立你的 context 檔案。**

這是消滅「通用 AI 產出」問題的步驟。在你的 CONTEXT 資料夾中建立三個 markdown 檔案：

- **about-me.md** 是你是誰、你做什麼、你目前的優先事項。不是你的履歷。是你每天實際在做什麼、服務誰、什麼事情現在重要。包含一兩個你引以為傲的工作範例。
- **brand-voice.md** 是你如何溝通。語調描述詞、你使用的詞、你絕不會用的詞、格式偏好，以及 2-3 段你實際寫作的參考樣本。這是區分「通用 AI 產出」和「這真的聽起來像我」的關鍵。
- **working-preferences.md** 是 Claude 應該如何表現。開始前先問問題。執行前展示計畫。未經確認不要刪除檔案。預設輸出格式。品質標準。要避免的事項。

這三個檔案一夜之間消除了冷啟動問題。沒有它們，每次 session 都從零開始。有了它們，Claude 在每次 session 開始時就已經知道你的聲音、你的標準和你的偏好。

**大多數人忽略的關鍵洞察：** 這些檔案會複利累積。每週改進它們。每當 Claude 產出你不喜歡的東西，問自己這是 prompt 問題還是 context 問題。十之八九是 context。在一個檔案中加一行。永久修復。

**第三步：設定 Global Instructions。**

前往 Settings > Cowork > Edit Global Instructions。

Global Instructions 在所有東西之前載入。在你的檔案之前。在你的 prompt 之前。在 Claude 甚至看你的資料夾之前。它們是適用於每一個 session 的基準行為。

以下是一個起始模板：

```
I'm [Name], [Role]. Before starting any task, read my context files
first. Always ask clarifying questions before executing. Show a brief
plan before taking action. Default output: .docx. Never delete files
without my explicit approval. If confidence is low, say so.
```

這意味著即使你最懶、最匆忙的 prompt 仍然產出校準過的輸出。Claude 永遠知道你是誰。永遠先讀正確的檔案。永遠在猜測前先問。你的 prompt 只需處理特定任務。

**第四步：學會 AskUserQuestion。**

![Cowork Setup in 5 Minutes](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/claude-code.jpg)

這個功能翻轉了整個互動模型。不是你去設計完美的 prompt，而是 Claude 設計完美的問題。

當你在任何 prompt 中加入 "Start by using AskUserQuestion"，Cowork 會產生一個互動表單。選擇題。可點擊的選項。具體的替代方案。一個結構化的 prompt 幫助你在 Claude 開始執行前想清楚你實際想要什麼。

你不再從頭寫冗長、精心設計的 prompt。你讓 Claude 弄清楚它需要知道什麼。如果第一輪問題沒有讓你們對齊，你就說出來。它會產生新的一組問題，然後你們迭代。

適用於幾乎所有場景的通用 prompt 模板：

```
I want to [TASK] to [SUCCESS CRITERIA].
First, explore my CLAUDE COWORK folder. Then ask me questions
using AskUserQuestion. I want to refine the approach with you
before you execute.
```

就這樣。那個模板加上你的 context 資料夾處理了 80% 的用例。工作流程永遠相同。只有 context 改變。

### Cowork 已發布的每個功能

**Connectors**

2 月 24 日發布。Claude Cowork 連接到 Google Drive、Gmail、DocuSign、FactSet、Google Calendar、Slack 等等。這些不是淺層的整合。Claude 可以自主瀏覽你的 Drive、拉取文件、跨來源綜合資料、根據找到的內容起草郵件，以及在合約中標記問題。一旦連接，Claude 可以在每次 session 中引用該工具的即時資料。不需要複製貼上、截圖或下載。

前往 Settings > Connectors。瀏覽目錄（50+ 整合）。點擊 "Add"。認證。完成。你只需做一次。Connectors 在所有方案（包括免費版，自 2 月 24 日起）都是免費的，而且可能是 Cowork 中最被低估的功能。

連接 Slack 後試試：「搜尋我過去 7 天的 Slack 訊息，給我一個需要跟進事項的摘要。按緊急度組織。」

連接 Google Drive 後：「在我的 Drive 中找到最近關於 [專案名稱] 的文件。閱讀它並告訴我三件最重要的事。」

連接 Google Calendar 後：「查看我這週的行事曆，找出任何會議衝突，並為最低優先級的那個起草改期郵件。」

**Plugins 和 Marketplace**

2 月 24 日發布。針對特定職務的預建 plugins。每個 plugin 將 skills、slash commands 和 connectors 打包成角色專屬套件。Anthropic 發布了涵蓋 Sales、Marketing、Legal、Finance、Data Analysis、Product Management、Customer Support、Enterprise Search、Engineering、HR、Operations、Design、Brand Voice、Bio Research 等的官方 plugins。

從左側欄的 Customize 選單 > Browse plugins 安裝。點擊 Install。在任何對話中輸入 `/` 查看可用的 slash commands。

大多數人應該先安裝的 plugins：

- **Productivity** 處理任務、行事曆和日常工作流。輸入 `/productivity:start`，Claude 會檢視你的一天。
- **Data Analysis** 讓你丟一個 CSV，輸入 `/data:explore`，Claude 會摘要欄位、標記異常、建議分析，並用白話寫 SQL。
- 然後安裝一個符合你工作的角色專屬 plugin。`/marketing:draft-content` 拉取你的品牌聲音並起草內容。`/sales:call-prep` 研究客戶並準備談話要點。`/legal:review` 閱讀合約並標記風險條款。

團隊可以建立私有 marketplace，透過 admin 控制為 Team 和 Enterprise plan 在組織內分發自訂 plugins。建構一次。部署給數百人。Anthropic 也為社群建構的 plugins 推出了公開 Marketplace 和 Ambassadors 計畫，所以生態系正在超越第一方的範疇成長。

你也可以為你的特定公司客製化每個 plugin。安裝後，告訴 Claude「customize the [plugin-name] plugin for me based on my company」。Claude 會問你關於你的工作流、術語和偏好的問題，然後記住答案用於該 plugin 的每個未來 session。這把一個通用的 Sales plugin 變成一個了解你的 ICP、定價層級和外展風格的專屬工具。

**排程任務**

2 月 25 日發布。告訴 Claude 一次，讓它定期做某件事。早晨郵件摘要。週五指標拉取。每週競爭情報簡報。只要你的電腦醒著且 Claude Desktop 開著，它就自動運行。

多位 power user 設定的真實範例：

```
Every Monday at 7am, research [competitor names] for news, product
updates, or pricing changes from the past 7 days. Check [industry
publications] for relevant coverage. Save a summary brief to
/competitive-intel/YYYY-MM-DD-weekly-brief.md. Flag anything that
directly impacts our positioning.
```

你在週一醒來就有一份簡報等你閱讀。結合 connectors，排程任務變成真正自主的。「每週一，從 #product-feedback 拉取所有未讀的 Slack 訊息，按主題分類，然後在 Google Drive 中建立摘要。」排程任務運行。Connector 拉取即時資料。Claude 處理它。產出出現在你的資料夾中。

這也伴隨著 Claude Desktop 中新的 Customize 區塊一起發布，將 skills、plugins 和 connectors 集中在一個地方。

**Dispatch**

![Dispatch: Phone to Desktop](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/claude-code-2.jpg)

3 月 17 日發布。手機到桌面的橋樑。Pro 和 Max 訂閱者可用。你透過 Claude Desktop 或 Claude iOS/Android 存取一個持久的 agent thread，從任何地方管理 Cowork 中的任務。

設定很簡單：打開 Claude Desktop > Cowork > 在左側欄點擊 Dispatch。打開 "Keep awake"（不打開的話，你的電腦睡眠時 Dispatch 就會停止）。然後打開 Claude 行動 app > 在側欄點擊 Dispatch。

一個跨裝置同步的連續對話。你在火車上告訴 Claude 從你桌上的三個試算表編譯一份報告。到辦公室時，它已經完成了。在一條 Dispatch 訊息中堆疊多個任務，你不在的時候 Claude 會依序完成它們。

**來自 Product Compass 指南的關鍵細節，大多數人都漏掉了：** Dispatch orchestrator 不會讀你的 CLAUDE.md。它根據假設來擬定任務 prompt。子任務會讀，但 prompt 已經帶有不精確的框架。修復方法：在你的 Dispatch 訊息中加入 "read CLAUDE.md"。

你不能從行動端新增 connectors。出門前在桌面上設定好 Gmail、Slack、Notion。Dispatch 會繼承它們全部。你也還不能從行動端附加檔案。變通方法：把檔案寄給自己，然後告訴 Dispatch 透過 Gmail connector 拉取。

**Projects**

3 月 20 日發布。將相關任務組織成持久的工作空間，擁有自己的檔案、連結、指令和 memory。匯入現有資料夾或從零開始。這意味著你可以有一個「Q1 財務報告」的 project 和另一個「產品發布素材」的 project，Claude 會記住每個的 context。

Projects 讓 Cowork 從一次性的 agent session 變成持久的工作空間。這對研究密集型工作很重要，因為你不斷在對話間失去 context，不得不一遍又一遍重述你的目標。

**Computer Use**

3 月 23 日發布（今天）。Research preview，僅限 macOS，面向 Claude Pro 和 Max 訂閱者。在 Cowork 和 Claude Code 中都可使用。Claude 現在可以指向、點擊和瀏覽你的螢幕。它打開 app、使用瀏覽器、填表單，並操作你電腦上的任何工具。當有直接的 connector（如 Slack 或 Google Calendar）時，Claude 會先使用那個。沒有的時候，它使用你的滑鼠和鍵盤。

Claude 在採取重大動作前會請求許可。Anthropic 仍建議作為預防措施不要用於敏感資訊。最大的風險要注意：透過螢幕內容的 prompt injection。如果 Claude 導航到不受信任的網站，頁面內容會進入 context window 並可能影響 Claude 的行為。堅持使用受信任的應用程式和已知網站。搭配 Dispatch，你可以從手機告訴 Claude 做一些需要操作你的桌面、瀏覽器或任何還沒有 connector 的應用程式的事情。

**Chrome 中的 Claude**

Chrome 擴充功能讓 Claude 閱讀頁面、點擊元素、填寫表單，並在你身邊導航網站。大多數人錯過的功能：

- 透過展示 Claude 步驟一次來錄製工作流程，它就學會重複執行。任何你一週做超過兩次的重複性瀏覽器任務都應該變成錄製的工作流程。
- Claude Code 整合讓你在終端機中建構並同時在瀏覽器中測試。擴充功能讀取 console 錯誤、network 請求和 DOM 狀態，所以當你的前端壞掉時，Claude 在你問之前就已經知道原因了。
- 你可以從 Claude Desktop 控制瀏覽器動作而不需要切換視窗。
- Team/Enterprise 的管理員可以使用允許清單和封鎖清單來組織範圍內管理擴充功能的網站存取權。

**實用範例：** 錄製一個每週檢查競爭對手定價頁面的工作流程。Claude 導航到每個網站、擷取定價資料，並放入你 Cowork 資料夾中的比較試算表。以前需要 45 分鐘的點擊變成一鍵重播。

對你給予存取的網站要謹慎。網頁內容是 prompt injection 的主要載體。限制存取只給受信任的網站。

### 你今天就能執行的真實用例

**整理數月累積的檔案。** 把 Cowork 指向一個有 6 個月傾倒檔案的資料夾。收據、合約、筆記、截圖。

```
Organize all files in this folder into subfolders by type (receipts,
contracts, notes, images). Use the format YYYY-MM-DD-descriptive-name
for all filenames. Create a summary log documenting every change. Don't
delete anything. If a file could belong to multiple categories, put it
in /needs-review.
```

Claude 讀取每個檔案、分類、用日期重新命名、建立結構、給你一份日誌。10 分鐘而不是 2 小時。

**從原始素材產出客戶交付物。** 你有會議筆記、逐字稿和一些研究連結。你需要一份精美的報告。

```
Using the files in /client-a/raw-materials, create a client-ready
report. Include executive summary, key findings, recommendations, and
next steps. Match the format of the template in /templates/.
Ask me questions first.
```

Claude 讀取你所有的原始素材、綜合成結構化報告、配合你的模板格式、存好準備發送。15 分鐘而不是 90 分鐘。

**自動化的每週研究簡報。** 為競爭情報設定一個排程任務。每週一早上 7 點，Cowork 研究競爭對手、查看產業出版物，並存一份格式化的簡報。你準備好時再看。結合 connectors 從 Slack、Gmail 和 Drive 拉取即時資料。

**財務建模。** 有位創作者請 Cowork 建構一個社群媒體出場估值模型。它自己規劃、自己找到公式錯誤、自己修復，並交付了一份華爾街級的 Excel 檔案，包含 129 個公式、四種估值方法。混合估值範圍包括收入倍數、EBITDA 倍數、受眾/訂閱者價值，以及 5 年 DCF。老實說，這挺瘋狂的。

### 誠實的限制

**Cowork 消耗用量很快。** 一個複雜的 session 可以燒掉通常等同於數十次一般聊天對話的量。在 Pro plan（$20/月）上，每天使用的話一週內你就會感受到限制。社群報告顯示，重度 Cowork 用戶在 Pro 上 3-4 天就會觸及速率限制——如果你正在處理重要的事情中途被斷，這老實說很殘酷。涉及檔案讀取、文件建立和平行子任務的多步驟任務是計算密集的。如果 Cowork 成為你的主要工作流，Max（$100/月的 5x Pro 用量，或 $200/月的 20x）可能是正確的選擇。在被中途截斷前，監控 Settings > Usage 看你目前的狀況。

**長 session 會遇到 context compaction。** 當 Cowork session 接近 context 限制時，Claude 自動摘要對話的早期部分以騰出空間。這讓 session 存活，但你失去了早期內容的細節。數字被四捨五入，特定的檔案引用變得模糊，你早期做的決定被壓縮成摘要。如果你注意到 Claude 開始引用「典型模式」而不是它之前讀過的特定檔案，compaction 已經發生了。是的，這跟聽起來一樣煩人。對於關鍵細節，告訴 Claude 將重要發現寫入一個你之後可以引用的檔案。這樣資訊即使在 context 被壓縮時也能持續存在。

**它仍然是 research preview。** Anthropic 對此很明確。它可能誤讀檔案。它有時在更簡單的方法就行的時候採取奇怪的方法。在複雜的多步驟任務中，agents 大約 10% 的時間會走偏，最終產出有一個部分與其餘不匹配。在發送任何東西給客戶之前，檢查它產出的內容。

**沒有跨 session memory。** 每個新的 Cowork session 都完全從零開始。不知道你是誰，不記得你昨天討論了什麼。這是最大的摩擦點——直到你發現 context 檔案就是變通方案。這就是為什麼設定如此重要。你的偏好存在檔案中。你的專案計畫存在文件中。你的標準存在指令中。如果你要連續性，就把它建構到檔案裡。但好處是巨大的：一個文件化完善的工作流是可攜帶的、可分享的、有版本控制的。

**關閉 app 任務就停止。** Cowork 作為 Claude Desktop 中的活動 session 運行。關閉視窗，任務就停止。改用讓電腦睡眠。Session 能撐過那個。

**僅限桌面。** 沒有行動版 Cowork、沒有瀏覽器版本、沒有跨裝置同步（Dispatch 部分橋接了這個問題但不完全一樣）。在雲端同步的資料夾（iCloud、Dropbox、OneDrive）中建構你的 context 檔案，這樣至少你的檔案在不同機器間是一致的。

## Claude Code：開發者的作業系統

如果 Cowork 是知識工作者的工具，Claude Code 就是開發者的。

Claude Code 始於 2025 年 2 月的命令列 coding 工具。它現在是一個完整的可擴展平台，用於在你整個開發工作流程中編排 AI agents，拉動 25 億美元的年化收入。

你透過 npm 安裝（`npm install -g @anthropic-ai/claude-code`），cd 到你的專案，輸入 `claude`，你就在跟一個能看到你整個 codebase 並實際對它做事的 agent 對話。讀取檔案、寫入檔案、執行命令、搜尋網路、執行測試、commit 程式碼，應有盡有。claude.ai 上也有網頁版，在二月獲得了重大升級，包括多 repo session、更好的 diff 和 git status 視覺化，以及 slash commands。但終端機版本仍然是最深層功能所在。

但 coding 本身不是讓 Claude Code 與其他一切區別開來的東西。**擴充層** 才是把它從一個花俏的 autocomplete 變成可配置平台的關鍵。

### CLAUDE.md：你的專案指令手冊

當你開始一個 session 時，Claude 做的第一件事就是讀你的 CLAUDE.md。它直接載入 system prompt 並在整個對話中保持在 mind 裡。你在這裡寫什麼，Claude 就遵循什麼。

大多數人要麼完全忽視它，要麼塞了一堆垃圾進去，讓 Claude 變得更差而不是更好。有一個閾值，太多或太少的資訊意味著更差的產出。

**該放什麼：**
- Build、test 和 lint 命令（實際重要的 bash 命令）
- 關鍵架構決策（「我們使用 Turborepo 的 monorepo」）
- 非顯而易見的陷阱（「TypeScript strict mode 開著，unused variables 是 error」）
- Import 慣例、命名模式、錯誤處理風格
- 主要模組的檔案和資料夾結構

**不該放什麼：**
- 任何屬於 linter 或 formatter config 的東西
- 你已經可以連結到的完整文件
- 解釋理論的長段落
- 保持在 200 行以下。超過的檔案開始吃太多 context，Claude 的指令遵循度實際上會下降，因為它在跟 Claude Code 自己的 system prompt 競爭注意力

告訴它為什麼，不只是什麼。「Use TypeScript strict mode」還行。「Use TypeScript strict mode because we've had production bugs from implicit any types」更好。為什麼給了 Claude context 來對你沒預料到的判斷做決定。

不斷更新它。工作時按 `#`，Claude 會自動將指令加到你的 CLAUDE.md 中。每次你在同一件事上糾正 Claude 兩次，那就是一個它應該在檔案中的信號。隨著時間它變成一份你的 codebase 實際如何運作的活文件。

**差的 CLAUDE.md** 看起來像寫給新人的文件。**好的 CLAUDE.md** 看起來像如果你知道明天會失憶，你會留給自己的筆記。

### CLAUDE.md 層次結構

大多數人完全沒注意到這個。不是只有一個 CLAUDE.md。有四層在 session 開始時合併：

1. **Managed Policy（組織範圍）：** IT 部署。不能被覆蓋。組織範圍的規則。
2. **~/.claude/CLAUDE.md（全域）：** 你跨所有專案的個人偏好。不受版本控制。
3. **./CLAUDE.md（專案根目錄）：** 團隊指令。提交到 git。每個 clone repo 的人都會得到這些。
4. **CLAUDE.local.md（個人專案覆蓋）：** 你對這個特定專案的個人調整。自動被 gitignore。

有衝突時更高層級勝出。這個分層是讓 Claude Code 對團隊（而不只是個人）有效的原因。

**最常見的團隊問題：** 一個開發者把指令放在他們的 user level config（`~/.claude/CLAUDE.md`）而不是 project level 檔案。他們得到完美的行為。一個新團隊成員 clone 了 repo，沒有那些 user-level 指令，得到不一致的結果。每個人都怪模型，但實際的問題是配置。我看過一個團隊花了兩天 debug「隨機的 Claude 行為」，然後才有人發現是資深開發者的個人 config 在做所有的重活。永遠把團隊標準放在 project-level 檔案中。

### Rules 目錄：可模組化且可擴展的指令

一旦你的 CLAUDE.md 變得擁擠（它會的），就把指令拆分到 `.claude/rules/` 檔案中。這個目錄中的每個 markdown 檔案都會和你的 CLAUDE.md 一起自動載入。

```
.claude/rules/
├── code-style.md
├── testing.md
├── api-conventions.md
└── security.md
```

每個檔案保持聚焦。負責 API 慣例的人編輯 `api-conventions.md`。負責測試的人編輯 `testing.md`。沒有人互相踩踏。

**Path scoped rules** 是真正回報的地方。加入帶有 glob 模式的 YAML frontmatter，rules 只在 Claude 處理匹配的檔案時啟用：

```yaml
---
paths:
  - "**/*.test.tsx"
---
# Test Conventions
Use React Testing Library, not Enzyme. Test behavior, not implementation.
Always include at least one integration test per component.
Mock API calls with MSW, never with jest.mock on fetch.
```

這捕捉所有測試檔案，不論目錄。目錄級的 CLAUDE.md 檔案只適用於該目錄中的檔案。對於必須跨 50+ 目錄的測試檔案應用的慣例，path specific rules 是正確的方法。它們也減少 token 使用量，因為它們只在相關時載入。

### Commands vs Skills vs Agents

![Commands vs Skills](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/api.jpg)

這三種擴充類型的運作方式不同。用錯的會造成摩擦。

**Commands**（`.claude/commands/`）是你手動觸發的 slash commands。一個名為 `review.md` 的檔案變成 `/project:review`。寫 markdown 加指令。`!` backtick 語法執行 shell 命令並在 Claude 看到 prompt 之前嵌入輸出。

```markdown
---
description: Review current branch diff before merging
---
## Changes to Review
!`git diff --name-only main...HEAD`

## Detailed Diff
!`git diff main...HEAD`

Review for code quality, security vulnerabilities, missing tests,
and performance concerns. Give specific, actionable feedback per file.
```

現在 `/project:review` 自動將真實的 git diff 注入 prompt。使用 `$ARGUMENTS` 傳遞參數：`/project:fix-issue 234` 直接餵入 issue 234 的內容。

Project commands（`.claude/commands/`）被提交和共享。Personal commands（`~/.claude/commands/`）顯示為 `/user:command-name`。

**Skills**（`.claude/skills/`）是 Claude 在任務匹配時自己呼叫的工作流程。你不需要輸入 slash command。Claude 讀取 skill 的 description，辨識出當前任務匹配，然後自動啟用。Commands 等你。Skills 觀察正確的時機並自己行動。

Skills 是資料夾，不是單一檔案。它們可以包含 scripts、參考文件、資料、模板。一個帶 YAML frontmatter 的 SKILL.md 定義觸發條件：

```yaml
---
name: security-review
description: Comprehensive security audit. Use when reviewing code for
  vulnerabilities, before deployments, or when the user mentions security.
allowed-tools: Read, Grep, Glob
---
```

Skills 現在也支援 effort frontmatter，讓你在呼叫時覆蓋模型的 thinking effort level。它們還支援只在 skill 被呼叫時啟用的 on-demand hooks。`/careful` 封鎖破壞性命令。`/freeze` 封鎖特定目錄外的編輯。

Anthropic 自己的工程團隊在內部使用了橫跨九個類別的數百個 skills：library/API reference、product verification、data fetching、business process automation、code scaffolding、code quality review、CI/CD deployment、runbooks 和 infrastructure operations。3 月 7 日，他們在 GitHub 的 `anthropics/skills` 開源了其中 17 個，涵蓋 creative design、document creation、technical development 和 enterprise communication。任何 skill 中最有價值的內容是 gotchas section。從曾經燒到你的東西來建構它。

**Agents**（`.claude/agents/`）是擁有自己 system prompt、工具存取權和模型偏好的特化 subagent personas。

```markdown
---
name: code-reviewer
description: Expert code reviewer. Use PROACTIVELY when reviewing PRs.
model: sonnet
tools: Read, Grep, Glob
---
You are a senior code reviewer focused on correctness and maintainability.
Flag bugs, not just style issues. Suggest specific fixes.
```

tools 欄位限制 agent 能做什麼。一個安全稽核者只得到 Read、Grep 和 Glob。沒有寫入權限。這是故意的。model 欄位讓你對聚焦任務使用更便宜的模型。Haiku 能很好地處理大多數唯讀探索。

![Subagent Architecture](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/hooks.jpg)

Subagents 保持你的主 context 乾淨。主 agent 的 context window 會被探索填滿。Subagents 在隔離的 context 中做髒活，然後返回壓縮的發現。你的主對話只得到答案。

### Tasks

1 月 22 日發布。Anthropic 將舊的 Todos 系統升級為 Tasks，一個正式的專案管理原語。Tasks 有相依性，存儲在檔案系統（`~/.claude/tasks`）中，多個 subagents 或 sessions 可以在同一個任務清單上協作。當一個 session 更新一個 task，變更會廣播到所有在該清單上工作的 sessions。將任務清單設為環境變數並啟動平行 agents 透過它協調。這是多 session 工作流的支柱，也是 Agent Teams 保持組織的方式。

### Agent Teams

2 月 5 日隨 Opus 4.6 作為實驗性功能一起發布。subagents 是向 coordinator 回報的隔離 worker，而 Agent Teams 是協作小隊。Teammates 透過基於 inbox 的系統直接互相傳訊，透過帶有相依性追蹤的共享任務清單分配工作，並平行協調。

最多 10 個同時的 teammates。一個 lead session 協調工作、分配任務並綜合結果。Teammates 獨立工作，每個在自己的 context window 中。不像 subagents，teammates 可以分享發現、互相挑戰，並在不經過 lead 的情況下協調。

用 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 在 settings.json 中啟用。

**真正閃耀的地方：** 假設你在加一個觸及 API、前端和測試套件的新功能。啟動三個 teammates。一個處理 API endpoint。一個建構 React 元件。一個寫整合測試。他們透過共享任務清單協調，所以測試 teammate 知道要測試什麼 endpoints 和元件。三個都平行工作而不是循序。一個 session 需要 90 分鐘的任務在 30 分鐘內完成。

它們增加了協調開銷並且消耗的 tokens 明顯多於單一 session。在平行探索增加真正價值且 teammates 能獨立運作時使用。對於循序任務或同一檔案的編輯，堅持用單一 session 或 subagents。

### Remote Control

2 月 24 日為 Max 用戶發布，後來擴展到 Pro。Channels 的前身。在你的終端機中執行 `claude rc`，然後在另一台裝置上打開 Claude 行動 app 或 claude.ai 繼續遠端控制那個 session。你在桌面上開始一個任務，走開，然後從手機繼續操控它。Channels（下面會提到）對大多數用例取代了它，因為加入了 Telegram 和 Discord 作為介面，但 Remote Control 仍然是不需要設定 messaging bot 就能實現手機到終端機存取的最簡單方式。

### Claude Code Channels：永遠在線的訊息傳遞

![Channels: Always-On Messaging](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/security.jpg)

3 月 20 日作為 research preview 發布。如果你曾經想從手機傳訊息給你的 AI agent 並讓它在你的本機上執行，這就是了。

Channels 將運行中的 Claude Code session 掛到 Telegram 或 Discord。從你的手機發送訊息。Claude 使用你完整的本地開發環境（檔案、工具、git，一切）處理它。透過同一個聊天 app 回覆。你的 session 在背景中待命。事件從外部世界進來。Claude 帶著完整的專案 context 處理每一個。你在終端機前的存在是可選的。

這與讓 OpenClaw 在 2025 年 11 月發布後爆紅的互動模型相同。一個你可以透過常見聊天 app 24/7 傳訊的持久 AI worker。除了 Channels 是 Claude Code 原生的，由 Anthropic 的安全基礎設施支撐，並建構在 MCP 上所以架構是可擴展的。

**設定：**

```bash
# 安裝 Telegram plugin
/plugin install telegram@claude-plugins-official

# 用你的 bot token 配置（從 BotFather 取得）
/telegram:configure <YOUR_BOT_TOKEN>

# 用 channels flag 啟動
claude --channels plugin:telegram@claude-plugins-official
```

打開 Telegram，向你的 bot 發送任何訊息。它會回覆一個配對碼。使用 `/telegram:access pair <code>` 完成連結。配對將 bot 鎖定到你的特定 user ID，所以沒有其他人可以控制你的 session。

Discord 遵循相同的模式，有自己的 plugin。

目前限制：你需要保持一個終端機 session 開著（搭配 tmux、screen 或背景程序）。research preview 期間只有 Anthropic 核准的 plugins。權限審批仍需要終端機存取。但 plugin 架構是為擴展而建的。Slack、WhatsApp 和 iMessage 都已被要求，而且建構自訂 channels 的文件已經公開。

### Hooks

在特定生命週期事件自動觸發的 shell 命令。Pre-commit、post-tool-call、當 Claude 嘗試編輯某些檔案時。這些不是關於 AI 智能。它們是關於確定性控制。

自動 lint Claude 寫的每個檔案。阻止敏感檔案被 commit。Claude 完成時發送 Slack 通知。每次編輯後執行 type-checking。執行必須 100% 遵循的合規規則。

這是一個防止 Claude 永遠不會 commit secrets 的入門 hook：

```json
{
  "hooks": {
    "PreCommit": [{
      "command": "git diff --cached --name-only | grep -E '\\.(env|pem|key)$' && echo 'BLOCKED: secret file' && exit 1 || exit 0"
    }]
  }
}
```

把這個加到 `.claude/settings.json`，它在 credential 檔案到達你的 repo 之前就攔截。零依賴於模型把事情做對。確定性的。

最近新增的包括 PostCompact hooks（在 context compaction 後觸發，用於記錄什麼被壓縮了）和 ExitWorktree hooks。

**決策框架：** hooks 是確定性保證。用於每次都必須運作的業務規則。Prompts 是概率性指引。用於偏好和軟性規則。如果業務會因為一次失敗而賠錢或面臨法律風險，用 hooks。

### MCP（Model Context Protocol）

連接 Claude 到外部服務的開放標準。資料庫、APIs、GitHub、Slack、Telegram、Google Drive，以及任何你能建構 server 的東西。

把 MCP 想成 AI 的 USB-C。不是為每個資料來源建構自訂整合，你針對一個協議建構。MCP servers 暴露資料和能力。Claude 連接到它們。整個 Channels 功能建構在 MCP 上。Telegram 和 Discord 整合本身就是 MCP servers。Plugin 架構在上面運行。如果你在建構任何連接 Claude 到外部資料的東西，你就在 MCP 上建構，不管你知不知道。

MCP server 配置位於 project level 的 `.mcp.json`（版本控制，與團隊共享）或個人 server 的 `~/.claude.json`。環境變數展開（`${GITHUB_TOKEN}`）讓 credentials 不進版本控制。

在建構自訂 MCP server 之前，檢查是否已有社群 server 存在你的整合。Jira、GitHub、Slack、Notion、Linear 和數十個其他常見工具已有社群 server。只在社群 server 無法處理你團隊特定的工作流時才自建。

定期執行 `/mcp` 檢查每個 server 的 token 成本。我見過專案中一個被遺忘的 MCP 連接每次 session 吃掉 15% 的 context window。斷開你沒在積極使用的 server。

### Plugins

![The Extension Stack](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/plugins.jpg)

這是你團隊標準實際存在的地方。一個人建構一個 plugin，捕捉你團隊的 code review 標準、部署清單和架構模式。每個人安裝它。一致的、符合品牌的、遵循流程的輸出遍及整個組織——因為標準存在 plugin 中，不是存在個人記憶中。

Plugins 將 skills、hooks、subagents 和 MCP servers 捆綁成單一可安裝的單元。如果你建構了一個結合 skill 檔案、subagent 和 pre-commit hook 的 code review 工作流，把它打包成 plugin 並透過 marketplace 或你團隊的私有 marketplace 分發。

Plugin skills 有命名空間（`/my-plugin:review`），所以你可以同時運行五個 plugins 而它們不會互相踩踏。3 月 20 日的更新新增了在 settings.json 中透過 `source: 'settings'` marketplace source 內聯宣告 plugin 條目的能力。

**從這裡開始：** 安裝一個符合你角色的官方 plugin。使用一週。然後建構你自己的，編碼你團隊使用的特定標準。第二步才是真正槓桿開始的地方。

### Headless Mode 和 CI/CD

Claude Code 用 `-p` flag 在非互動模式下運行。這就是你如何將它整合到自動化 pipeline 中。PR review、安全掃描、測試生成、文件更新。沒有 `-p` flag，你的 CI job 會掛在那邊等待互動輸入。

搭配 `--output-format json` 和 `--json-schema` 使用以獲得機器可解析的結構化輸出。自動化系統可以將發現作為 inline PR 評論發布。基本的 GitHub Actions 設定：在 PR 開啟時觸發，執行 `claude -p "review this diff for bugs and missing tests" --output-format json`，解析輸出，發布為 review 評論。設定大約需要 15 分鐘，在任何人類 review 之前就能發現問題。

永遠用獨立的 Claude instance 做 code review，不要用寫程式碼的同一個 session。產生程式碼的 session 保留了推理 context，使它較不可能質疑自己的決定。一個全新的 instance 能發現更多。

### Claude Code Security

這對任何部署 production 程式碼的團隊來說都是大事。Claude Code 現在可以 review 整個 codebase 來識別安全漏洞。傳統掃描器將程式碼與已知模式匹配並標記所有看起來可疑的東西，這意味著 30-60% 的 false positive 率和大量浪費在分類噪音上的時間。Claude 改為語義性地推理程式碼，跨 codebase 追蹤資料流並尋找跨檔案邊界的邏輯缺陷。

Anthropic 報告 false positive 率低於 5%。在使用 Opus 4.6 的測試中，他們的安全團隊在經過良好 review 的開源專案中發現了超過 500 個漏洞，包括多年來未被發現的 bug。Claude 然後透過自己的 red-teaming 流程過濾 false positives。把它指向你的 repo 看看它發現什麼。你可能會驚訝。

### Voice Mode

Claude Code 支援語音輸入進行免手 coding。對它說話而不是打字。你在一個螢幕上 review 程式碼，口述重構筆記而不需要切換視窗。或者你在辦公室裡踱步，試圖弄清楚為什麼 auth flow 壞了，你就直接口述修復。用 `/voice` 切換。早期有一些粗糙的邊角（WebSocket 斷線、全新安裝的啟動怪癖），但 Anthropic 一直在穩步修補。

### Code Review 和自動化 PR 工作流

Claude Code 也處理 pull requests 上的自動化 code review。在 headless mode（`-p`）中，它讀取 PR diff、評估程式碼品質、標記 bug、檢查缺少的測試，並將發現作為 inline PR 評論發布。結合 `--output-format json` flag，這直接嵌入 GitHub Actions 或任何 CI pipeline。團隊也在使用 Claude Code 進行自動化預覽生成和 merge 準備——Claude review 變更、執行測試、產生摘要，如果一切通過就 stage merge。

## 超越 Chat、Cowork 和 Code

![Remote Control vs Dispatch vs Channels](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/channels.jpg)

除了 Chat、Cowork 和 Code 之外，還有更多。以下是其他發布的內容。

### Claude for Excel 和 PowerPoint

Claude 作為兩個 app 中的 add-ins 運作。3 月 11 日的更新讓它們共享完整 context，所以在 Excel 中的工作（資料分析、公式、樞紐分析表、條件格式化）直接流入 PowerPoint（簡報、視覺化）而不丟失資訊。Skills 在 add-ins 中也能運作，使用 Amazon Bedrock、Google Cloud Vertex AI 或 Microsoft Foundry 的組織可以透過 LLM gateway 連接。

**省最多時間的工作流：** 把你的原始資料丟進 Excel，告訴 Claude 分析它、建構樞紐分析表、突出顯示關鍵趨勢。然後打開 PowerPoint 告訴 Claude 從這些發現建構簡報。共享 context 意味著它已經知道資料、關鍵要點和數字。不需要重新解釋、不需要在 app 間複製貼上、不需要重新格式化。有位創作者報告從原始季度資料到董事會級簡報只用了 20 分鐘。

Microsoft 在 3 月 9 日推出了由 Claude 模型驅動的自家「Copilot Cowork」，作為新的 $99/用戶 E7 授權層級的一部分。Anthropic 的技術正在成為其他公司產品內部的引擎。

### 對話中的自訂視覺化

3 月 12 日作為 beta 發布，所有用戶（包括免費版）可用。Claude 現在可以在對話中內聯建立互動式圖表、圖解和視覺化。使用 HTML 和 SVG 建構，支援 hover 和 click 互動，並隨對話繼續而演化。

這些不同於 Artifacts。Artifacts 是側面板中的持久、可分享文件。Inline visuals 是暫時性的。它們存在於對話流中，隨你的跟進問題改變，並可能隨對話演進而消失。想像 Claude 在對話中途拿出白板。你可以將它們存為 SVG/HTML 或轉換為完整的 artifact 如果你想保留它們。

當你在探索資料或解釋概念並想邊說邊看時使用 inline visuals。當你需要一個可分享或下載的完成交付物時使用 artifacts。甜蜜點：在 debug 對話中途說「show me a flowchart of how this auth system works」。Claude 畫出來，你發現壞掉的步驟，你繼續對話。不需要切換到另一個工具。

### API

對於在 Claude 上建構的開發者，最大的實際變更：

- **Adaptive thinking 搭配可配置的 effort levels** 取代了舊的 `budget_tokens` 方法。在 Sonnet 4.6 上將 effort 設為 "medium" 用於大多數任務，你會顯著減少成本而不損失品質。Opus 4.6 上新的 "max" effort level 在你需要頂尖能力時使用，但 token 消耗很快。Thinking tokens 按 output tokens 計費，Opus 為 $25/M，所以 effort level 是你在自動化 pipeline 中的主要成本控制槓桿。
- **Fine grained tool streaming** 是 GA，不需要 beta header。**Structured outputs** 是 GA。**Data residency controls** 讓你以 1.1x 定價將推論固定在美國。**1M context window** 對超過 200K token 的請求自動生效，不需要程式碼變更。
- **Code execution** 與 web search 或 web fetch 結合時是免費的。搜尋結果的 dynamic filtering 不需要超出標準 token 的額外費用。Web search 和 web fetch 都是 GA，不需要 beta header。這是大多數人錯過的重點。
- **API Skills** 是新的，大多數開發者還沒發現。Anthropic 發布了用於 PowerPoint、Excel、Word 和 PDF 處理的預建 skills。你也可以透過 `/v1/skills` endpoints 上傳自訂 skills，將領域專業知識和組織工作流打包成可重複使用的能力。Skills 需要啟用 code execution。如果你在 API 上建構文件處理 pipeline，這取代了很多自訂工具。
- **Context compaction** 在對話接近限制時自動摘要較舊的 context。當 session 填滿時，API 識別可以被壓縮的部分同時保留關鍵資訊。自從 1M window GA 以來，compaction 事件整體減少。

## 數字

![The Numbers](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/numbers.jpg)

Anthropic 在 2026 年 2 月 12 日以 $3,800 億估值完成了廣泛報導的 $300 億 Series G。由 GIC 和 Coatue 領投。史上第二大風險投資交易，僅次於 OpenAI 的 $400 億融資。Microsoft 和 Nvidia 也是貢獻者之一。

年化收入達到 $140 億，連續三年每年成長 10 倍。僅 Claude Code 的年化收入就有 $25 億，自一月以來翻了一倍多。企業訂閱在同期增長了四倍。Fortune 10 中有八家是 Claude 客戶。超過 500 家客戶每年花費超過 $100 萬，兩年前大約只有十幾家。年花費超過 $10 萬的客戶在過去一年增長了 7 倍。企業佔業務的 80%，任何組織現在都可以在網站上直接購買 Enterprise，不需要銷售對話。

在企業基礎設施方面：HIPAA-ready Enterprise plans 在一月為處理受保護健康資訊的醫療組織推出。Enterprise Analytics API（2 月 13 日）提供了按組織每天匯總的使用和參與資料的程式化存取。這類東西讓採購團隊簽約。

Anthropic 還推出了 Claude Partner Network，投資 $1 億用於培訓、聯合行銷和技術架構支持。首個專業認證 **Claude Certified Architect (Foundations)** 在 3 月 12 日作為監考的架構級考試發布。它測試 agentic design、MCP 整合、Claude Code 配置和 production reliability patterns。Accenture 正在培訓約 30,000 名專業人員。Anthropic Academy 在 3 月 2 日提供了 Skilljar 上的 13 門免費課程作為官方準備路徑。之後又新增了兩門課程，選項擴展到 15 門。針對銷售人員、開發者和進階架構師的額外認證計畫在今年稍後推出。如果你是顧問或代理商，當企業客戶開始問你團隊中誰有認證時，這個認證堆疊會很重要。

**內部數據：** Anthropic 的工程師使用 Claude 完成大約 60% 的自身工作，一年前是 28%。他們每天內部發布 60 到 100 個版本。Cowork 從零到上線只花了 10 天，完全用 Claude Code 建構。工具在建構工具，而這個反饋迴路是發布節奏從數月變成數週再變成數天的原因。

## 這一切對你意味著什麼

![The Complete Claude Ecosystem](/assets/img/posts/2026-03-28-everything-claude-shipped-2026/excel.jpg)

**基礎設施層正在快速商品化。** 六個月前需要自訂框架的功能現在是原生平台功能。護城河從來不是基礎設施。一直是品味、分發，以及你在工具之上建構的東西。

**對於在 Claude 上建構產品的 builders，** 擴充系統是槓桿所在。Skills、subagents、Agent Teams、hooks、Channels、MCP、plugins。一個配置良好的 Claude Code setup 加上自訂 skills 和 scoped agents，完全是與在聊天視窗中打 prompt 不同的工具。學習這些層級。為你的工作流配置它們。這份投資在每次 session 都會回報。

**對於知識工作者，** Cowork 從本週開始改變你的日常工作流。建構你的 context 資料夾。設定你的 global instructions。安裝兩個 plugins。對所有事情使用 AskUserQuestion。設定一個排程任務。Dispatch 的手機到桌面橋樑意味著空閒時間變成生產時間。今天的 Computer Use 發布更進一步。

**對於團隊主管，** plugin marketplace 和企業功能意味著你可以標準化整個組織如何使用 Claude。建構編碼你團隊機構知識的 plugins 並分發它們。這就是你如何從「我們有時使用 AI」變成「AI 是我們運作的方式」。

節奏不會放慢。它在加速，因為 Anthropic 正在使用自己的工具來建構下一代工具。每個新模型讓建構下一個更快。這會複利累積，它改變了一切的數學。

**現在就學習這個平台。** 不是下個季度，不是下個月。就是現在。

---

如果你讀到了這裡，你已經領先 99.9% 那些會收藏這篇然後可能永遠不會回來看的人了。他們會繼續把 Claude 當基本的 chatbot 用。你不會。

我不是工程師。我是自學的。我也不會聲稱對每個使用場景都有所有答案或最佳的 Claude 設定——如果有人聲稱他們有，他們在騙你。這裡的一切來自每天用這些東西建構、不斷搞壞東西，然後寫下什麼真正有效，這樣其他人就不用猜了。你需要樂在其中並勇於嘗試。這就是你學習的方式。如果這裡有什麼是錯的、缺少的或過時的，請告訴我。我寧願修正它，也不願讓人建構在錯誤的資訊上。

### 資源

- [Claude Documentation](https://docs.claude.com)（任何功能從這裡開始）
- [Claude Code Documentation](https://code.claude.com)（extensions、skills、agents、hooks）
- [Claude Cowork](https://claude.com/blog/cowork-research-preview)（launch post + 功能概覽）
- [Claude Blog](https://claude.com/blog)（每個功能公告）
- [Claude Code Channels](https://code.claude.com/docs/en/channels)（Telegram/Discord 設定指南）
- [Claude Partner Network](https://anthropic.com/partners)（免費加入，認證存取）
