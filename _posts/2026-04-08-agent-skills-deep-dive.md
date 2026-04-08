---
title: "addyosmani/agent-skills 深度拆解：把資深工程師的本能變成 AI agent 的紀律"
date: 2026-04-08
categories: [AI]
tags: [claude-code, skills, ai-agents, software-engineering, plugin, addy-osmani]
---

> 原始 repo：[addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) by **Addy Osmani**

---

## 為什麼這個 repo 值得被認真看待

Addy Osmani 在這個 repo 的 README 開門見山就丟了一句很狠的話："AI coding agents default to the shortest path — which often means skipping specs, tests, security reviews."

這句話不是抱怨，是診斷。任何認真用 AI agent 寫過幾天 production code 的人都知道：模型不是不會做這些事，而是預設會跳過。它會「猜」需求、會跳過測試、會在不確定時繼續往下寫、會把 fix-on-fix 堆成一座地獄。資深工程師之所以是資深工程師，不是因為他們知道更多 syntax，而是因為他們知道**哪些事不能跳**。

`agent-skills` 這個 repo 想做的，就是把那一套「不能跳」的紀律外顯成 agent 看得懂的流程文件——19 個 skill，覆蓋從 idea refinement 一路到 shipping 與 launch 的整個生命週期。它不是另一套 prompt 模板，也不是「給我寫個 React component」那種工具型 plugin，而是一份**寫給 AI 的工程紀律手冊**。

而且很值得一提的是：它**完全模型無關**。docs 目錄裡同時有 Claude Code、Cursor、Gemini CLI、Windsurf、OpenCode、GitHub Copilot、OpenAI Codex 的安裝指南。作者刻意把它做成跨廠商的中性規格。這在 2026 年 plugin 生態系開始百家爭鳴的時刻，是一個很有遠見的決定。

repo 在 2026 年 2 月開張，到我寫這篇時已經 9k stars、19 個 skill、52 個檔案，作者每週都在更新。MIT 授權。

接下來我會帶你拆解：在這個 repo 的語境裡，「skill」到底是什麼東西、19 個 skill 各做什麼、6 個我認為最值得學習的設計、整個 repo 的招牌設計模式，最後是我對它的誠實評論——包含哪些做得漂亮、哪些其實有點粗糙。

## skill 在這裡到底是什麼？

先講結論：**skill 不是 prompt，是 process document。**

每個 skill 是一個資料夾，必須包含一份 `SKILL.md`。frontmatter 規範非常明確（見 `docs/skill-anatomy.md` L307-312）：

```yaml
---
name: skill-name-with-hyphens
description: Brief statement ... Use when [specific trigger conditions].
---
```

幾條鐵律：

- `name` 必須與目錄同名、小寫、用連字號
- `description` 上限 1024 字元，**必須以第三人稱動詞開頭**（"Creates specs…", "Drives development…", "Hardens code…"）
- `description` **不能寫成流程摘要**——這是 `docs/skill-anatomy.md` L318 特別警告的——否則 agent 會跟著摘要走而不去讀完整 skill

第三條是這份規範裡最反直覺也最深刻的一條。Anthropic 官方文件也提過類似觀點，但 addyosmani 在這裡把它寫成強制規則：description 是讓 agent **判斷要不要讀**的，不是讓 agent **執行**的。一旦你在 description 裡寫了步驟，agent 就會在沒讀完 SKILL.md 主體的情況下開始走捷徑。

`SKILL.md` 主體有七個標準章節（`docs/skill-anatomy.md` L322-353）：

1. **Overview**
2. **When to Use**（含 When NOT to use）
3. **Core Process / The Workflow**
4. **Specific Techniques / Patterns**
5. **Common Rationalizations** ←整個 repo 的招牌
6. **Red Flags**
7. **Verification**（帶 checkbox 的 exit criteria）

我特別把第 5 個用箭頭標出來，因為這是 addyosmani 對 agent skill 設計最原創的貢獻。我們等會兒會專門拆解這個東西。

