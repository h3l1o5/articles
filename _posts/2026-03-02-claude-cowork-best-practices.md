---
title: "讓 Claude Cowork 強大 100 倍的 17 個最佳實踐"
date: 2026-03-02
categories: [AI]
tags: [claude, cowork, productivity, workflow, automation]
---

> 原文：[17 Best Practices That Make Claude Cowork 100x More Powerful](https://x.com/heynavtoor/status/2028148844891152554) by **Nav Toor** (@heynavtoor)
> 發佈日期：2026 年 3 月 2 日

---

我從 1 月 12 日——也就是 Cowork 上線的第一天——就開始使用 Claude Cowork 了。

七週內，我跑了超過 400 個 Cowork session。每個 plugin、每個 connector、每個 slash command 我都測過了。我把它搞壞的方式大概連 Anthropic 都沒見過。然後我搞清楚了，那些把「覺得 Cowork 還不錯啦」的人和「已經用它取代了一半軟體工具」的人區分開來的關鍵實踐到底是什麼。

差距是巨大的。而且跟 prompt 技巧完全無關。

關鍵在於設定、結構，以及十七個大多數用戶永遠不會自己發現的具體實踐——因為 Anthropic 根本沒有寫在文件裡。

我逐一測試了每一個，並衡量了差異。以下是完整清單——按影響力排序。

## Part 1：Context 架構（實踐 1–5）

光是這五個實踐就能徹底改變你的 Cowork 體驗。其他所有東西都建立在這個基礎之上。

### 1. 為每個工作資料夾建立 \_MANIFEST.md

這是沒人在討論的、影響力最大的單一實踐。

問題是這樣的：當你把 Cowork 指向一個資料夾，Claude 會讀取所有東西。每個檔案、每個子資料夾、每個過時的草稿和已被取代的版本。DEV Community 上有個開發者在一個包含 462 個檔案的顧問資料夾開始產出矛盾內容後，記錄了這個問題——Claude 從三個月前就被替換掉的定價模型中提取 context。

解法：在任何工作資料夾裡放一個 `_MANIFEST.md` 檔案。它告訴 Claude 哪些文件是真理來源、哪些子資料夾對應哪些領域，以及哪些要完全跳過。

用三個層級來組織：

**Tier 1（Canonical）**：Claude 必須優先讀取的真理來源文件。你的品牌指南、專案簡報、當前策略文件。

**Tier 2（Domain）**：對應到特定主題的子資料夾。只有當任務涉及該領域時，Claude 才會載入這些。例如「/pricing → 定價模型與費率表」或「/research → 競品分析」。

**Tier 3（Archival）**：舊草稿、已取代的版本、參考資料。除非你明確要求，否則 Claude 會忽略這些。

底線前綴讓它排在資料夾最上方。填寫只需五分鐘，卻能省下數小時的混亂輸出。十個檔案以下的資料夾不需要。但任何更大的——尤其是會在數週內累積檔案的專案資料夾——這絕對不能省。

### 2. 把 Global Instructions 當成你的永久作業系統

Settings → Cowork → Edit next to Global Instructions。

大多數人把這裡留白。那就像買了一台車卻從不調後照鏡。

Global Instructions 在所有東西之前載入——在你的檔案之前、在你的 prompt 之前、在 Claude 甚至還沒看你的資料夾之前。它們是套用到每一個 session 的基準行為。

我的是這樣寫的：「我是 [姓名]，一個 [角色]。開始任何任務之前，先找 \_MANIFEST.md 並優先讀取 Tier 1 檔案。執行前一定要問澄清問題。行動前先展示簡要計畫。預設輸出格式：.docx。不要用填充語言。不要灌水。品質標準：每份交付物都應該不需要編輯就能直接交給客戶。如果信心不足，直接說。」

這代表即使是我最懶、最趕的 prompt，仍然能產出校準過的輸出。Claude 永遠知道我是誰。永遠先讀正確的檔案。永遠在猜測之前先問。Global Instructions 處理基準，你的 prompt 只負責任務。

### 3. 建立三個持久的 context 檔案

我在之前的文章中已經深入介紹過了，但這太重要了，不能不在這裡重複。

建立一個叫做「Claude Context」（或者「00\_Context」讓它排在最前面）的資料夾，加入三個檔案：

**about-me.md** — 你的專業身份。不是你的履歷。你實際做什麼、服務誰、當前的優先事項是什麼，以及一兩個你最佳作品的範例。

**brand-voice.md** — 你的溝通風格。語調描述、你會用的詞、你絕不用的詞、格式偏好，以及兩到三段你實際寫作的文字作為參考。

**working-style.md** — Claude 應該如何行動。協作規則、輸出格式預設值、品質標準，以及一份要避免的事項清單。

這三個檔案能一夜之間消除「通用 AI 輸出」的問題。沒有它們，每個 session 都是冷啟動。有了它們，Claude 在每個 session 開始時就已經知道你的聲音、你的標準和你的偏好。

大多數人忽略的關鍵洞察：這些檔案會複利累積。每週精煉它們。每當 Claude 產出你不喜歡的東西，問問自己這是 prompt 問題還是 context 問題。十次有九次是 context 問題。在某個檔案裡加一行就好，永久修復。

### 4. 使用 Folder Instructions 設定專案級 context

Global Instructions 對每個 session 都一樣。Folder Instructions 則是特定於你正在工作的資料夾。

當你在 Cowork 中選擇一個資料夾，Claude 可以自動讀取和更新 Folder Instructions。但你也可以手動設定。這裡放的是專案特定的規則：客戶名稱、專案目標、特定術語、交付物格式、審查截止日期。

層次結構很重要。Global Instructions 設定通用行為。Folder Instructions 增加專案 context。你的 prompt 指定任務。三層，每一層都比上一層更具體。這就是你從「通用 AI」進化到「這聽起來像是在我團隊待了六個月的人寫的」的方法。

### 5. 永遠不要讓 Claude 讀取所有東西——刻意限定你的 context

這是區分進階用戶和其他人的實踐。

Claude 的 context window 非常大——在 Opus 4.6 上超過一百萬 token。但更大的 context 不代表更好的輸出。事實上，往往相反。Claude 讀取的無關檔案越多，進入推理的雜訊就越多，輸出品質就越差。

告訴 Claude 該讀什麼。在你的 Global Instructions 中加入：「開始任何任務時，先找 \_MANIFEST.md。載入 Tier 1 檔案。只有當任務明確涉及該領域時才載入 Tier 2 檔案。除非我特別要求，否則永遠不要載入 Tier 3 檔案。」

如果你使用 subagent，範圍要限定得更緊：「分解任務給 subagent 時，只給每個 subagent 完成其特定子任務所需的最少 context。」

刻意的 context 管理是 Cowork 用戶獲得不一致結果和每次都獲得可靠、高品質輸出之間最大的區分因素。

## Part 2：任務設計（實踐 6–10）

你如何框定任務，決定了 Cowork 交付的是成品還是昂貴的粗略草稿。

### 6. 定義終態，而非過程

這是改變一切的思維轉換。Cowork 不是聊天機器人，它是同事。你不會一步一步告訴同事怎麼做他的工作，你會告訴他「完成」長什麼樣子。

不好的 prompt：「幫我處理我的檔案。」

好的 prompt：「把這個資料夾裡的所有檔案按客戶名稱整理到子資料夾中。所有檔名使用 YYYY-MM-DD-descriptive-name 格式。建立一份摘要日誌記錄每個變更。不要刪除任何東西。如果一個檔案可能屬於多個客戶，放到 /needs-review。」

第二個 prompt 定義了終態（整理好的資料夾）、命名規範、輸出成品（摘要日誌）、安全約束（不刪除）和不確定性處理（needs-review 資料夾）。Claude 現在可以自主執行了——你可以放手去做其他事。

每個任務 prompt 都應該回答三個問題：「完成」長什麼樣子？限制條件是什麼？不確定時 Claude 該怎麼做？

### 7. 永遠要求在執行前先展示計畫

在你的 Global Instructions 加入這行：「任何任務在行動前先展示簡要計畫。等待我批准後再執行。」

這一行就能預防 90% 的 Cowork 災難。沒有它，Claude 讀完你的 prompt 就立刻開始執行。有時候完全正確，有時候它誤解了一個詞，就把三個月的檔案往錯誤的方向重新整理了。

有了計畫步驟，你會得到一個 30 秒的審查窗口。「我打算建立這六個子資料夾，移動這些檔案，用這個命名規範重新命名，然後把日誌存在這裡。可以繼續嗎？」你掃一眼，看起來沒問題，批准。Claude 執行。

代價：每個任務多花 30 秒。好處：你永遠不需要撤銷一個 20 分鐘的自主錯誤。

### 8. 告訴 Claude 遇到不確定性時該怎麼做

這是整份清單中最被低估的實踐。

大多數人為順利路徑給了 Claude 明確的指示，卻對邊界情況隻字未提。當收據圖片模糊時怎麼辦？當一個檔案可能屬於兩個分類時怎麼辦？當資料來源不完整時怎麼辦？

Claude 會猜。而 Claude 的猜測往往是錯的——不是因為它笨，而是因為它不知道你對模糊情況的偏好。

把不確定性處理寫進每個任務：「如果日期不明確，標記為 VERIFY。如果一個檔案可能放在多個資料夾中，放到 /needs-review。如果你對某個分類的信心低於 80%，標記它而不是猜測。」

這把 Cowork 從一個「有時會出錯的工具」轉變為一個「精確告訴你哪裡需要你判斷的工具」。那是完全不同層次的價值。

### 9. 把相關工作批次放進單一 session

每個 Cowork session 都有啟動成本。Claude 讀取你的檔案、載入你的 context、處理你的資料夾結構。那是你在花費的算力。

不要為五個相關任務跑五個獨立的 session。跑一個 session：「我需要處理這個月的費用收據、更新預算試算表、產生摘要報告、起草一封給財務部的 email，然後把所有東西存到 /monthly-reports/february。」

Claude 規劃所有五個任務，跨任務共享 context（收據資料注入預算，預算注入報告，報告注入 email），然後在一次執行中產出五個相互關聯的交付物。更快、更省、品質更高——因為每個任務的 context 都會提供給下一個任務。

如果你一直碰到用量限制，這通常就是解法。更少的 session、每個 session 更多任務，幾乎永遠比「很多 session 各做一件事」來得好。

### 10. 刻意使用 subagent——要求平行處理

Cowork 最強大的功能是大多數用戶從未觸發過的。

當你給 Cowork 一個包含獨立部分的任務時，它可以啟動多個 subagent 同時處理。每個 subagent 獲得獨立的 context，處理它負責的部分，然後把結果交回主 agent 進行彙整。

觸發方式：在你的 prompt 中加入「Spin up subagents to...」或「Work on these in parallel using subagents」。

範例：「我正在評估四家供應商。啟動 subagent 分別研究每家的定價、支援口碑和整合選項。給我一張比較表。」不再依序研究——供應商 A、然後 B、然後 C、然後 D——Cowork 啟動四個平行 agent。本來要花 40 分鐘的任務只需 10 分鐘。

適用場景：競品分析、多來源研究、批次處理檔案、從不同角度（財務、營運、客戶體驗）評估選項，以及任何子任務之間不相依的情境。

注意事項：subagent 在 Opus 4.6 上表現最好，且會消耗更多 token。用在複雜任務——時間節省能合理化成本的地方。別用它來整理你的 Downloads 資料夾。

## Part 3：自動化與排程（實踐 11–13）

這是 Cowork 從生產力工具進化為自主系統的地方。

### 11. 用 /schedule 排程重複性任務

在任何 Cowork 任務中輸入 `/schedule`。Claude 會引導你設定一個自動執行的任務——每天、每週、每月，或按需執行。

我設定過最好的排程任務：

**週一早晨簡報**：「每週一早上 7 點，檢查我的 Slack 頻道和本週行事曆。摘要即將發生的事、標記需要準備的項目，然後存一份簡報到 /weekly-briefings。」

**週五狀態報告**：「每週五下午 4 點，從 Asana 拉取我已完成的任務，摘要本週交付了什麼，起草一份狀態更新，存到 /reports。」

**每日競品追蹤**：「每天早上 9 點，研究 [競爭對手名稱] 的新聞、產品更新或定價變動。只在有新資訊時存摘要。」

關鍵限制：排程任務只有在你的電腦醒著且 Claude Desktop 開啟時才會執行。如果你的機器在任務預定時間睡眠，Cowork 會在你回來時補執行並通知你。把這點納入規劃。

### 12. 建構一次，每週執行——把所有東西外化到檔案

Cowork 在 session 之間沒有記憶。這同時是它最大的限制和最棒的設計特色。

沒有記憶代表沒有 context 溢出。沒有三週前的幻覺回憶。每個 session 都是全新開始。但這也意味著你不能依賴「Claude 記得我喜歡怎麼做」。

解法：把所有東西外化到檔案中。你的偏好存在 context 檔案裡。你的專案計畫存在 markdown 文件中。你的標準作業程序存在 skill 檔案裡。你的決策和結果存在日誌檔案裡。

有個進階用戶記錄了建立每週回顧系統的過程：跨五個專門的 subagent 指令超過 1,500 行。建構一次，每週執行。Claude 讀取指令，啟動五個平行 agent，每個都有限定的權限和定義好的輸出，然後不需要任何新的輸入就能產出完整的每週回顧。

如果你想要連續性，就必須把它建構到檔案中。但好處是巨大的：一個文件化的工作流程是可攜帶的、可分享的、有版本控制的。它不是存在某個 AI 的記憶裡，而是存在你的系統中。

### 13. 用 /schedule + connector 的組合實現真正的自動化

排程任務在結合 connector 後會變得真正強大。

連接 Gmail、Slack、Google Drive、Notion、Asana，或五十多個可用整合中的任何一個，然後排程會拉取即時資料的任務：

「每週一，從 #product-feedback 拉取所有未讀 Slack 訊息，按主題分類，然後在 Google Drive 建立摘要。」

「每天早上，檢查我的 Gmail 是否有發票，提取金額和日期，然後更新我本機 /finance 資料夾裡的費用試算表。」

這是 Cowork 從任務執行器蛻變為自主系統的時刻。排程任務執行，connector 拉取即時資料，Claude 處理它，輸出出現在你的資料夾或連接的工具中。你準備好了再檢查。

Settings → Connectors → Browse connectors 可以查看有哪些可用。先從 Slack 和 Gmail 開始，光這兩個就能每週省下好幾個小時。

## Part 4：Plugin 與 skill（實踐 14–16）

Plugin 是 Cowork 的模組化大腦。Skill 是它的劇本。大多數用戶裝了一個 plugin 就再也不看了。這等於放著 80% 的價值不用。

### 14. 堆疊 plugin 以獲得複合能力

每個 plugin 都是一組針對特定領域設計的 skill、slash command 和 subagent 配置——銷售、法律、財務、產品管理、資料分析等等。

但大多數人忽略的是：plugin 是可組合的。你可以安裝多個 plugin，在單一任務中使用所有 plugin 的能力。

範例：安裝 Data Analysis plugin 和 Sales plugin。然後：「分析我們的 Q1 pipeline 資料（用 Data Analysis），找出三筆最弱的交易，並為每筆起草個人化的 follow-up email（用 Sales）。」Claude 在一個工作流程中使用了兩個 plugin 的能力。

我目前的組合：Productivity（永遠開著）、Data Analysis（永遠開著）、Sales（做 outreach 的那幾週）和 Marketing（做內容的那幾週）。根據當前重點輪換後面兩個。

先從我發佈的 tier list 開始——安裝符合你角色的 S-tier 和 A-tier plugin。然後嘗試組合。

### 15. 為你的特定工作流程建立自訂 skill

Skill 是一個 markdown 檔案，教 Claude 如何處理一個特定的、可重複的任務。Plugin 打包了許多 skill。但你也可以自己建立。

自訂 skill 檔案的結構：

```
# [Skill Name]
## Purpose: What this skill does.
## Inputs: What information Claude needs.
## Process: Step-by-step instructions.
## Output: What the finished deliverable looks like.
## Constraints: Rules and guardrails.
```

範例：我建立了一個「Weekly Article Drafting」skill。Purpose：從主題和大綱起草一篇 2,000 字的文章。Inputs：主題、大綱、目標受眾、關鍵證據。Process：用 web search 研究、撰寫各段落、匹配 brand-voice.md、產生 VISUAL SUGGESTIONS 和 QUOTABLE LINES。Output：.docx 檔案存到 /articles/drafts。Constraints：不使用 AI 語義用語、不使用填充語句、至少 8 個證據點。

現在我只要說「Run my article drafting skill on [topic]」就能得到一篇可發佈的草稿。Skill 編碼了我通常要花 20 分鐘在 prompt 中解釋的所有內容。

把自訂 skill 存為 .md 檔案放在你的工作資料夾中，或透過 Customize 選單上傳。Claude 會在每個相關 session 開始時讀取它們。

### 16. 用 Plugin Management plugin 以對話方式建立 plugin

這是 Cowork 中最 meta 的功能——也是最少被使用的。

安裝 Plugin Management plugin。然後說：「Help me create a plugin for [你的工作流程]。」Claude 會透過對話引導你定義 skill、slash command 和配置。不需要寫程式碼、不需要 GitHub、不需要學習 markdown 語法。

你描述你想要什麼。Claude 建立 plugin。你測試它。你精煉它。不到一個小時，你就有了一個自訂 plugin，把你特定的工作流程、標準和術語都編碼在裡面。

對團隊來說，這是變革性的。一個人為團隊的標準流程建立 plugin。所有人安裝它。突然間整個團隊都能產出一致的、符合品牌的、合規的輸出——因為標準存在 plugin 裡，不是存在個人記憶中。

企業團隊：Anthropic 在二月推出了私有 plugin marketplace。管理員可以建立、策展和分發自訂 plugin 到整個組織。建構一次，部署給數百人。

## Part 5：安全與效率（實踐 17）

### 17. 把 Cowork 當成一個有能力的員工，不是玩具

Cowork 有實際的檔案系統存取權。它可以建立、移動、重新命名——在你的許可下——刪除你電腦上的實際檔案。它可以瀏覽網頁。它可以與連接的工具互動。它可以無人監督地執行好幾個小時。

這種能力需要被認真對待。以下是不可妥協的安全實踐：

**實驗前先備份。** 尤其是檔案整理任務。Cowork 大部分時候都做對了。「大部分時候」對你的客戶合約來說不夠好。

**把敏感檔案放在獨立資料夾中。** 財務文件、密碼、個人資料——把它們放在 Cowork 永遠不會碰到的資料夾裡。不要把你整個 Documents 目錄的存取權都開放。嚴格限定範圍。

**永遠加上「不要刪除任何東西」，除非你特別想要刪除。** 即使有刪除保護（Claude 會在刪除前詢問），從源頭預防請求總是更好的。

**監控任何新工作流程的最初幾次執行。** 觀察 Claude 做了什麼。讀計畫。檢查輸出。一旦你信任了某個工作流程，就可以放手。但先贏得那份信任。

**注意 prompt injection 風險。** 如果 Claude 讀取了惡意文件或網站，隱藏的指令可能改變它的行為。不要在沒有先檢查的情況下，把 Cowork 指向不受信任的檔案來源或不熟悉的 URL。

**追蹤你的用量。** Cowork 消耗的配額遠超一般聊天。複雜的、多步驟的帶 subagent 任務是算力密集的。如果你碰到限制，批次處理相關工作，用「只修改第 2 節」而不是「全部重做」，並透過檔案預載 context 而不是在聊天中重新解釋。

## 所有 17 個實踐背後的模式

如果你退後一步看，這份清單上的每個實踐都遵循同一個原則：

**投資在設定上，減少 prompting。**

在 Cowork 上掙扎的人，每個任務都在寫又長又詳細的 prompt，結果卻不穩定。在 Cowork 上如魚得水的人，花了一個下午建構他們的 context 架構——manifest 檔案、global instructions、context 檔案、folder instructions、自訂 skill——現在只用十個字的 prompt 就能產出可直接交給客戶的交付物。

這是從 ChatGPT 時代思維到 Cowork 時代思維的根本轉變。ChatGPT 獎勵 prompt engineering。Cowork 獎勵系統 engineering。

Prompt 是 Cowork session 中最不重要的部分。你圍繞它建構的 context、結構、skill 和約束——那才是輸出品質的來源。

就像一位每天早餐前就跑五個平行工作流程的 Substack 作者說的：「它感覺不像對話，更像是留任務給一個有能力的同事。」

那就是目標。不是聊天機器人。不是一問一答的工具。而是一個已經知道你的標準、你的聲音、你的專案和你的偏好的同事——因為你把那些知識建構進了它每次都會讀取的檔案裡。

## 你的實施檢查清單

按順序做。每一步都會在前一步的基礎上複利累積。

**今天（30 分鐘）**：建立你的三個 context 檔案，設定你的 Global Instructions。光這個就能讓你超越 95% 的 Cowork 用戶。

**本週**：在你最常用的專案資料夾中加入 \_MANIFEST.md。安裝兩到三個符合你角色的 plugin。設定一個排程任務。

**本月**：為你最常重複的工作流程建立第一個自訂 skill。用 subagent 嘗試一個複雜的研究任務。根據輸出品質精煉你的 context 檔案。

到第一個月結束，你將擁有一個 Cowork 設定，能以更少的時間產出比你用過的任何 AI 工具都更高品質的輸出。

把 Cowork 從玩具變成系統的差距，就是十七個實踐和大約兩小時的設定。

知道這些實踐的人和不知道的人之間的差距已經很大了。

六個月後，會是一道鴻溝。
