---
title: "Compound Engineering Plugin 深度拆解：把 AI 寫程式變成知識飛輪"
date: 2026-04-08
categories: [AI]
tags: [claude-code, plugin, compound-engineering, ai-agents, every, code-review, multi-agent]
---

> 原始 repo：[EveryInc/compound-engineering-plugin](https://github.com/EveryInc/compound-engineering-plugin) by **Kieran Klaassen** (@every.to)

---

## 為什麼這個 plugin 值得認真研究

13.6k stars。50 多個 agent。50 多個 skill。版本號已經跑到 2.63.1。建立六個月、平均每幾天 release 一次。這是 2026 年 Claude Code 生態系裡最被認真對待的 plugin 之一，作者 Kieran Klaassen 是 Every（以 every.to 為主要出版品的紐約軟體媒體公司）的工程主管。整個 plugin 承載了 Every 內部對「AI 輔助寫 code 應該長什麼樣」的整套主張，他們把這套主張命名為 **Compound Engineering**。

Compound Engineering 這個詞本身就值得拆。它的核心宣告寫在 root README 的開頭幾行：**"Each unit of engineering work should make subsequent units easier — not harder."** 翻成中文：**每一單位的工程工作都應該讓後續工作更容易，而不是更困難。**

這句話聽起來像是一句廢話金句，但它背後的具體主張其實很尖銳：他們認為一般的 AI 輔助開發——Cursor tab、Copilot completion、`claude` 一句一句餵——優化的是**執行的吞吐量**，而 Compound Engineering 優化的是**可重複使用的工程資產的累積**。每次 review、每次 plan、每次解決一個棘手 bug，都應該變成一份有結構、有 frontmatter、可被未來 agent 搜尋的 Markdown 檔。下次 review 的時候，agent 會自動去翻過去解過的問題；下次 plan 的時候，agent 會自動參考過去的 spec。**這就是「compound」**——它不只是隱喻，它是 repo 裡真實存在的 `docs/solutions/` 目錄與 `learnings-researcher` agent 的閉環。

而最有說服力的證據是：**這個 plugin 是用它自己寫出來的。** repo 自己的 `docs/brainstorms/` 有 25+ 份需求文件，`docs/plans/` 有 40+ 份計畫文件，`docs/solutions/` 有 15+ 份「過去解過的問題」。每一個 plugin 自己的功能，都有完整的 brainstorm → plan → solution 軌跡可以追溯。沒有什麼比這個更能證明這套方法論是真的有人在用的。

這篇文章我會帶你拆：
1. 「Compound Engineering」這個詞到底主張什麼
2. plugin 的架構與所有元件
3. 6-8 個我認為最值得學習的 skill 跟 agent
4. 整個 workflow 怎麼閉環
5. 這個 plugin 的招牌技術
6. 安裝與使用
7. 哪些做得漂亮、哪些其實是 hype

## Compound Engineering 是什麼？

先講核心主張，然後拆裡面的具體機制。

### 一句話版本

「每單位工程工作都應該讓後續工程工作更容易。」

### 三段論版本

1. **80% in planning and review, 20% in execution.** 大多數 AI 寫程式的工具都把 80% 的注意力放在執行（generate code），這個 plugin 認為應該倒過來：**把 80% 的精力放在 plan 跟 review 上，執行只是 20%。**
2. **Plan thoroughly, review thoroughly, codify, keep quality high.** 計畫要徹底、review 要徹底、把學到的東西寫下來變成可重用的文件、讓品質保持高到未來的修改也容易。
3. **每個 unit 都要產生 reusable artifact。** 不只是 code commit，還要產生 brainstorm doc、plan doc、solution doc。這些 doc 不是給人看的紀錄，是給**未來的 agent 看的搜尋目標**。

### 為什麼這跟「一般的 spec-driven development」不一樣？

Spec-driven development 的版本通常是：寫 spec → 寫 code → 完成。Compound Engineering 多了關鍵的**第三步**：**把這次解決的問題寫進 `docs/solutions/`，讓下一次 review 時自動找到。**

這個 `docs/solutions/` 的角色特別重要。`ce:review` 這個 skill 永遠會自動 dispatch 一個叫 `learnings-researcher` 的 agent，這個 agent 的工作就是去 `docs/solutions/` 翻有沒有跟當前 PR touch 到的模組相關的歷史教訓。**這是 compound 機制真正運作的地方**——你的知識庫不只是檔案夾，它是一個 review 流程會自動 query 的記憶體。

理論上講，當你用這個 plugin 越久、`docs/solutions/` 累積越多，每次 review 的品質應該越高。這是「compound」的字面意思。

但這個機制有一個前提：**有人要願意執行 `ce:compound`，有人要願意 curate `docs/solutions/`**。沒有這個紀律的話，它就退化成一個比平均水準好的 plugin。我會在誠實評論那一節再回來討論這個問題。

## 架構：marketplace、plugin、目錄佈局

這個 repo 是一個 **multi-plugin marketplace**——top-level 的 `.claude-plugin/marketplace.json` 列出兩個 plugin：`compound-engineering`（主角）跟 `coding-tutor`（一個小型的 Nityesh Agarwal 教學 plugin）。本文只談前者。

主 plugin 的入口是 `plugins/compound-engineering/.claude-plugin/plugin.json`：

```
name: compound-engineering
version: 2.63.1
author: Kieran Klaassen <kieran@every.to>
license: MIT
```

注意那個 `2.63.1`——一個六個月大的 plugin 跑到 2.63 minor version，這是一個非常頻繁的 release cadence。release-please 自動化發佈、15+ 個 CI workflow，這是一個被當成軟體 product 在維運的 plugin。

### 目錄結構

來自 `plugins/compound-engineering/AGENTS.md` L617-628：

```
agents/
├── review/           # 27 個 code review agents
├── document-review/  # 7 個 plan/requirements review agents
├── research/         # 7 個 research agents
├── design/           # 3 個 design agents
├── docs/             # 1 個 docs agent
└── workflow/         # 4 個 workflow agents

skills/
├── ce-*/             # 核心 workflow skills
└── */                # 其他所有 skill
```

值得停下來說明一個歷史細節（`AGENTS.md` L630）：**v2.39.0 的時候，plugin 把所有 commands 都遷移成了 skills。** 過去叫 `/command-name` 的 slash command 現在都活在 `skills/command-name/SKILL.md` 底下。`skills/` 目錄裡那些以 `ce:` 開頭的 skill 名稱（例如 `ce:plan`、`ce:review`），都是同時作為 skill 跟 slash command 的——Claude Code 把任何帶冒號的 skill name 自動視為 slash command。

`ce:` 這個前綴的存在原因很實際（`AGENTS.md` L644-652）：Claude Code 已經有內建的 `/plan` 跟 `/review`，所以這個 plugin 必須加前綴避免衝突。

### 跨平台支援

這個 plugin 不只能在 Claude Code 上跑。`plugins/compound-engineering/` 的內容是 source of truth，但 repo 同時 ship 了一個 **Bun/TypeScript CLI**（`@every-env/compound-plugin`）負責把 Claude Code 的 SKILL.md 格式轉換成 11 種其他 agent 能讀的格式：Codex、OpenCode、Droid、Pi、Gemini、Copilot、Kiro、Windsurf、OpenClaw、Qwen，外加 Cursor 透過原生 plugin 系統。

repo 的 645k 行 TypeScript 主要就是這些 converter（`src/converters/claude-to-*.ts`）跟對應的測試。這是一個非常認真的跨平台投資。

對 Compound Engineering 的核心 thesis 來說，這也是一個 reinforcement——**file-based hand-off contract** 之所以重要，正是因為它能跨工具、跨機器、跨人類隊友存在。

## 元件總覽：50+ agent + 50+ skill

我嘗試把所有元件做一個盤點，這樣你才會對這個 plugin 的規模有感覺。

### Agents（49 個檔案）

**Review（27 個）** — 程式碼 review 用的角色：
`adversarial-reviewer`、`agent-native-reviewer`、`api-contract-reviewer`、`architecture-strategist`、`cli-agent-readiness-reviewer`、`cli-readiness-reviewer`、`code-simplicity-reviewer`、`correctness-reviewer`、`data-integrity-guardian`、`data-migration-expert`、`data-migrations-reviewer`、`deployment-verification-agent`、`dhh-rails-reviewer`、`julik-frontend-races-reviewer`、`kieran-python-reviewer`、`kieran-rails-reviewer`、`kieran-typescript-reviewer`、`maintainability-reviewer`、`pattern-recognition-specialist`、`performance-oracle`、`performance-reviewer`、`previous-comments-reviewer`、`project-standards-reviewer`、`reliability-reviewer`、`schema-drift-detector`、`security-reviewer`、`security-sentinel`、`testing-reviewer`。

**Document review（7 個）** — review 計畫跟需求文件用的（不是 review code）：
`adversarial-document-reviewer`、`coherence-reviewer`、`design-lens-reviewer`、`feasibility-reviewer`、`product-lens-reviewer`、`scope-guardian-reviewer`、`security-lens-reviewer`。

**Research（7 個）**：
`best-practices-researcher`、`framework-docs-researcher`、`git-history-analyzer`、`issue-intelligence-analyst`、`learnings-researcher`、`repo-research-analyst`、`slack-researcher`。

**Design（3 個）**：`design-implementation-reviewer`、`design-iterator`、`figma-design-sync`。

**Workflow（4 個）**：`bug-reproduction-validator`、`lint`、`pr-comment-resolver`、`spec-flow-analyzer`。

**Docs（1 個）**：`ankane-readme-writer`。

### Skills（~50 個）

**核心 workflow（CE 系列）**：
`ce-brainstorm`、`ce-ideate`、`ce-plan`、`ce-work`、`ce-work-beta`、`ce-review`、`ce-compound`、`ce-compound-refresh`、`ce-update`。

**Git 相關**：`git-commit`、`git-commit-push-pr`、`git-worktree`、`git-clean-gone-branches`。

**Workflow 工具**：`changelog`、`feature-video`、`reproduce-bug`、`report-bug-ce`、`resolve-pr-feedback`、`test-browser`、`test-xcode`、`onboarding`、`todo-create`、`todo-resolve`、`todo-triage`、`setup`。

**框架相關**：`agent-native-architecture`、`agent-native-audit`、`andrew-kane-gem-writer`、`dhh-rails-style`、`dspy-ruby`、`frontend-design`。

**品質**：`claude-permissions-optimizer`、`document-review`。

**內容**：`every-style-editor`、`proof`。

**自動化**：`agent-browser`、`gemini-imagegen`、`orchestrating-swarms`、`rclone`。

**Beta / 實驗**：`lfg`、`slfg`。

光看這份清單你就知道——**這個 plugin 不是「給你一個工具」，它是給你「一整套工作流程」**。光是程式碼 review 就有 27 個不同角度的 reviewer。你大概不會每次都全部用上，但這代表這個 plugin 對「review 該看什麼」的思考非常深入。

## 6 個值得深挖的元件

### 1. `ce:plan` — 不寫 code 的計畫者

`skills/ce-plan/SKILL.md` 裡有一個我覺得整個 plugin 最核心的句子：

> "ce:brainstorm defines WHAT to build. ce:plan defines HOW to build it. ce:work executes."

`ce:plan` 的任務分得非常乾淨——**它不實作 code、不跑 test、不從執行結果中學習。** 它只負責產出一份計畫文件。

它的核心原則（L34-40）有三條值得注意：
- **"Decisions, not code"** — 計畫的內容是決策，不是程式碼
- **"Right-size the artifact"** — 計畫的詳細程度要剛好
- **"Separate planning from execution discovery"** — 計畫階段不做「邊做邊發現問題」這件事

它的 "Plan Quality Bar" 強制計畫文件要包含哪些東西：每個 feature-bearing unit 都要有列舉的 test scenarios、所有路徑都要 repo-relative。這個「禁止 absolute path」的規範背後是一個重要設計：**plan 必須能跨機器、跨人類、跨 agent 重用。**

skill 還把它的詳細內容拆到 `references/deepening-workflow.md`、`references/plan-handoff.md`、`references/universal-planning.md`、`references/visual-communication.md`——這是一個有意的 token-budget 設計，主 SKILL.md 保持精簡，需要的細節才額外載入（這個 pattern 在 `AGENTS.md` L680-682 有完整解釋）。

### 2. `ce:review` + 17-persona reviewer system — 整個 plugin 最架構漂亮的部分

如果你只想看一個元件來理解這個 plugin，看 `ce:review`。

它的 description 寫得很乾淨："Structured code review using tiered persona agents, confidence-gated findings, and a merge/dedup pipeline."

#### 四種 mode

`ce:review` 支援四種執行模式（L100-106）：
- **interactive**（預設）— 跟使用者來回討論
- **mode:autofix** — 不問人，只套用 `safe_auto` 的 finding，並做有界的重複 review 回合
- **mode:report-only** — 嚴格 read-only，可以平行跑多個
- **mode:headless** — 給 skill-to-skill 呼叫用的，回傳結構化 envelope

四種 mode 共用同一個 skill 主體，只差在執行時傳入的參數。這是一種非常乾淨的設計——一個 skill 同時服務「人類想 review」跟「另一個 skill 想自動 review」兩種場景。

#### 17 個 persona 的分層選擇

`references/persona-catalog.md` 是這個 plugin 對「程式碼 review 該看什麼」的完整目錄：

**Always-on layer（4 個 persona + 2 個 CE agent）**：每次 review 都會跑
- `correctness`、`testing`、`maintainability`、`project-standards`
- 加上 `agent-native-reviewer`
- 加上 **`learnings-researcher`** ← 這個最重要

`learnings-researcher` 的工作是去 `docs/solutions/` 翻有沒有跟當前 review 相關的歷史教訓。**這就是 compound 機制在 review 流程裡的具體實現。**

**Cross-cutting conditional（8 個 persona）**：根據判斷選擇性啟動
- `security`、`performance`、`api-contract`、`data-migrations`、`reliability`、`adversarial`、`cli-readiness`、`previous-comments`
- L558 寫得很清楚：選擇是 **"agent judgment, not keyword matching"** ——讓 agent 自己決定要不要啟動，不是 if-else

**Stack-specific conditional（5 個 persona）**：跟著技術棧走
- `dhh-rails`、`kieran-rails`、`kieran-python`、`kieran-typescript`、`julik-frontend-races`
- 注意命名——這些是以**真實工程師**的名字命名的，等於是給你某個人的審視角度

**CE migration-specific**：`schema-drift-detector`、`deployment-verification-agent`。

#### Findings schema 與 merge pipeline

每個 persona 都會回傳結構化 JSON，符合 `references/findings-schema.json`。Plugin 有一個 merge / dedup pipeline 把它們合併成一份報告。每個 finding 帶 resolution category：`safe_auto`、`gated_auto`、`manual`、`human`、`release`——這個 category 決定了 `mode:autofix` 能不能自動套用它。

這是我看過開源世界裡最成熟的多 agent code review 架構。它把「誰來看」跟「怎麼合併結果」乾淨地分開，而且支援自動化跟互動兩種模式。光是 `references/persona-catalog.md` 這份文件就值得任何想做 multi-agent review 的人讀一遍。

### 3. `adversarial-reviewer` — 把 reviewer 變成攻擊者

`agents/review/adversarial-reviewer.md` L481 的角色定義：

> "You are a chaos engineer who reads code by trying to break it."

這是 conditional reviewer——只有在 diff ≥ 50 行、或 touch 到 auth / payments / migrations / external API 時才會被選上。它有四種 hunting technique：
- **Assumption violation** — 找出 code 裡的隱含假設然後構造打破它的輸入
- **Composition failures** — contract mismatch、shared state、ordering across boundaries、error contract divergence
- **Cascade construction** — 多步驟失敗鏈，例如 retry storm、state corruption propagation
- **Abuse cases** — 惡意使用者怎麼濫用這個 API

它跟其他 reviewer 的關鍵差異在 prompt 裡寫得很白：「Where other reviewers check whether code meets quality criteria, you construct specific scenarios that make it fail... you don't evaluate -- you attack.」

**這是個非常聰明的 prompt engineering 技巧。** 一般 reviewer 在做的是 classification（這段 code 符不符合品質標準），但 LLM 在 classification 上沒比 generation 強多少。而 adversarial-reviewer 給 LLM 的是 **generative 任務**——「**發明**一個會讓這段 code 壞掉的場景」——這正中 LLM 的長處。Generation 的假陽性容易過濾，classification 的假陰性很難救。

### 4. `ce:compound` — 知識飛輪

`skills/ce-compound/SKILL.md` L199 把這個 skill 的目的講得很清楚：

> "Captures problem solutions while context is fresh, creating structured documentation in docs/solutions/ with YAML frontmatter for searchability."

哲學宣告（L201）：**"Each documented solution compounds your team's knowledge."**

skill 有兩種 mode：
- **Full mode** — 多階段 subagent orchestration，包含重複偵測、跨檔案 reference
- **Lightweight mode** — 單次 pass，少 token

但這個 skill 有一個重要的架構決定值得強調：**phase-1 的 subagent 只回傳 text，不回傳檔案路徑，只有 orchestrator 自己會寫檔案。** 為什麼？因為過去的經驗告訴他們，如果讓 subagent 自己寫檔案，subagent 會建出一堆使用者沒想到的檔案。這個經驗本身被寫成了一份 solution doc：`docs/solutions/skill-design/pass-paths-not-content-to-subagents-2026-03-26.md`。

這是一個非常 meta 的時刻——**plugin 的設計教訓本身就是用這個 plugin 的方法論寫下來的。** 如果你想看一個 plugin 「dogfood 自己」是什麼樣子，看這份文件。

`ce:compound` 還會做一個 **"Discoverability Check"**——可能會去編輯你 repo 的 `AGENTS.md` 或 `CLAUDE.md`，加上指向 `docs/solutions/` 的 reference，這樣未來的 agent 才會找得到這些 solution。這是把「knowledge base」轉換成「always discoverable knowledge base」的具體手法。

### 5. `ce:work` — 三檔分流的執行者

`skills/ce-work/SKILL.md` 的設計很實用。它的 Phase 0 會把輸入分成三檔：

- **Trivial**（1-2 個檔案，typo / rename）→ 直接做，不建任務清單
- **Small/Medium**（< 10 個檔案）→ 建一份任務清單再做
- **Large**（10+ 個檔案，或 touch 到 auth / payments / migrations）→ **告訴使用者這應該先用 `/ce:brainstorm` 或 `/ce:plan`**

第三檔的處理特別有意思——**它會主動拒絕**。如果你給它一個太大的任務，它不會硬著頭皮做，而是建議你先去 plan。

它讀 plan 文件的方式也很特別：它把 plan 當成 **"a decision artifact, not an execution script"**——計畫是決策的紀錄，不是執行的腳本。它會找特定的章節：`Implementation Units`、`Deferred to Implementation`、`Scope Boundaries`。每個 implementation unit 還帶一個 `Execution note` 欄位，標示這個 unit 是 test-first 還是 characterization-first。

這是 plan → work hand-off contract 的具體實現——兩個 skill 之間透過**檔案結構**對話，不是透過 in-memory state 或 prompt 傳遞。

### 6. `lfg` — 自動化 pipeline 的 gated state machine

`skills/lfg/SKILL.md` 是這個 plugin 最大膽的元件。LFG（"let's f-ing go"）是一個全自動 pipeline，標記為 `disable-model-invocation: true`，意思是 agent 不會自動觸發它，只有使用者明確呼叫才會跑。

它的 skill body 整個就是一個有編號的、有 gate 的狀態機：

1. 可選的 `ralph-loop` slash command 暖身
2. `/ce:plan $ARGUMENTS` — **GATE: STOP unless** `docs/plans/` 有新檔案
3. `/ce:work` — **GATE: STOP unless** 有檔案被建立或修改
4. `/ce:review mode:autofix plan:<plan-path>` — review，傳入 plan 路徑做需求驗證
5. `/compound-engineering:todo-resolve`
6. `/compound-engineering:test-browser`
7. `/compound-engineering:feature-video`
8. 輸出 `<promise>DONE</promise>`

它的開頭預警寫得非常嚴：

> "CRITICAL: You MUST execute every step below IN ORDER... The plan phase (step 2) MUST be completed and verified BEFORE any work begins."

**那些 `GATE: STOP` 是這個 skill 最聰明的設計。** 一般 prompt 工程很難讓 LLM 嚴格按順序執行長串步驟，因為 LLM 會自然 collapse。但這個 skill 把每個 step 變成一個有明確 post-condition 的 gate——你必須驗證 `docs/plans/` 真的有新檔案，才能進下一步。這把「跟著 prompt 走」變成了「跟著可驗證的事實走」，可靠度大幅提升。

### 7. `orchestrating-swarms` — 多 agent 原語

`skills/orchestrating-swarms/SKILL.md` 是 Claude Code 多 agent 系統的權威 reference。它定義了所有 swarm 的概念：
- **Team** — 命名群組，config 在 `~/.claude/teams/{name}/config.json`
- **Teammate** — 帶 inbox 的命名 agent
- **Leader** — 領隊
- **Task** — 帶 dependency 的工作項目
- **Inbox** — JSON file
- **Backend** — auto-detected: `in-process`、`tmux`、或 `iterm2`

包含 mermaid 圖描述 team topology 跟 7 步驟的生命週期。這份文件完整度比 Anthropic 自己出的 swarm 文件還高。它也是 `disable-model-invocation: true`——避免汙染普通 session 的 context。

### 8. `ce:ideate` — 對 LLM 的去偏見技巧

`skills/ce-ideate/SKILL.md` 是哲學上最特別的 skill，因為它的工作是**產生想法**而不是 refine 一個既有想法。

三段式分工（L324-326）：
- `ce:ideate` 回答：「**有哪些值得探索的強想法**？」
- `ce:brainstorm` 回答：「被選中的那個想法到底是什麼？」
- `ce:plan` 回答：「該怎麼建？」

但 `ce:ideate` 真正聰明的是它的核心原則 #2（L352）：

> "Generate many → critique all → explain survivors only — the quality mechanism is explicit rejection with reasons, not optimistic ranking."

**這直接打中了 LLM 在「列出想法」時的失敗模式**：你叫 LLM「給我五個方向」，它通常會給你五個平庸的、相互重疊的、安全的選項。為什麼？因為 ranking 任務會讓 LLM 自動往「平均」收斂。

`ce:ideate` 的解法是：先用 generate-many 模式產生多到不合理的選項（包含明顯爛的），然後用 explicit rejection 階段把大部分淘汰掉，最後只解釋活下來的幾個。**adversarial filtering 取代了 optimistic ranking**——這是去偏見的關鍵。

這個 pattern 也出現在 `adversarial-reviewer` 跟 `adversarial-document-reviewer` 上。你可以看出整個 plugin 的作者群對 LLM 的失敗模式有非常具體的觀察。

## 端到端 workflow

把上面的元件拼起來，你會得到這個 pipeline：

```
ce:ideate    ─ 可選，發散式生點子
    ↓
ce:brainstorm ─ 互動式 Q&A → docs/brainstorms/ 下的需求文件
    ↓
ce:plan ─ docs/plans/ 下的計畫文件，被 document-review persona 審過
    ↓
ce:work ─ 用 worktree + 任務清單 + test-first 姿態執行
    ↓
ce:review ─ 17 persona 平行 review → 結構化 JSON → 合併報告
    ↓
ce:compound ─ 把學到的東西寫進 docs/solutions/，帶 YAML frontmatter
    ↓
（下次 ce:review 自動透過 learnings-researcher 查詢 docs/solutions/）
```

整條 pipeline 的 hand-off 是 **file-based**——每個階段產出一份 durable Markdown artifact，路徑是 repo-relative。Subagent 之間傳的是**檔案路徑，不是內容**——這個原則被明文寫在 `docs/solutions/skill-design/pass-paths-not-content-to-subagents-2026-03-26.md`。`lfg` skill 把整條 pipeline 包進 gated transition 裡，`slfg` 再加上 swarm 平行執行。

這就是「compound」的具體機制：每一階段的 output 變成一個檔案，下一階段讀這個檔案，然後產生新的檔案。**檔案是溝通協定**。這個設計的好處是：

1. **可跨 session** — 你今天 plan，明天 work，後天 review，沒問題
2. **可跨 machine** — 你 plan 完 push 到 git，隊友 pull 下來繼續 work
3. **可跨工具** — 同一份 plan 文件可以給 Claude Code、Codex、Cursor 任何一個讀
4. **可跨 agent 與人類** — agent 寫的 plan 可以給人類 review，反之亦然

這個「file 是溝通協定」的理念，是 Compound Engineering 整個方法論最深的一層。

## 招牌技術

把上面的元件跟 workflow 拆完，可以提煉出幾個橫貫整個 plugin 的技術。

### 1. Skill 裡的 gated state machine

`lfg` 的 `GATE: STOP` 模式不只用在 lfg 一個地方——它是這個 plugin 對「長串 workflow 怎麼可靠執行」的標準答案。把每個 step 變成一個有可驗證 post-condition 的 gate，把「跟著 prompt 走」變成「跟著事實走」。

### 2. 分層 persona + JSON envelope 的 review 系統

`ce:review` 的 always-on / conditional / stack-specific 三層結構，加上每個 persona 都回傳符合 `findings-schema.json` 的結構化 envelope，然後有一個 merge/dedup pipeline 合併。這乾淨地把「review 看什麼」跟「review 結果怎麼合併」分開。

### 3. 一個 skill 多種 mode 的 dispatch

`ce:review` 同時支援 interactive / autofix / report-only / headless 四種 mode，靠 argument token 切換。headless 模式是專門設計給 skill-to-skill 呼叫用的，這就是 `lfg` 怎麼把 review 整合進自動化 pipeline 而不需要使用者介入的關鍵。

### 4. Adversarial filtering 取代 optimistic ranking

`ce:ideate`、`adversarial-reviewer`、`adversarial-document-reviewer` 都用同一個技巧：與其叫 LLM「給最好的」，不如叫它「全部生出來再淘汰」。這是 LLM 去偏見的關鍵手法。

### 5. Reference file extraction：context budget engineering

`AGENTS.md` L680-682 明文寫了一條規則：skill 主體在觸發時載入，會帶在所有後續訊息裡。所以 conditional 的、後段才用到的內容，要抽到 `references/` 用 backtick 路徑載入。

`@` syntax 跟 backtick 路徑的差別也有規範：小於 ~150 行的、總是要的 schema 用 `@`，其他用 backtick。這條規範還有對應的 test（`tests/frontmatter.test.ts`）。

這是非常認真的 token-budget 工程。寫過大 plugin 的人都知道 context 會吃光，但很少有 plugin 把這個問題寫成 explicit rule。

### 6. Reviewer 帶 confidence calibration

多個 reviewer agent 都帶 confidence signal，這樣 `mode:autofix` 才能只套用 `safe_auto` 的 finding。這是把 LLM 的 uncertainty 變成 actionable signal 的具體手法。

### 7. Knowledge flywheel 的閉環

`ce:compound` 寫，`learnings-researcher` 讀。閉環。

### 8. Cross-platform write-once

11 種 target format，寫一次 source 編譯到所有平台。645k 行 TypeScript 主要在做這件事。**這是一個 plugin level 的 universal binary**。

### 9. Checkpointed autofix rounds

`ce:review mode:autofix` 跑有界的多輪重 review，每輪的 artifact 寫進 `.context/compound-engineering/ce-review/<run-id>/`。這是 iterative verification 的初級形式。

### 10. Self-hosting as validation

最後也最重要：**這個 plugin 自己用自己**。`docs/brainstorms/` 跟 `docs/plans/` 跟 `docs/solutions/` 加起來 ~80 份文件。每個 plugin feature 都有完整的軌跡。沒有什麼比這個更能驗證方法論的可行性了。

## 安裝與使用

**前置需求**：Claude Code（或任一支援的 target）。某些 skill 需要額外的工具，例如 browser-automation skill 需要：

```bash
npm install -g agent-browser
agent-browser install
```

**Claude Code（首選）**：

```
/plugin marketplace add EveryInc/compound-engineering-plugin
/plugin install compound-engineering
```

**其他工具（透過 converter CLI）**：

```bash
bunx @every-env/compound-plugin install compound-engineering --to codex
bunx @every-env/compound-plugin install compound-engineering --to opencode
bunx @every-env/compound-plugin install compound-engineering --to all
```

**用法** — 安裝完之後就是 slash command：

```
/ce:brainstorm "幫我加 rate limiting 到 public API"
/ce:plan
/ce:work
/ce:review
/ce:compound
```

或者全自動：

```
/lfg "幫我加 rate limiting 到 public API"
```

**本地 dev**——如果你想對 plugin 做修改測試，可以用：

```bash
alias cce='claude --plugin-dir ~/code/compound-engineering-plugin/plugins/compound-engineering'
```

這樣可以測試一個 checkout 而不污染 production install。

## 誠實評論

13.6k stars 不代表沒有問題。我來分三部分講。

### 做得漂亮的地方

1. **File-based hand-off contract 是正確的原語。** 把 requirement / plan / solution 都當成帶 YAML frontmatter 的 durable Markdown，這是跨 session、跨工具、跨機器、跨人類隊友的最大公約數。也是 cross-tool converter 之所以可能的根本原因。

2. **17-persona review 系統的架構很漂亮。** 結構化 JSON envelope + confidence-gated autofix + 安全分級（`safe_auto` / `gated_auto` / `manual` / `human` / `release`）——這是我看過開源 multi-agent code review 最成熟的設計。光是 `references/persona-catalog.md` 這份文件就值得任何想做 multi-agent review 的人讀。

3. **Self-hosting 證據強。** 80 份帶日期的 brainstorm + plan + solution 在 repo 裡——這是 Every 內部團隊真的在用這套方法的硬證據。沒什麼比一個不用自己方法論的方法論 repo 更尷尬了，這個 repo 沒這個問題。

4. **Context budget 紀律。** `AGENTS.md` 裡 `@` vs backtick 路徑的區分、後段內容必須抽到 reference 的規則——這是很 senior 的 prompt engineering 紀律，被一致地套用在 50+ skill 上。

5. **跨工具 converter。** 大部分 Claude plugin 是 Claude only。這個 plugin 投入了 645k 行 TypeScript 把它轉到 11 種其他工具，每個 target 都有 test。這是真投資。

6. **`ce:ideate` 的「generate many, reject most」。** 對 LLM idea generation 用了正確的去偏見手法。

### 粗糙的地方

1. **規模本身就是負擔。** 50+ agent、50+ skill、~200 個 Markdown 檔案。光是要知道有什麼可以用就需要學習曲線。README 的 component 表是主要導航工具，但對新手不夠友善。

2. **意見很重，而且是某些特定人的意見。** Stack-specific reviewer 直接以工程師的名字命名（`dhh-rails-reviewer`、`kieran-rails-reviewer`、`julik-frontend-races-reviewer`）。如果你跟這些人的觀點吻合很棒；如果不吻合，`ce:review` 會給你一份「DHH 會怎麼想你的 Rails code」的審視，可能不太實用。

3. **Rails / Ruby 偏重。** 5 個 stack-specific reviewer 裡 3 個是 Rails；`agents/workflow/lint.md` 是 Ruby/ERB 專用。這個 plugin 顯然是在一家 Rails shop 誕生的。如果你做 Go / Java / Rust / 純 frontend，你會發現 stack-specific 那層的價值打折扣很多。

4. **版本變動快。** 6 個月跑到 2.63.1，`docs/brainstorms/` 顯示幾乎每週都有新需求。今天裝的版本，下個月行為可能改變。內建的 `ce-update` skill 跟 `AGENTS.md` L635-641 的「stale cache」警告，都是直接的證據——這是個真實的維運問題。

5. **Gate 是用 prompt 寫的，不是用 code 強制的。** `lfg` pipeline 的 `GATE: STOP` 全部靠 LLM 自己遵守。如果 harness 有任何 autonomy，或者某個 step 被微妙地跳過，沒有任何結構性的東西會擋住——gate 是英文寫的，不是 hook 或 exit code 強制的。

6. **Beta 散在各處。** `ce-work-beta`、`lfg`、`slfg`、`-beta` suffix 約定、`beta-skills-framework.md` 文件——這些都暗示 stable / beta 邊界還沒完全定下來。

7. **`disable-model-invocation: true` 是雙面刃。** 對某些 orchestration skill 有這個標記是安全的（避免汙染普通 session），但代價是 model 不會自動叫 `orchestrating-swarms` 即使它有用——使用者必須先知道這個 skill 存在。

### 哪些是 hype

我得說一些反話，因為這個 plugin 在社群裡被吹得很厲害，但有些東西其實沒那麼神。

1. **「Compound」這個 framing 部分是行銷。** 拆掉品牌之後，這個 plugin 實際上 ship 的是：
   - opinionated 的 brainstorm / plan 文件模板
   - 一個架構漂亮的 multi-persona code reviewer
   - 一個 `docs/solutions/` 知識庫慣例
   - 一個 cross-tool converter
   
   這四個東西每個都不錯，但 **「each unit makes the next easier」這個 compound 機制只有在使用者真的紀律執行 `ce:compound` 並且 curate `docs/solutions/` 的時候才會發生**。沒有這個紀律的話，它就退化成「比平均水準好的 plugin」。Compound 是承諾，不是預設行為。

2. **「80% planning and review, 20% execution」是 slogan，不是 measurement。** 這是一個有感染力的口號，但它沒有任何客觀數據支持。它是希望的方向，不是當前的事實。

3. **「17 persona」聽起來很猛，實際上每次 review 大概只跑 4-6 個。** 17 是選擇空間，不是 swarm size。always-on 的 4 個加 1-3 個 conditional，這才是實際數字。

### 誰應該採用

**強烈推薦給：**
- 已經被 spec-driven / plan-first AI 開發說服的團隊，想要一份 opinionated 的腳手架而不是自己 roll 一份
- Rails / Ruby shop——你會發現 stack-specific reviewer 的命中率特別高
- 有真正 CI/CD 紀律的團隊
- 任何想在 coding agent 上面建立 institutional knowledge layer 的團隊

**不太適合：**
- Solo vibe coding 或 prototype 階段——這些 ceremony 是純成本
- 技術棧跟 stack-specific reviewer 完全沒重疊的團隊
- 對「agent 應該按你的意思走，不是按某個方法論走」有強烈偏好的開發者

**如果你只想從這個 repo 偷一個東西，偷 `ce:review`。** 17-persona 架構就算你不裝這個 plugin 也值得研究——它是目前我看過 multi-agent code review 設計最成熟的範例。

## 結語

Compound Engineering Plugin 是 2026 年 Claude Code 生態系裡最有野心的 plugin。它不只是工具集合，是一份完整的軟體工程方法論。它的長處跟短處都很真實——架構漂亮、自我驗證、跨平台投資巨大；但同時也規模龐大、意見強烈、Rails 偏重、版本變動快。

如果你在尋找一個現成的 agent-driven development workflow 並且願意接受 Every 團隊的觀點，這是目前最完整的選擇。如果你只是想學東西，光是 `ce:review` 的 17-persona 架構跟 `lfg` 的 gated state machine 就值得花一個下午把 source 讀過一遍。

最後再回到開頭那句話：「Each unit of engineering work should make subsequent units easier — not harder.」這句話的真正意義不是這個 plugin 自動做到了 compound，而是**它把 compound 變成一個你可以選擇執行的紀律**。紀律永遠是選擇的問題，工具只能讓選擇變得容易。這個 plugin 做到了讓選擇變得容易，剩下的看你。