檔案佈局也有規範：

```
skills/skill-name/
  SKILL.md           # 必要
  supporting-file.md # 選用，>100 行才拆
  scripts/           # 少數 skill 才有
```

而所有共享的 reference checklist（測試模式、安全清單、效能清單、無障礙清單）都集中在 repo 根目錄的 `references/`，**不放進 skill 子目錄**（`docs/skill-anatomy.md` L404）。這跟 Anthropic 官方範例常見的「reference 放進 skill 子目錄」不一樣——addyosmani 選擇把通用 checklist 抽到頂層共享，避免重複。

## 19 個 skill 全圖：六階段開發生命週期

19 個 skill 並不是隨意放在 `skills/` 下，作者按照軟體開發的六個階段做了清楚的分組。我把全部列出來，每個一句話：

### Define（2 個）

- **`idea-refine`** — 透過 divergent / convergent 思考把模糊想法轉成一頁 PRD
- **`spec-driven-development`** — 寫 PRD 再寫 code，四階段 gated workflow

### Plan（1 個）

- **`planning-and-task-breakdown`** — 把 spec 拆成有接受條件、有依賴順序的原子任務

### Build（5 個）

- **`incremental-implementation`** — thin vertical slices，每片都 implement → test → verify → commit
- **`test-driven-development`** — Red / Green / Refactor，Beyonce Rule，80/15/5 test pyramid
- **`context-engineering`** — 管理 agent context 的節奏，rules file / MCP / context packing
- **`frontend-ui-engineering`** — 元件架構、設計系統、WCAG 2.1 AA 無障礙
- **`api-and-interface-design`** — contract-first，Hyrum's Law，One-Version Rule

### Verify（2 個）

- **`browser-testing-with-devtools`** — 用 Chrome DevTools MCP 抓 runtime data
- **`debugging-and-error-recovery`** — Stop-the-line、五步驟 triage：reproduce → localize → reduce → fix → guard

### Review（4 個）

- **`code-review-and-quality`** — five-axis review、~100 行變更上限、Nit / Optional / FYI 嚴重度標籤
- **`code-simplification`** — Chesterton's Fence、Rule of 500，簡化但行為必須完全保留
- **`security-and-hardening`** — OWASP Top 10、Three-Tier Boundary（Always / Ask first / Never）
- **`performance-optimization`** — 測量先行、Core Web Vitals、bundle analysis

### Ship（5 個）

- **`git-workflow-and-versioning`** — trunk-based、atomic commits、commit 即 save point
- **`ci-cd-and-automation`** — Shift Left、Faster is Safer、feature flag pipeline
- **`deprecation-and-migration`** — code-as-liability、compulsory vs advisory deprecation
- **`documentation-and-adrs`** — Architecture Decision Records，文件只寫 why
- **`shipping-and-launch`** — pre-launch checklist、feature flag lifecycle、staged rollout

### Meta（1 個）

- **`using-agent-skills`** — 決策樹式的 skill 挑選器，會話開始時最先載入

光是看這份清單你大概就能感覺到：這不是「給我寫一段 React」的工具集合，這是一份**把 staff engineer 的工作風格寫下來給 agent 看**的清單。Hyrum's Law、Beyonce Rule、Chesterton's Fence、trunk-based development、Shift Left、Core Web Vitals——這些都是 Google SWE book 裡你會看到的概念，addyosmani 把它們不只是引用，而是落地成具體的 process，這是我認為這個 repo 最有價值的地方。

## 6 個值得深挖的 skill

接下來我挑 6 個我認為最有教育意義的 skill 來深拆。每一個我都會講「它真正做了什麼聰明的事」，而不只是「它要 agent 做什麼」。

### 1. `test-driven-development`：用 failing test 證明 bug 存在

這個 skill 的核心問題很明確：agent 最常見的退化模式就是「先寫實作再補測試」，或乾脆不寫。它的解法分兩層。

