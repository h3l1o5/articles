---
title: ".claude/ 資料夾就是你的新履歷"
date: 2026-03-26
categories: [AI]
tags: [claude-code, skills, career, developer-tools, ai-agent]
image:
  path: /assets/img/posts/2026-03-26-claude-folder-new-resume/claude-folder-new-resume-banner.jpg
  alt: "The .claude/ Folder Is Your New Resume"
---

> 原文：[The .claude/ Folder Is Your New Resume](https://x.com/heynavtoor/status/2036861280859124100) by **Nav Toor** (@heynavtoor)
> 發佈日期：2026 年 3 月 26 日

---

有個令人不安的事實，科技圈沒人在談：**你的履歷已經死了。**

不是正在消亡。不是逐漸淡出。是已經死了。

目前所有公開的 GitHub commits 中，有 4% 是由 Claude Code 產生的。按照目前的趨勢，這個數字在 2026 年底前會突破 20%。Claude Code 從零開始，只花了八個月就成為排名第一的 AI coding 工具，超越了 GitHub Copilot 和 Cursor。Anthropic 的 Skills repository 在 GitHub 上獲得了 69,000+ 顆星。Agent Skills 成為了整個生態系統採用的開放標準。

當大家還在爭論 AI 是否會取代開發者的時候，另一件事情悄悄發生了：

**你如何配置你的 AI agent，成為了你實際工作方式最明顯的信號。**

你的 `.claude/` 資料夾——CLAUDE.md 檔案、自定義的 Skills、Hooks、MCP 整合——這就是你的新作品集。這就是區分「打 'fix this bug' 就交差」的人和「用 10 倍速度交付 production 軟體」的人的關鍵。

而招募主管已經開始注意了。

## 那些該讓你警覺的數字

讓我拿數據說話：

→ **84% 的開發者**現在每週都在使用 AI coding 工具。

→ 頂尖公司中 **95% 的工程師**至少每週使用 AI 工具——75% 的人有一半以上的工作依賴 AI。

→ Claude Code 在 2026 年初達到了 **25 億美元的年化營收**。Anthropic 的總營收年化達到了 140 億美元。

→ 2024 到 2025 年間，junior 開發者的職缺**下降了 67%**。

→ 招募主管不再要求候選人從零開始寫程式。他們開始觀察**候選人如何與 AI 協作**。

再讀一次最後一條。整個面試範式已經轉變。問題不再是「你會不會寫程式？」——而是「你能不能指揮 AI 來構建 production 軟體？」

而這種指揮能力最清楚的證明？就在你的 `.claude/` 資料夾裡。

## .claude/ 裡面到底有什麼（以及為什麼重要）

如果你從來沒打開過你的 `.claude/` 目錄，你正在浪費你最大的職涯資產。讓我逐一說明。

### 1. CLAUDE.md — 你的作業系統

每個 Claude Code 專案啟動時都會讀取 CLAUDE.md 檔案。不要把它當成 prompt——而是把它當成你 AI agent 的作業系統。它告訴 agent 你的世界如何運作：你的專案結構、命名規範、東西放在哪、什麼絕對不能碰。

最厲害的開發者不會寫一個扁平的檔案。他們會建立一套層級結構：

→ **Global (~/.claude/CLAUDE.md)**：你的通用偏好——語氣、快捷鍵、編輯器習慣

→ **Workspace root**：專案架構、技術棧、部署模式

→ **Subdirectory-level**：針對 /docs、/tests、/api 的領域特定規則

有個開發者建了一套 6 層層級、106 行的配置，涵蓋語音筆記路由、憑證管理、命名規範的強制執行——全部在 agent 寫第一行程式碼之前就設定好了。

**這向招募主管傳遞的信號是：**你用系統化思維來做事。你不只是在下 prompt——你在做架構設計。你理解在 2026 年，context management 才是真正的工程瓶頸。

### 2. Skills — 讓你的 Agent 獲得超能力的 Markdown

Skill 是放在 `~/.claude/skills/` 裡的 `.md` 檔案，教 Claude 如何處理特定任務。不需要 SDK、不需要 API、不需要 build step。你用 markdown 寫指令，Claude 就會照做。

有三種模式：

→ **Pattern A — Prompt-Only**：純 markdown 指令。最適合 coding standards、review checklists、commit message 格式化。

→ **Pattern B — Prompt + Scripts**：Markdown 指令搭配 Python/JS scripts。最適合資料驗證、PDF 處理、報告生成。

→ **Pattern C — Prompt + MCP + Subagents**：完整的外部服務整合。最適合端到端的工作流程，像是建立 issues、生成 branches、修程式碼、開 PRs。

Anthropic 官方的 Skills repository 提供了 17 個 production-ready 的 skills——從 frontend-design（一句話生成 production UI）到 webapp-testing（不需要 boilerplate 的 Playwright 自動化）到 skill-creator（一個能建立其他 skills 的 meta-skill）。

但真正的大招？是自己建。

有個開發者建了一個「Fix Ticket」skill，它會讀取 Jira ticket、用 Playwright 重現 bug、研究並規劃修復方案、透過 multi-agent code review 實作、在瀏覽器中驗證修復、commit、deploy 到 Vercel，然後交給 QA。全程不用碰一行程式碼。

另一個人建了 29 個 skills、5 個 agents 的配置，包含 blockchain analytics、三語內容跨平台發佈、以及自動化的 session wrap-ups。

**這向招募主管傳遞的信號是：**你不做重複性的工作，你把它自動化。你把你的領域專業知識捕捉成一種可攜帶的、有版本控制的格式，任何團隊成員第一天就能使用。

### 3. Hooks — 證明你能交付 Production 的安全網

Hooks 是在特定事件觸發時自動執行的 shell scripts：commit 之前、tool call 之後、當 agent 試圖編輯某些檔案時。

把它們想成你 AI agent 的護欄：

→ **Pre-commit hooks**：阻擋包含 .env 檔、API keys 或憑證的 commits。你的 agent 根本不可能洩漏 secrets。

→ **Bash safety guards**：90 多行 regex 阻擋像 `rm -rf` 這樣的危險指令。Agent 會建議移到垃圾桶來替代。

→ **Plan review gates**：強制 agent 在執行前先提出方案。兩個 hooks 協同運作——一個是 enforcer，一個是 blocker。

→ **PDF processing hooks**：攔截檔案讀取，把 50K tokens 壓縮到 2K，保持 context windows 乾淨。

**這向招募主管傳遞的信號是：**你理解 production-grade 的安全性。你不是盲目信任 AI——你會約束它。你在問題發生之前就考慮了失敗模式。這就是 senior engineer 的思維。

### 4. MCP Servers — 你的整合層

MCP（Model Context Protocol）讓 Claude 能與外部服務溝通。你在 `.mcp.json` 中定義 servers，agent 就獲得了新的工具。

實際的配置案例：

→ **Deployment servers**：Agent 可以部署應用、重啟服務、查 logs、管理資料庫。有個開發者的 agent 能把自己重新 deploy 到 production。

→ **Communication integrations**：完整的 Telegram/Slack 存取。一個 server agent 在開發者睡覺時回答 Telegram 頻道裡的問題。

→ **Dual-model review**：Claude 寫計劃，送到 OpenAI 的 Codex 做獨立 review，然後吸收回饋。兩個 AI agent 互相交叉檢查。

**這向招募主管傳遞的信號是：**你建構的是整合，不是孤島。你理解 AI agent 的價值會隨著它能觸及的系統數量而增長。

## 為什麼招募主管開始在意

讓我講直白一點：2026 年的面試流程正在經歷自 LeetCode 成為預設標準以來最大的變革。

Tyler Folkman 經常負責招聘工程師，他寫了一段話，值得每個開發者停下來好好讀：

> 「區分一個只會 vibe-code 出 demo 的開發者和一個能交付可靠軟體的開發者的，是 prompt 和 commit 之間發生的事情。他們會在下 prompt 前先寫 spec 嗎？他們會批判性地閱讀輸出嗎？他們會寫測試嗎？還是把 AI 當成求神問卦就算了？」——Tyler Folkman

他不再要求候選人從零開始寫程式。他現在觀察的是他們如何與 AI 協作。

一位年薪超過百萬美元的頂尖 AI/ML 工程師對當前局面的評價：

> 「Claude Code 仍然是我的 MVP。它應該讓每個人都更有生產力，但說實話，因為機會變多了，感覺大家反而工作得更努力了。」

在 LinkedIn 上分享這段話的招募主管補充道：「技術面試正在轉變。對獨立手寫程式碼的環節越來越不重視。對架構、系統思維、以及你如何有效利用 AI 工具來放大產出的重視程度越來越高。」

而你的 `.claude/` 資料夾的關鍵在於：**它是可審計的。**

一個 CLAUDE.md 檔案，任何工程師 30 秒就能讀完。一個 Skills 目錄，清楚展示你自動化了哪些工作流程。Hooks 展示你強制執行了哪些安全約束。MCP configs 展示你整合了哪些系統。

拿這個跟傳統履歷上寫的「精通 Python」比比看——後者對招募主管來說，完全看不出你在 2026 年到底是怎麼構建軟體的。

## 如何建立你的 Skill 作品集（Step by Step）

這是你該停止閱讀、開始動手的部分。以下是確切的系統。

### Step 1：建立基礎架構

如果你的 `.claude/` 目錄結構還不存在，現在就建立：

```
~/.claude/
├── CLAUDE.md          # 全域偏好設定
├── skills/            # 你的 skill 作品集
│   ├── my-skill/
│   │   ├── SKILL.md       # 指令
│   │   ├── scripts/       # 自動化程式碼
│   │   └── references/    # 領域知識
│   └── ...
├── hooks/             # 安全護欄
└── .mcp.json          # 外部整合
```

### Step 2：寫一個展示系統思維的 CLAUDE.md

不要堆砌一整面文字牆。像寫工程文件一樣結構化：

→ 從你的專案架構開始——什麼東西在哪裡、技術棧是什麼

→ 定義命名規範和 coding standards

→ 設定邊界——agent 絕對不能修改的檔案、必須遵循的 patterns

→ 加入路由邏輯——「如果 X，就做 Y」的決策樹，處理常見的工作流程

→ 控制在 150 行以內。隨著工作流程演進，每週更新

### Step 3：建立你的前 5 個 Skills

從這些高信號值的 skill 類別開始：

→ **領域特定分析器**：一個處理你最常接觸的資料類型的 skill（銷售 CSV、API logs、客戶 tickets）

→ **Code review enforcer**：一個檢查你團隊特定 patterns 的 skill，不只是 linting

→ **文件生成器**：一個按照你團隊的格式和語氣產出文件的 skill

→ **Deployment workflow**：一個端到端處理你 CI/CD pipeline 的 skill

→ **Testing automation**：一個按照你的測試哲學來撰寫和執行測試的 skill

用 Anthropic 的 skill-creator meta-skill 來快速搭建框架。先 brain dump 你的工作流程，讓 Claude 問你後續問題，反覆迭代直到它完全掌握你的流程。然後儲存 SKILL.md 並分享出去。

### Step 4：加上證明 Production 思維的 Hooks

至少加這些：

→ 一個 pre-commit hook，阻擋 secret 檔案（.env、.key、.pem）

→ 一個 bash safety guard，防止破壞性指令

→ 一個 plan-review gate，在重大變更前要求你批准

光是這三個 hooks，就比一年的 commit 歷史更能向招募主管證明你的 production 思維。

### Step 5：公開分享

這是大多數人跳過的步驟。別跳過。

→ 把你的 `.claude/` 配置 push 到公開的 GitHub repository

→ 寫一份 README 說明你的理念——為什麼選這些 skills、為什麼用這些 hooks、每個解決了什麼問題

→ 用 Agent Skills 標準把你最好的 skills 發佈成 open-source packages

→ 在 X/Twitter 和 LinkedIn 上分享你的 setup 拆解——「Claude Code setup」這個題材現在正在爆發

有個開發者在 Reddit 上分享了他的 116 項配置——29 個 skills、8 個 hooks、5 個 agents、22 個 rules 檔案——然後就爆紅了。他在 MIT license 下 open-source 了整個配置。

那個 repo 現在比任何傳統的作品集網站都更令人印象深刻。

## 沒人幫你準備好的職涯轉變

SignalFire 的 State of Tech Talent Report 講得很清楚：公司不再找潛力——他們找的是證據。Big Tech 的應屆畢業生招募自 2022 年以來減少了超過一半。公司開出 junior 職缺，但用 senior IC 來填補。

在這個市場裡，「我會 Python」毫無意義。「我建了一套 15 個 skills 的 Claude Code 配置，自動化了我們整個 code review pipeline，把 PR turnaround 從 3 天縮短到 4 小時」——這才能讓你接到面試電話。

Khe Hy 說得很精闢：

> 「是時候建立你的新作品集了：Skills。這就是在這個 AI-first 世界生存所需要的。一系列 SKILL.md 檔案，才是區分 AI 高手和一般人的關鍵。」——Khe Hy

Agent-native 的開發者不只是寫程式碼。他們設計的是由約束、工作流程和整合組成的系統，讓 AI agents 變得可預測、安全、快速。

而這整個系統的每一個部分，都住在同一個資料夾裡。

## 結論

在 2024 年，你的履歷是一份 PDF。在 2025 年，是你的 GitHub commit 歷史。在 2026 年，是你的 `.claude/` 資料夾。

不是因為它很潮。而是因為它是你實際工作方式最誠實的呈現。每一個 CLAUDE.md 檔案展示你如何思考。每一個 Skill 展示你自動化了什麼。每一個 Hook 展示你堅持不破壞什麼。每一個 MCP config 展示你能連接什麼。

招募主管只要花 5 分鐘掃過你的 `.claude/` 目錄，就能讀懂你整個工程哲學。不需要白板面試。不需要 LeetCode。不需要「跟我說一次你曾經...」的行為面試。

就是：這是我的系統，這是它做什麼，這是它為什麼能運作。

這就是新的履歷。

開始建立你的吧。
