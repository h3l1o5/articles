---
title: "Claude Code + Obsidian 終極指南：打造你的 AI 第二大腦"
date: 2026-04-09
categories: [AI]
tags: [claude-code, obsidian, knowledge-management, llm-wiki, karpathy, second-brain]
image:
  path: /assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/banner.jpg
  alt: "Claude Code + Obsidian Ultimate Guide"
---

> 原文：[Claude Code + Obsidian Ultimate Guide (build an AI second brain)](https://x.com/aiedge_/status/2041908011078447222) by **AI Edge** (@aiedge_)
> 發佈日期：2026 年 4 月 8 日

---

Claude Code + Obsidian 是我用過最強大的 AI 組合。

我真的打造了一個 AI 第二大腦，裡面包含我想的、讀的、寫的、網路上研究的所有東西，以及更多。

它包含我的商業計畫、我所有的 YouTube 影片（從以前到現在）、我寫過的文章，還有任何對我來說重要的東西。

Claude Code + Obsidian 在各個平台上都爆紅了，而且理由很充分。

對我個人來說，這個 AI 系統減輕了我最大的心智負擔，讓我能專注在事業和個人生活中真正重要的事情上。

## 我的 Claude Code + Obsidian 系統

![My Claude Code + Obsidian system](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/01-system-overview.jpg)

這看起來可能很複雜，但它真的只需要 5 分鐘就能設定好，而且最瘋狂的是，它有一個內建的 memory 系統，會**隨著時間自己進化**。

讓我來展示你如何精確地複製這個 AI 第二大腦系統，真正提升你的生產力。

記得看到文章最後，我會給你我完整的 Claude Code + Obsidian playbook cheatsheet，以及我下面提到的每一個資源（100% 免費）。

## 前導

我不能把這個系統的功勞全攬在自己身上，因為它的靈感來自 Andrej Karpathy 幾天前爆紅的推文——關於 LLM 知識庫的概念。

> 延伸閱讀：本站已翻譯 Karpathy 的完整文件 → [LLM Wiki：用 LLM 打造個人知識庫的全新模式](/articles/posts/llm-wiki/)

這則推文之所以爆紅，是因為它為當前 AI 的一個**巨大問題**奠定了解決基礎。

那就是：**每次你開新對話或遷移到新的 AI 工具時，都需要不斷重新提示、添加 context，基本上從頭來過。**

透過將 Karpathy 的 system prompt 與 Obsidian 和 Claude Code 配對，你可以完全消除這個問題，並得到顯著更好的 AI 輸出。

## 這個系統的運作方式

這個系統有 4 個動態部件：

1. **你的資料** — 文章、筆記、逐字稿、想法等
2. **組織** — 由 Claude Code 在 Obsidian 中完成
3. **即時 Prompting** — 隨時向你的新資料庫提問任何問題
4. **進化中的 Memory** — 隨著時間變得更聰明的能力

## 這個系統的威力

身為人類，我們的心智頻寬有限。

我們會忘記事情，有時候很難連結想法，最終我們能追蹤的東西就只有那麼多。

透過使用這個四部件系統，你基本上是在釋放心智頻寬，讓 Obsidian 和 Claude Code 來連結、視覺化和解讀你的資料。

你的想法現在互相連結了，你的筆記跟其他筆記產生關聯，你可以透過 Claude 來萃取它們——這裡的可能性是無限的。

## 設定你的 AI 大腦（5 分鐘）

### 1. 下載 Obsidian

前往 [https://obsidian.md/](https://obsidian.md/) 下載。

### 2. 設定你的 Vault

下載完成後，系統會要求你設定一個「Vault」。可以把它想成一個簡單的桌面資料夾，我們會讓 Claude Code 存取它。

你可以隨便命名——我的叫「Obsidian Vault」。

![Obsidian Vault](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/02-obsidian-vault.jpg)

這個 Vault 就是 Obsidian 存放所有資料、筆記等的地方，以 MD（Markdown）檔案的形式。

### 3. Claude Code 設定

接下來，你需要一個存取 Claude Code 的方式。

對我個人來說（可能也是大多數人），我發現最簡單的方式就是直接用桌面應用程式。

在主聊天框裡，點擊「Select Folder」然後找到你的 Obsidian Vault。

![Claude Code - find your Vault](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/03-claude-code-select-folder.jpg)

### 4. System Prompt

選好資料夾後，把 Andrej Karpathy 的 system prompt 貼到主聊天框裡。

你可以在這裡複製 prompt：[https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

這份 prompt 的核心概念是：不像傳統的 RAG 在每次查詢時從原始文件中重新發現知識，而是讓 LLM **漸進式地建構和維護一個持久的 wiki** ——一個結構化的、互相連結的 Markdown 檔案集合，位於你和原始來源之間。

整個架構有三層：

- **Raw sources** — 你策劃的原始文件集合。文章、論文、圖片、資料檔案。這些是不可變的。
- **Wiki** — LLM 產生的 Markdown 檔案目錄。摘要、實體頁面、概念頁面、比較、綜覽、綜合分析。
- **Schema** — 一份文件（如 Claude Code 的 `CLAUDE.md`），告訴 LLM wiki 的結構、慣例和工作流程。

你的輸入應該像這樣：

![Claude Code initial input](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/04-system-prompt-input.jpg)

> 小提示：如果你不想的話，你完全不需要碰 Obsidian。你只要讓 Claude Code 存取 MD 資料夾和輸入資料，那些資料就會出現在你的 Obsidian 第二大腦裡（因為 Claude Code 可以修改資料夾）。

### 5. 建立你的資料庫

輸入上面的初始 system prompt 之後，Claude Code 會問你一些資料點來開始填充你的第二大腦。

![Building your database](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/05-building-database.jpg)

重要的是把 Obsidian 想成一本空白筆記本，你必須先輸入資料才能開始建構你的資料庫。

想想看：筆記、CSV 檔案、MD/文字檔等。

一些小技巧：

- 從你現有的筆記應用程式匯出一些資料
- 如果你用 Notion，匯出 CSV 檔案
- 請你的 Claude 對話（或其他 LLM）提供關於你自己的資訊，這樣你就可以注入到你的第二大腦裡
- 如果你有文章、書籤、想法等，現在就輸入它們——這是把至少一些初始資料放進你的第二大腦的時候，你隨時可以之後再新增

建立像我這樣的資料庫（有大量資料）需要時間來輸入各種資料集。

![My database](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/06-my-database.jpg)

就這樣，你的 AI 第二大腦現在已經完全設定好，可以開始運作了。

現在讓我給你一些優化它的進階技巧。

## 進階技巧

### 1. Obsidian Chrome Extension

要輕鬆輸入資料，你只需要 Obsidian Chrome Extension，它讓你在任何資料來源上點擊「add to Obsidian」。

這讓建立你的第二大腦變得超級簡單。

我經常使用這個功能來新增文章、線上資料、研究等。

![Example - using Obsidian Chrome Extension](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/07-chrome-extension.jpg)

不過，這會把資料集加入為一個獨立的來源。

你接下來要做的是告訴 Claude Code：「嘿，我剛剛在 Obsidian 裡輸入了 [x]，請把這些資料 ingest 到我的 Wiki 裡。」

Claude Code 接著會在你的第二大腦/Wiki 中與其他資料來源建立連結和關聯。

這就是為什麼這個工具組合如此強大。

### 2. 獨立資料夾

Karpathy 建議保持兩個獨立的資料夾/Vault。

一個用於商業/工作。一個用於個人/目標。

我也發現這是最理想的設定。

### 3. 實用性

我發現這個系統最好的使用案例之一，就是讓我的 LLM prompt 更準確。

有了對我整個人生、商業計畫、寫作脈絡等的存取權，它在創建策劃過的（且最新的）mega prompt 方面非常強大。

顯然，這個系統有大量的使用案例，但最實用的使用案例之一就是創建精準的 prompt——我推薦你用它做這件事。

### 4. Orphans

Obsidian 中的「Orphans」讓你看到哪些資料點缺乏連結。

這對於識別你可能想要發展的想法，以及資料庫中不太與其他內容重疊的潛在「弱點」很有用。

![Orphans](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/09-orphans.jpg)

你可以在右上角點擊三個點來找到 Orphans 的切換開關。

![Orphans toggle](/assets/img/posts/2026-04-09-claude-code-obsidian-ai-second-brain/10-orphans-toggle.jpg)

## 這個系統的潛在缺點

我們已經介紹了所有優點、一些酷炫的使用案例，以及如何優化這個 AI 系統。

但潛在的缺點呢？什麼時候有人**不應該**使用這個？

### 1. 你不是視覺型的人

這個設定的主要好處之一是你可以視覺化你的資料。如果你不是從視覺化工具中受益的人，這可能不適合你。

### 2. 維護

其次，如果你不想維護一個資料庫，這可能不適合你。維護量不大，但如果不實際輸入資料到你的第二大腦裡，它就是沒用的。

### 3. 儲存空間

這些 MD 檔案會佔用你裝置上的儲存空間。只是要記住這一點。

## 最後的想法

如果你看到了這裡，感謝你的閱讀，希望你覺得這篇文章有價值。

作為感謝，你可以在這裡取得我的**免費** cheatsheet：[https://www.aiedgehq.co/](https://www.aiedgehq.co/)

更多類似的 AI 內容，請追蹤 [@aiedge_](https://x.com/aiedge_)——每週上傳 2-3 篇關於業界最熱門話題的 AI 文章。