**第一層**是把 Red / Green / Refactor 三階段畫成 ASCII 流程圖（L36-44）——對 LLM 友善的結構，比文字段落更容易 parse。

**第二層**才是聰明的：它定義了一個叫 **"Prove-It Pattern"** 的東西——任何 bug 都必須先寫一個 failing test 來複現它，然後才能動修。它還明文寫死："A test that passes immediately proves nothing."

我看過很多 AI agent 在 debug 時的失敗模式都長一樣：agent 看到 error，猜一個 fix，然後 run test 通過了就宣稱搞定。但它跑的 test 跟那個 bug 一點關係都沒有。Prove-It Pattern 的厲害之處是把這個失敗模式從根上堵住——你必須先有一個明確會 fail 的 test，再去寫 fix，這樣 test 從紅到綠才有意義。

金句："Tests are proof — 'seems right' is not done."

### 2. `spec-driven-development`：把 silent assumption 變成顯式清單

agent 最危險的習慣不是寫錯 code，而是**沉默地假設**。它在不確定需求的時候，會自己腦補一個版本然後動工，等你看到結果才發現方向錯了三公里。

這個 skill 的解法是四階段 gated workflow：**SPECIFY → PLAN → TASKS → IMPLEMENT**，每一階段都要人類簽核才能進下一階段（L108-114）。但更聰明的是它強制 agent 在 SPECIFY 階段列出一份叫 **`ASSUMPTIONS I'M MAKING`** 的清單（L122-128），把所有它打算腦補的東西全部攤開來給人看，等人類確認後才能動。

spec 本身有六大必備欄位：Objective / Commands / Project Structure / Code Style / Testing Strategy / Boundaries。其中 **Boundaries** 是這個 repo 最有特色的設計——它是一個三層制（L159-162）：

- **Always do**（無例外）
- **Ask first**（需要人類同意）
- **Never do**（禁止）

這個三層制叫 **Three-Tier Boundary**，是 addyosmani 的招牌設計模式，會在好幾個 skill 裡重複出現。我等會兒會單獨講它。

金句："Code without a spec is guessing."

### 3. `debugging-and-error-recovery`：Stop-the-line + 五步驟 triage

agent 在 debug 時最可怕的退化模式是「再試一次」、「換個寫法」、「加個 try-catch 蓋過去」。這個 skill 把這個失敗模式直接堵死，用的是兩個機制。

第一個是 **"Stop-the-Line Rule"**（L266-275）：任何意料之外的事情發生（test 失敗、API 回 500、build 失敗），agent 必須**立刻停止**新增功能、保留證據、照 triage checklist 走。這個概念來自 Toyota 的 andon cord——任何工人在生產線上看到問題都有權拉繩子停下整條線。把這個搬到 agent 上面，就是強制中斷它的「再試一次」迴路。

第二個是五步驟 triage：

1. **Reproduce** — 穩定重現
2. **Localize** — 縮小範圍
3. **Reduce** — 做出最小重現
4. **Fix** — 改
5. **Guard** — 加 test 防止再犯

但這個 skill 真正讓我覺得作者很懂的，是它對「無法穩定重現」的情境提供了一張決策樹（L299-317），分成 **Timing / Environment / State / Random** 四種根因類型，每種都附具體動作（加 timestamp、加 artificial delay、比較 Node 版本、清 cache、檢查 race condition 等等）。**這是把資深工程師那種「我不知道它為什麼有時會掛掉」的直覺，外顯成 checklist。**

金句："Guessing wastes time."

### 4. `api-and-interface-design`：把 Hyrum's Law 變成設計原則

Hyrum's Law 是 Google SWE book 裡的一個觀察："With a sufficient number of users of an API, it does not matter what you promise in the contract: all observable behaviors of your system will be depended on by somebody."

意思是：你的 API 不只有 contract，每一個**可觀察的行為**——回應時間、錯誤訊息格式、回傳順序、甚至 typo——都會變成有人依賴的隱性合約。

