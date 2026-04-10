---
title: "Compound Engineering：AI 原生的工程哲學完整指南"
date: 2026-04-10
categories: [AI]
tags: [compound-engineering, ai-agents, every, software-engineering, claude-code, code-review, workflow, plugin]
---

> 原文：[Compound Engineering](https://every.to/guides/compound-engineering) by **Kieran Klaassen** (Every)
> 發佈日期：2026 年 1 月 17 日（最後更新：2026 年 4 月 8 日）

---

Compound engineering 誕生於從零開始打造 Cora——一個為你的收件匣設計的 AI 幕僚長——的過程中。當開發者們在無數 PR 中實戰測試各種 pattern、agent 和 workflow 時，他們發展出了一套生產力技巧，最終演化成一套系統化的 AI 輔助開發方法。之所以分享這套哲學，是因為實踐者們相信 compound engineering 將會成為軟體開發的預設方式。

### 哲學

核心原則：「每一個工程工作單位都應該讓後續的工作變得更容易——而不是更困難。」

大多數 codebase 會隨著時間推移而劣化，因為功能不斷累積複雜度。十年之後，團隊花在管理系統上的精力比花在建設系統上的還多，因為每個新功能都需要跟既有功能進行協調。codebase 變得越來越難以理解、修改和信任。

Compound engineering 逆轉了這個軌跡。功能不再引入脆弱性，而是教會系統新的能力。Bug 修復消除的是整個類別的未來 bug。被文件化的 pattern 成為後續工作的工具。隨著時間推移，codebase 變得更容易理解、修改和信任。

### 主循環

Every 運營五個產品——Cora、Monologue、Sparkle、Spiral 和 Every.to——主要使用單人工程團隊。支撐這一切的系統是一個四步驟循環：

**Plan → Work → Review → Compound → 重複**

前三個步驟類似傳統的開發 workflow。第四個步驟是 compound engineering 與傳統 AI 輔助開發的區別所在。這是累積收益發生的地方。跳過它，就只是加了 AI 輔助的傳統工程。

這個循環無論是修一個五分鐘的 bug 還是做一個多天的功能都完全一樣——唯一的差異只是每個步驟的持續時間不同。

最佳時間分配：Plan 和 review 應該佔工程時間的百分之八十；work 和 compound 佔剩餘的百分之二十。

#### 1. Plan

規劃是把想法轉化為藍圖。關鍵行動：
* 理解需求：要建什麼？為什麼？有什麼限制？
* 研究 codebase：類似的功能是怎麼運作的？存在什麼 pattern？
* 對外研究：framework 文件怎麼說？有什麼已確立的最佳實踐？
* 設計方案：採用什麼方式？哪些檔案需要修改？
* 驗證計畫：這是否連貫？是否完整？

#### 2. Work

執行遵循計畫。agent 負責實作，開發者負責監控。較小的任務：
* 建立隔離環境：Git worktree 或 branch 將工作隔離開來
* 執行計畫：agent 逐步實作
* 執行驗證：每次變更後執行測試、linting 和型別檢查
* 追蹤進度：監控已完成和待完成的工作
* 處理問題：當某些東西壞掉時調整計畫

#### 3. Review（評估）

這個步驟在部署前捕捉問題並記錄學習。行動：
* 讓多個 agent 審查輸出：專門的 reviewer 平行檢查程式碼
* 優先排序發現：標記為 P1（必須修復）、P2（應該修復）或 P3（可以修復）
* 解決發現的問題：agent 根據回饋修復問題
* 驗證修復：確認修復正確且完整
* 擷取 pattern：記錄哪裡出了問題以防止再次發生

#### 4. Compound（最重要的步驟）

傳統開發在第三步就結束了。Compound engineering 的收益來自第四步。行動：
* 擷取解決方案：什麼有效？什麼沒用？可重用的洞見是什麼？
* 讓它可被找到：添加帶有 metadata、tags 和 categories 的 YAML frontmatter
* 更新系統：將新 pattern 加入 CLAUDE.md。需要時建立新的 agent
* 驗證學習成果：系統下次是否能自動捕捉到這個問題？

### Plugin

#### 包含內容
* 26 個專門的 agent
* 23 個 workflow 指令
* 13 個 skill

#### 安裝

Claude Code：
```
claude /plugin marketplace add https://github.com/EveryInc/every-marketplace
claude /plugin install compound-engineering
```

OpenCode（實驗性）：
```
bunx @every-env/compound-plugin install compound-engineering --to opencode
```

Codex（實驗性）：
```
bunx @every-env/compound-plugin install compound-engineering --to codex
```

#### 檔案存放位置

```
your-project/
├── CLAUDE.md              # Agent 指令、偏好和 pattern
├── docs/
│   ├── brainstorms/       # /workflows:brainstorm 輸出
│   ├── solutions/         # /workflows:compound 輸出（已分類）
│   └── plans/             # /workflows:plan 輸出
└── todos/                 # /triage 和 review 發現
```

CLAUDE.md 是 agent 每次 session 會讀取的最關鍵檔案。docs/solutions/ 建立機構知識。todos/ 追蹤工作項目。

### 核心指令

#### /workflows:brainstorm
```
/workflows:brainstorm Add user notifications
```
幫助你腦力激盪要建什麼以及如何建。執行輕量級的 repo 研究、提出澄清問題、提出方案、將決策記錄在 docs/brainstorms/。

#### /workflows:plan
```
/workflows:plan Add email notifications when users receive new comments
```
產生三個平行研究 agent：repo-research-analyst、framework-docs-researcher、best-practices-researcher。spec-flow-analyzer 檢查使用者流程。結果合併為結構化計畫。

#### /workflows:work
```
/workflows:work
```
四個階段：快速啟動（建立隔離的 git worktree）、執行（實作每個任務）、品質檢查（產生五個 reviewer agent）、上線（執行 linting、建立 PR）。

#### /workflows:review
```
/workflows:review PR#123
```
平行產生 14 個以上的專門 agent：security-sentinel、performance-oracle、data-integrity-guardian、architecture-strategist、pattern-recognition-specialist、code-simplicity-reviewer，以及 framework 專用的 reviewer。

##### Review Agent（14 個 agent）

**安全性：** security-sentinel — OWASP top 10、注入攻擊、認證漏洞

**效能：** performance-oracle — N+1 查詢、缺少的 index、快取、演算法瓶頸

**架構：** architecture-strategist — 系統設計、元件邊界、依賴方向；pattern-recognition-specialist — design pattern、anti-pattern、code smell

**資料：** data-integrity-guardian — migration、transaction、參照完整性；data-migration-expert — ID 映射、rollback 安全性

**品質：** code-simplicity-reviewer — YAGNI、複雜度、可讀性；kieran-rails-reviewer — Rails 慣例、Turbo Streams；kieran-python-reviewer — PEP 8、type hint；kieran-typescript-reviewer — 型別安全、現代 ES；dhh-rails-reviewer — 37signals 慣例

**部署：** deployment-verification-agent — 部署前檢查清單、rollback 計畫

**前端：** julik-frontend-races-reviewer — JS/Stimulus 中的 race condition

**Agent 原生：** agent-native-reviewer — agent 可存取的功能

##### 輸出格式
```
P1 - CRITICAL (must fix):
[ ] SQL injection vulnerability in search query (security-sentinel)
[ ] Missing transaction around user creation (data-integrity-guardian)

P2 - IMPORTANT (should fix):
[ ] N+1 query in comments loading (performance-oracle)
[ ] Controller doing business logic (kieran-rails-reviewer)

P3 - MINOR (nice to fix):
[ ] Unused variable (code-simplicity-reviewer)
```

#### /triage
逐一呈現每個發現供人類決策：批准、跳過或自訂。

#### /workflows:compound
```
/workflows:compound
```
產生六個平行 subagent：context analyzer、solution extractor、related docs finder、prevention strategist、category classifier、documentation writer。

#### /lfg
```
/lfg Add dark mode toggle to settings page
```
串連完整 pipeline：plan → deepen-plan → work → review → resolve → browser test → feature video → compound。產生 50 個以上的 agent。

### 需要放下的信念

#### 「程式碼必須手寫」
要求是寫出好的程式碼——可維護的、解決正確問題的程式碼。誰來打字並不重要。

#### 「每一行都必須人工審查」
自動化系統可以捕捉到同樣的問題。如果你不信任它們，就去修好這個系統，而不是什麼都自己來。

#### 「解決方案必須源自工程師」
工程師的角色變成加入品味——知道哪個方案適合這個 codebase、這個團隊和這個情境。

#### 「程式碼是主要產出物」
一個能產出程式碼的系統比任何一段個別的程式碼都更有價值。

#### 「寫程式碼是核心工作職能」
開發者的工作是交付價值。程式碼只是其中一個輸入。

#### 「第一次嘗試就應該寫好」
第一次嘗試大約有百分之九十五的垃圾率。第二次嘗試仍然有百分之五十。讓快速迭代成為你的目標。

#### 「程式碼是自我表達」
程式碼從來都不真正屬於你。它屬於團隊、產品和使用者。放下把程式碼當作自我表達的執念是一種解放。

#### 「打更多字等於學更多」
理解比肌肉記憶重要得多。一個審查十個 AI 實作的開發者比一個手動打兩個的開發者理解更多 pattern。

#### 轉型挑戰
少打字感覺像少做事。放手感覺有風險。「這是誰做的？」功能上線而你沒有直接寫程式碼可能感覺像作弊。但規劃、審查和確保品質就是工作。

### 需要採納的信念

#### 把你的品味萃取到系統中
把偏好寫在 CLAUDE.md 或 AGENTS.md 裡。建立專門的 agent。建立反映你品味的 skill。一旦 AI 理解了你喜歡怎麼寫程式碼，它就會產出你認可的程式碼。

#### 50/50 法則
將百分之五十的工程時間分配給建構功能，百分之五十用於改善系統。花一小時建立一個 review agent 可以在接下來的一年節省十小時的審查時間。

#### 信任流程，建立安全網
信任不代表盲目的信仰。它意味著設立防護措施。當你無法信任輸出時，就加一個讓該步驟變得可信的系統。

#### 讓你的環境成為 Agent 原生
如果開發者能看到或做到什麼，agent 也應該能。執行測試、查看 log、用截圖除錯、建立 PR。

#### 平行化是你的朋友
新的瓶頸是算力——你能同時跑多少個 agent。

#### 計畫是新的程式碼
計畫文件現在是你產出的最重要的東西。

#### 核心原則
* 每一個工作單位都讓後續工作更容易
* 品味屬於系統，不屬於審查
* 教會系統，而不是自己做
* 建立安全網，而不是審查流程
* 讓環境成為 agent 原生
* 在每一處都應用 compound 思維
* 擁抱放手的不適感
* 交付更多價值。少打程式碼。

### 入門——五個階段

#### 階段 0：手動開發
逐行手寫程式碼，不使用 AI。

#### 階段 1：對話式輔助
把 AI 當作智慧參考工具使用。複製貼上有用的程式碼片段。

#### 階段 2：Agentic 工具搭配逐行審查
Agentic 工具進入 workflow。你批准或拒絕每一個提案。大多數開發者停滯在這裡。

#### 階段 3：計畫優先，只在 PR 層級審查
你和 AI 協作制定詳細計畫。然後你離開。在 PR 層級進行審查。Compound engineering 從這裡開始。

#### 階段 4：從想法到 PR（單一機器）
你提供一個想法。agent 處理一切：研究、規劃、實作、測試、自我審查、建立 PR。你的參與：構思、PR 審查、merge。

#### 階段 5：平行雲端執行（多裝置）
執行移到雲端。多個 agent 平行工作。你從任何地方指揮 agent。你在指揮一支艦隊。

### 如何升級

#### 0 → 1：開始協作
選一個工具。先提問。把樣板程式碼委派出去。審查每一件事。Compounding 行動：記下效果好的 prompt。

#### 1 → 2：讓 Agent 進來
切換到 agentic 模式。從目標性的小修改開始。批准每個動作。審查 diff。Compounding 行動：建立一份 CLAUDE.md 記錄偏好。

#### 2 → 3：信任計畫（關鍵轉型）
投資在規劃上。讓 agent 做研究。讓計畫明確化。執行然後離開。在 PR 層級審查。Compounding 行動：記錄計畫遺漏了什麼。

#### 3 → 4：描述，不要規劃
給出成果，而不是指令。讓 agent 來規劃。批准方案。審查 PR。Compounding 行動：建立一個以成果為導向的指令庫。

#### 4 → 5：把一切平行化
把執行移到雲端。跑平行工作流。建立佇列。啟用主動運作模式。Compounding 行動：記錄哪些任務適合平行化。

### 三個問題

在批准任何 AI 輸出之前：
1. 「你在這裡做的最困難的決定是什麼？」
2. 「你否決了哪些替代方案？為什麼？」
3. 「你最沒把握的部分是什麼？」

### 最佳實踐

#### Agent 原生架構

Agent 原生意味著賦予 agent 跟你一樣的能力。

**檢查清單：**
開發環境：在本機執行應用程式、執行測試、執行 linter、執行 migration、seed 資料。
Git 操作：建立 branch、做 commit、push、建立 PR、閱讀 PR 留言。
除錯：查看本機 log、查看 production log（唯讀）、截圖、檢查網路請求、存取錯誤追蹤。

**漸進式 Agent 原生（4 個層級）：**
層級 1：基礎開發——檔案存取、測試、commit
層級 2：完整本機——瀏覽器、本機 log、PR
層級 3：Production 可見性——production log、錯誤追蹤、監控
層級 4：完整整合——票務系統、部署、外部服務

#### 跳過權限提示

`--dangerously-skip-permissions` 關閉權限提示。

適用時機：你信任流程、你在安全的環境中、你想要速度。
不適用時機：你在學習中、你在 production 環境中、你沒有好的 rollback 機制。

不需要提示的安全性：Git 是你的安全網、測試捕捉錯誤、merge 前審查、worktree 隔離風險。

#### 設計 Workflow

**Baby App 方法：** 建立一個可拋棄的專案來做設計迭代。一旦設計感覺對了，把 pattern 萃取到主專案中。

**UX 探索循環：** 生成多個版本、逐一點擊操作、分享給使用者、收集回饋、刪除然後從正式計畫開始。

**與設計師合作：** 設計師建立 Figma 稿 → AI 實作 → figma-design-sync agent 檢查是否吻合 → 設計師審查實際版本 → 迭代。

**將設計品味系統化：** 在 skill 檔案中寫下設計偏好。

**設計 Agent：** design-iterator、figma-design-sync、design-implementation-reviewer。

#### Vibe Coding

給那些不在意程式碼——只想要結果的人。

快速路徑：描述 → 等待 → 檢查是否可用。

適合：個人專案、原型、實驗、內部工具、UX 探索。
不適合：Production 系統、其他人會維護的程式碼、對安全性或效能敏感的系統。

Vibe Coding 悖論：用 vibe code 來發現你想要什麼，然後用規格來正確地建構它。

#### 團隊協作

**新的團隊動態：** 成員 A 建立計畫 → AI 實作 → AI agent 審查 → 成員 B 審查 AI 的審查 → Merge

**團隊標準：** 要求明確的計畫簽核。PR 擁有者 = 發起工作的人。人類 reviewer 專注於意圖，而不是實作。

**溝通模式：** 預設非同步。明確的交接，附帶狀態、context 和如何繼續。

**擴展模式：** 清晰的所有權 + 非同步更新。Feature flag + 小型 PR。Compound 文件 = 部落知識。

#### 使用者研究

結構化研究，讓 AI 能使用它。建立 persona 文件。將洞見連結到功能。

**傳統流程：** 研究報告放在 Google Drive → 開發者永遠不會讀它
**Compound 流程：** 結構化洞見 → AI 在規劃時參考洞見 → 功能符合使用者需求

#### 資料 Pattern 萃取

尋找：重度使用 pattern、困難 pattern、變通方法 pattern、放棄 pattern。
從 pattern 到功能：注意行為 → 識別洞見 → 建構功能。

#### 文案撰寫

從一開始就在計畫中包含文案。在 skill 檔案中將你的語氣系統化。像審查程式碼一樣用 copy-reviewer agent 審查文案。

#### 產品行銷

建構功能的同一套系統也能宣傳它們。從計畫生成 release note、建立社群貼文、用 Playwright 擷取截圖。