addyosmani 把這個概念直接寫成這個 skill 的核心設計原則（L347-357）："every observable behavior is a potential commitment"。這是給 agent 的一個非常重要的提醒：你設計 API 的時候不能只想「能用」，你必須想**未來會被綁住的所有觀察點**。

skill 還塞了另一個 Google 文化的精華——**One-Version Rule**（L358-361）：永遠只維護一個版本，不要被「同時支援 v1 和 v2」的誘惑騙走。這對 agent 特別重要，因為 agent 預設會傾向「保留舊版本以防萬一」這種看似安全實則昂貴的選擇。

最後它還給了一個統一的 `APIError { code, message, details }` 結構（L390-400），把 error contract 也標準化。整個 skill 給人的感覺是：作者把 staff engineer 對 API 設計的所有 instinct 全部翻譯成 agent 看得懂的規格。

金句："Good interfaces make the right thing easy and the wrong thing hard."

### 5. `incremental-implementation`：每 100 行就停下來

agent 最愛的事情是一口氣寫 500 行 code 然後整包炸掉。這個 skill 的解法粗暴但有效：每個 slice 必做 **Implement → Test → Verify → Commit → 下一片**（L428-447）。

它甚至明文寫死了一條觸發條件（L422-423）："任何時刻你想寫超過 ~100 行而不測試，就應該用這個 skill。"

100 行這個門檻不是隨便挑的。它是一個對 LLM 來說剛好的數字——夠大可以做有意義的事，夠小可以一次性 review。配合 `git-workflow-and-versioning` skill 的「commit 即 save point」哲學，這套組合天然支援 agent 反悔與回退：每 100 行就 commit 一次，任何時候出錯都可以乾淨地 reset 回去。

skill 還提供三種切片策略：

- **Vertical Slices**（首選）— 端到端的薄片，每片都是可運行的功能
- **Contract-First Slicing** — 前後端平行開發，先定 contract 再分頭實作
- **Risk-First Slicing** — 先做最不確定的部分，盡早消除技術風險

金句："Each increment should leave the system in a working, testable state."

### 6. `code-simplification`：簡化但不能變成 churn

簡化程式碼是 agent 最容易做爛的事情之一。它要嘛不簡化，要嘛過度重寫——你叫它清理一個 function，它順手把整個 module 重構成它覺得「更乾淨」的樣子，然後 break 了五個你沒注意到的呼叫點。

這個 skill 的第一條原則就是：**Preserve Behavior Exactly**（L194-205），附上一個四問 checklist：

- 同樣的輸出？
- 同樣的錯誤？
- 同樣的副作用與順序？
- 所有既有的 test 不改就能過？

四個問題只要有一個不確定，就不准動。它還內建 **Chesterton's Fence** 原則：在你不理解一段 code 為什麼存在之前，不要把它拆掉。

更聰明的是它的唯一驗收條件（L175）：**"Would a new team member understand this faster than the original?"** 如果答案不是明確的「是」，那這次簡化就不算成功——它只是 churn，不是 improvement。

skill 還明確列出 **When NOT to use**（L186-191），避免 agent 為簡化而簡化：

- 不理解程式碼之前不能簡化
- 即將整個重寫的 code 不值得簡化
- 為了個人偏好而簡化（風格不算）

值得一提的是，這個 skill 的開頭第 3 行寫了一句非常坦白的話："Inspired by the Claude Code Simplifier plugin. Adapted here as a model-agnostic, process-driven skill."——它直接致敬 Anthropic 官方的 `code-simplifier` plugin，並且明說自己是把它改寫成「跨模型、流程導向」的版本。這句話某種程度上概括了整個 repo 的定位。

金句："Simplification that breaks project consistency is not simplification — it's churn."

## 整個 repo 的招牌設計模式

讀完 19 個 skill 之後你會發現幾個重複出現的設計手法。這些是 addyosmani 的「個人風格」，也是這個 repo 跟其他 skill collection 最不一樣的地方。

### 1. Three-Tier Boundary：Always / Ask first / Never

這是 addyosmani 的簽名 pattern。它出現在 `spec-driven-development`（spec 的 Boundaries 欄位）、`security-and-hardening`（L669-700 的 Three-Tier Boundary 章節），以及好幾個其他 skill 裡。

為什麼這個三層制這麼重要？因為它解決了 agent 與人類協作裡最模糊的一個問題：**哪些事 agent 可以自己決定，哪些必須問**。傳統的「自動化 vs 手動」二分法太粗糙，實際工作有大量「我可以做但你最好先看一眼」的灰色地帶。Three-Tier 把這個灰色地帶外顯出來：

- **Always do** — 無例外，agent 直接執行（例如：每次 release 前跑 `npm audit`）
- **Ask first** — agent 必須先問人類（例如：刪除任何資料庫表）
- **Never do** — 禁止，agent 應該拒絕（例如：把 secret 寫進 client storage）

我認為這個三層制是這個 repo 對 agent UX 設計的最大貢獻。它應該被整個產業抄走。

### 2. Common Rationalizations：把藉口外顯

這是 repo 裡我最喜歡的設計。每個 skill 都必須包含一張「Common Rationalizations」表格，列出 agent 在這個情境下可能的藉口，**並且預先寫好反駁文**。

舉 `test-driven-development` 為例，它的 rationalization 表格大概長這樣：

| Rationalization | Reality |
|---|---|
| "I'll add tests later" | 你不會。Later 永遠不會來 |
| "This is too simple to test" | Simple code 也會壞，test 只要 30 秒 |
| "I'll keep this as reference and write tests after" | 你會適配 reference，這就是 tests-after |

這個設計的哲學是：**大多數失敗模式不是 agent 不知道要做什麼，而是 agent 會說服自己「這次例外」。** 把那些「這次例外」的具體說詞列出來並且事先反駁，就讓 agent 沒有 verbal escape hatch。

`docs/skill-anatomy.md` L370-374 把這個設計目的講得很清楚：要讓 agent **無話可說**。

我看過很多 prompt engineering 範例都試圖用「正面說明」來引導 agent，但我覺得這個 repo 的「負面預先反駁」是更深刻的觀察——LLM 是 token predictor，它最會的就是順著自己的 reasoning 一路往下生成。如果你不在它的 reasoning path 上預先擺好反駁，它就會自己生成出鬼話。

### 3. ASCII 流程圖：對 LLM 友善的結構

幾乎每個 skill 都有 box-drawing 的 ASCII 流程圖。這不是審美選擇——它對 LLM 比連續文字段落更容易 parse。當 agent 需要決定「現在該執行哪個 step」時，從一張視覺結構清楚的流程圖裡找答案，比從一段三百字的描述裡找答案要可靠得多。

### 4. Cross-skill 參照而非複製

`docs/skill-anatomy.md` L406-415 明文規定：**"Don't duplicate content between skills — reference and link instead."** 共享的 testing patterns、security checklist、performance checklist、accessibility checklist 全部抽到頂層的 `references/` 目錄。

這個決定的意義是：當 agent 載入一個 skill 時，它只會載入這個 skill 自己的 process，需要 reference 才會額外載入。這是一種 **progressive disclosure**——避免 token 爆炸。

### 5. Verification 必須帶 checkbox

每個 skill 結尾都必須有一份 `- [ ]` 的 exit criteria，強制 agent 在宣告完成前打勾。這對 LLM 特別重要，因為 LLM 預設會傾向「自我感覺良好」地宣告完成。把 verification 做成 checkbox 就把這個自我感覺強制變成可驗證的清單。

## 跨 agent 安裝：模型無關才是真招

addyosmani 為這個 repo 提供了七種不同 agent 的安裝方式，這是它跟絕大多數 Claude-only skill repo 最大的差異。

**Claude Code**（首選）：

```
/plugin marketplace add addyosmani/agent-skills
/plugin install agent-skills@addy-agent-skills
```

**其他 agent**：

- **Cursor** — 複製 SKILL.md 到 `.cursor/rules/`
- **Gemini CLI** — `gemini skills install https://github.com/addyosmani/agent-skills.git --path skills`
- **Windsurf** — 把 skill 內容貼進 Windsurf rules
- **OpenCode** — 透過 `AGENTS.md` 的 skill-driven execution model，使用 `skill` tool 呼叫
- **Copilot** — `agents/` 下的三個 persona 可當 Copilot 用
- **Codex 或其他** — 任何接受 system prompt 的 agent 都可以直接貼 Markdown

這個跨平台策略的成本不低——作者必須保持 SKILL.md 的格式對所有 agent 都中性，不能用任何單一 agent 的特有語法。但它的好處是這個 repo 變成了**事實上的 cross-vendor agent skill 標準**。

**觸發方式是手動 + 自動混合**：

- **手動**：repo 在 `.claude/commands/` 下提供 7 個 slash command（`/spec`、`/plan`、`/build`、`/test`、`/review`、`/code-simplify`、`/ship`）
- **自動**：每個 skill 的 description 欄位包含觸發關鍵字，由 Claude Code 的 description-based dispatch 自動挑選。README L33："designing an API triggers `api-and-interface-design`, building UI triggers `frontend-ui-engineering`"

有趣的是，commands 並不是 skills 的替代品，**它是 skills 的 orchestration layer**。以 `/build` 這個 command 為例，它實際上做的事情就是叫 agent 同時載入 `incremental-implementation` 和 `test-driven-development` 兩個 skill，然後給一份 8 步驟的執行大綱。換句話說，**skills 是知識本體，commands 是入口。**

## 誠實評論

任何 repo 我都會給一份誠實的好壞評。9k stars 不代表沒有問題。

### 做得漂亮的地方

1. **Common Rationalizations 是原創且必要的設計。** 我看過上百個 prompt engineering 範例，沒有任何一個跟這個一樣，**主動假設 agent 會偷懶並事先反駁**。其他 skill repo 通常只寫 happy path，這個 repo 從一開始就把 dark path 也納入設計。這是這個 repo 最大的智識貢獻。

2. **哲學層次對齊 Google SWE book。** Hyrum's Law、Beyonce Rule、Chesterton's Fence、trunk-based development、Shift Left 全部不是停在抽象引用，而是落地成具體的 process step。如果你讀過 *Software Engineering at Google* 這本書，你會發現這個 repo 像是把那本書的精華翻譯成 agent 看得懂的版本。

3. **Progressive disclosure 做得乾淨。** description 決定是否觸發，SKILL.md 是主入口，`references/` 下的 checklist 是按需載入的詳盡 reference。token 負擔可控。

4. **跨平台策略有遠見。** 在 plugin 生態系開始碎裂的時刻，一個刻意中立的 skill collection 就是事實上的標準。

5. **Three-Tier Boundary 應該被整個產業抄走。** Always / Ask / Never 三層制是我看過對 agent 自動化邊界最好的設計。

### 粗糙的地方

1. **幾乎沒有自動化測試。** 整個 repo 的 CLAUDE.md L628 寫著 `npm test: Not applicable (this is a documentation project)`。所有貢獻都是人眼審閱，沒有 skill 品質的客觀驗證機制。對一個有 9k stars 的 repo 來說這有點可惜——你怎麼知道某次更新沒有降低 skill 對 agent 行為的影響力？

2. **沒有效能評量數據。** repo 從頭到尾沒有任何「採用這 19 個 skill 後 agent 輸出品質提升 X%」的實測數據，全部是 "trust me, this works" 的哲學論述。對一個目標是「production-grade」的 repo 來說，這是一個明顯的空白。

3. **`idea-refine` 是個明顯的異物。** 它的 frontmatter 不是第三人稱動詞開頭、結構更像 Anthropic 官方 skill 而非 repo 自家規範，而且是唯一一個有 `scripts/` 子目錄的 skill。看起來是從別處移植進來但沒重寫。

4. **部分 skill 哲學重複但沒互相 link。** 例如 `security-and-hardening` 的 Three-Tier Boundary 跟 `spec-driven-development` 的 Boundaries 章節概念高度重疊但沒互相 reference。違反了它自己 `docs/skill-anatomy.md` L406-415 的規範。

5. **Anti-rationalization 的 context 成本不可忽視。** 每個 skill 都載入 rationalization 表格、flow chart、verification checklist，如果一次啟動多個 skill，token 壓力明顯。作者在 `docs/getting-started.md` L484-487 自己也承認這點，建議「不要一次全部載入」。但這個自我承認其實暴露了一個更深的問題：當你的紀律變成負擔，使用者就會繞過它。

6. **覆蓋面偏 web/frontend。** 19 個 skill 裡幾乎沒有 backend service、data engineering、ML/LLM-ops 的內容。對一個 Google Chrome 工程主管寫的 repo 來說這不意外，但如果你做的是後端或數據工程，你會發現很多概念可以借鑑但沒辦法直接套用。

### 跟 Anthropic 第一方 skills 比較

Anthropic 官方的 skills（例如 `pdf`、`docx`、`skill-creator`、`figma:*`）跟 addyosmani 的 skills 完全是兩種東西。

**Anthropic 的 skills 是工具型的**——每一個解決一個 narrow 的具體任務，frontmatter 通常包含詳細的 `TRIGGER when:` 與 `DO NOT TRIGGER when:` 清單，描述自己會什麼、不會什麼。

**addyosmani 的 skills 是流程型的**——它們不教 agent「怎麼做一件事」，它們教 agent「**做事的態度**」。它不會告訴你怎麼操作 PDF，它會告訴你在做任何事之前先寫 spec、做事的時候每 100 行就 commit、做完之後用 checkbox verify。

這兩種 skill 是互補的，不是競爭關係。Anthropic 的 skills 給你**能力**，addyosmani 的 skills 給你**紀律**。一個團隊兩種都需要。

`code-simplification/SKILL.md` 第 3 行那句直接致敬 Anthropic `code-simplifier` plugin 的話，其實是這個 repo 對自己定位最清楚的宣告：**Anthropic 是原廠工具，addyosmani 是跨廠商的紀律規範。**

## 誰應該採用？

**你應該採用，如果：**

- 你已經試著用 AI agent 寫 production code 一陣子，並且開始感受到「猜需求、跳測試、fix-on-fix」這些痛點
- 你在團隊裡引入 AI agent，而且需要一份能讓所有人對齊的「我們是怎麼跟 agent 一起寫 code 的」標準
- 你用的不只是 Claude Code（也許還有 Cursor、Codex、Gemini），需要一份跨 agent 的工程紀律
- 你想學 senior engineer 是怎麼想事情的——這個 repo 本身就是一份不錯的工程哲學讀物

**你可以暫緩，如果：**

- 你大部分時間在做 prototype 或一次性 script，紀律對你來說是純粹的成本
- 你的工作領域是 backend / data / ML，目前 19 個 skill 對你的覆蓋率不高
- 你的 AI usage 是以「對話 + 修改」為主而非「agent 自主執行」為主——這個 repo 的價值在 agent 自主執行越長的場景越顯著

我自己的結論是：即使你不打算照單全收，這個 repo 的 `docs/skill-anatomy.md` 跟 `using-agent-skills` 兩份文件就值得讀完——它們是目前我看過對 agent skill 設計最清晰的方法論。Common Rationalizations 跟 Three-Tier Boundary 兩個設計模式更是任何寫 agent 工具的人都應該偷學的東西。

把資深工程師的本能變成 AI agent 的紀律——這件事看起來很簡單，但實際上沒幾個人做得到 addyosmani 這個程度的具體與徹底。光是這一點，這個 repo 就值得一個 star。
