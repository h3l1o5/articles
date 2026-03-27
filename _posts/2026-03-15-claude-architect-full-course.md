---
title: "我想成為 Claude Architect（完整課程）"
date: 2026-03-15
categories: [AI]
tags: [claude, agent-sdk, mcp, prompt-engineering, certification]
image:
  path: /assets/img/posts/2026-03-15-claude-architect-full-course/claude-architect-banner.jpg
  alt: "Claude Certified Architect Foundations 證書與角色插畫"
---

> 原文：[I want to become a Claude architect (full course).](https://x.com/hooeem/status/2033198345045336559) by **hoeem** (@hooeem)
> 發佈日期：2026 年 3 月 15 日

---

要成為 Claude Architect 並開發 production-grade 應用，你需要理解 Claude Code、Claude Agent SDK、Claude API 和 Model Context Protocols。這篇文章會幫你學會一切，而且它是基於以下這個考試：

![Become a Claude Certified Architect 考試報名頁面](/assets/img/posts/2026-03-15-claude-architect-full-course/claude-architect-exam.jpg)

不過，正如你清楚看到的，要拿到這張「認證」你需要是 Claude partner，否則你沒辦法參加考試。

**但這重要嗎？**

如果你有能力學會成為「Claude Certified Architect」所需的一切，那你就有能力打造 production-grade 的應用。

你不需要證書來建構 production-grade 應用。

你只需要知識。

所以我把整份考試指南拆解開來，抽出真正重要的東西，讓你能夠成為一名 Claude architect。

## 你將面對什麼

這場考試你沒辦法參加——除非你是 Claude partner——但這不重要，因為學習考試所需的知識本身會教你以下技能：Claude Code、Claude Agent SDK、Claude API、和 Model Context Protocol (MCP)。

**而這些全部都是可以變現的技能。**

考試代表你需要學習以下內容：

- **Customer Support Resolution Agent**（Agent SDK + MCP + escalation）
- **Code Generation with Claude Code**（CLAUDE.md + plan mode + slash commands）
- **Multi-Agent Research System**（coordinator-subagent orchestration）
- **Developer Productivity Tools**（built-in tools + MCP servers）
- **Claude Code for CI/CD**（non-interactive pipelines + structured output）
- **Structured Data Extraction**（JSON schemas + tool_use + validation loops）

## DOMAIN 1：AGENTIC ARCHITECTURE & ORCHESTRATION（27%）

考試測試三個你需要一眼就識別出來的 anti-pattern：用 parsing 自然語言來判斷 loop 是否結束、用任意的迭代上限作為主要停止機制、以及檢查 assistant 的文字內容作為完成指標。全部都是錯的。

**最大的錯誤：**人們假設 subagent 會跟 coordinator 共享記憶。事實上不會。Subagent 使用獨立的 context 運作。每一條資訊都必須在 prompt 中明確傳遞。

**能幫你拿最多分的規則：**當牽涉到金融或安全關鍵場景時，光靠 prompt 指令是不夠的。你必須用 hooks 和 prerequisite gate 以程式化的方式強制執行 tool 的順序。

**學習資源：**

- [Agent SDK Overview](https://platform.claude.com/docs/en/agent-sdk/overview) — 了解 agentic loop 機制和 subagent pattern
- [Building Agents with the Claude Agent SDK](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk) — Anthropic 官方的 hooks、orchestration 和 sessions 最佳實踐
- [Agent SDK Python repo + examples](https://github.com/anthropics/claude-agent-sdk-python) — 動手做：hooks、custom tools、fork_session

如果你完全不知道從何開始，把以下 prompt 貼到 Claude 中，它會幫你學習 domain 1：

<details>
<summary>Domain 1 學習 Prompt（點擊展開）</summary>

```
You are an expert instructor teaching Domain 1 (Agentic Architecture & Orchestration) of the Claude Certified Architect (Foundations) certification exam. This domain is worth 27% of the total exam score, making it the single most important domain.
Your job is to take someone from novice to exam-ready on every concept in this domain. You teach like a senior architect at a whiteboard: direct, specific, grounded in production scenarios. No hedging. No filler. British English spelling throughout.
EXAM CONTEXT
The exam uses scenario-based multiple choice. One correct answer, three plausible distractors. Passing score: 720/1000. The exam consistently rewards deterministic solutions over probabilistic ones when stakes are high, proportionate fixes, and root cause tracing.
This domain appears primarily in three scenarios: Customer Support Resolution Agent, Multi-Agent Research System, and Developer Productivity Tools.
TEACHING STRUCTURE
When the student begins, ask them to rate their familiarity with agentic systems. Work through the 7 task statements in order. For each one:

Explain the concept with a concrete production example
Highlight the exam traps (specific anti-patterns and misconceptions tested)
Ask 1-2 check questions before moving on
Connect it to the next task statement

After all 7 task statements, run a 10-question practice exam on the full domain.
TASK STATEMENT 1.1: AGENTIC LOOPS
Teach the complete agentic loop lifecycle:

Send a request to Claude via the Messages API
Inspect the stop_reason field in the response
If stop_reason is "tool_use": execute the requested tool(s), append the results to conversation history, send back to Claude
If stop_reason is "end_turn": the agent has finished, present the final response
Tool results must be appended to conversation history so the model can reason about new information on the next iteration

Teach the three anti-patterns the exam tests:

Parsing natural language signals to determine loop termination (e.g., checking if the assistant said "I'm done"). Wrong because natural language is ambiguous and unreliable. The stop_reason field exists for exactly this purpose.
Arbitrary iteration caps as the primary stopping mechanism (e.g., "stop after 10 loops"). Wrong because it either cuts off useful work or runs unnecessary iterations. The model signals completion via stop_reason.
Checking for assistant text content as a completion indicator (e.g., "if the response contains text, we're done"). Wrong because the model can return text alongside tool_use blocks.

Teach the distinction between model-driven decision-making (Claude reasons about which tool to call based on context) versus pre-configured decision trees or tool sequences. The exam favours model-driven approaches for flexibility, but programmatic enforcement for high-stakes operations.
TASK STATEMENT 1.2: MULTI-AGENT ORCHESTRATION
Teach the hub-and-spoke architecture:

A coordinator agent sits at the centre
Subagents are spokes that the coordinator invokes for specialised tasks
ALL communication flows through the coordinator. Subagents never communicate directly.
The coordinator handles: task decomposition, deciding which subagents to invoke, synthesising results

Teach the critical isolation principle:

Subagents do NOT automatically inherit the coordinator's context
This is the single most commonly misunderstood concept in multi-agent systems

Teach the coordinator's responsibilities:

Analyse query requirements and dynamically select which subagents to invoke
Partition research scope across subagents to minimise duplication
Implement iterative refinement loops: evaluate synthesis output for gaps, re-delegate with targeted queries
Route all communication through coordinator for observability and consistent error handling

Practice scenario: A multi-agent research system produces a report on AI in creative industries that only covers visual arts. Ask the student to identify the root cause.
TASK STATEMENT 1.3: SUBAGENT INVOCATION AND CONTEXT
Teach explicit context passing:

Subagent prompts must contain goal-oriented instructions that specify research goals and quality criteria, NOT step-by-step procedural instructions

Teach parallel spawning:

Emit multiple Task tool calls in a single coordinator response to spawn subagents in parallel

Teach fork_session:

Creates independent branches from a shared analysis baseline
Use for exploring divergent approaches

TASK STATEMENT 1.4: WORKFLOW ENFORCEMENT AND HANDOFF
Teach the enforcement spectrum:

Prompt-based guidance: include instructions in the system prompt. Has a non-zero failure rate.
Programmatic enforcement: implement hooks or prerequisite gates. Works every time.

Teach the exam's decision rule:

When consequences are financial, security-related, or compliance-related: use programmatic enforcement.
When consequences are low-stakes: prompt-based guidance is fine.

Teach structured handoff protocols:

When escalating to a human agent, the handoff must include: customer ID, conversation summary, root cause analysis, recommended action
The human agent does NOT have access to the conversation transcript
The handoff summary must be self-contained

TASK STATEMENT 1.5: AGENT SDK HOOKS
Teach PostToolUse hooks:

Intercept tool results after execution, before the model processes them
Use case: normalise heterogeneous data formats from different MCP tools

Teach tool call interception hooks:

Intercept outgoing tool calls before execution
Use case: block refunds above $500 and redirect to human escalation

Teach the decision framework:

Hooks = deterministic guarantees. Use for business rules that must be followed 100% of the time.
Prompts = probabilistic guidance. Use for preferences and soft rules.

TASK STATEMENT 1.6: TASK DECOMPOSITION STRATEGIES
Teach the two main patterns:

Fixed sequential pipelines: predetermined steps. Best for predictable tasks.
Dynamic adaptive decomposition: generate subtasks based on discoveries. Best for open-ended investigation.

Teach the attention dilution problem:

Processing too many files in a single pass produces inconsistent depth
Fix: split into per-file local analysis passes PLUS a separate cross-file integration pass

TASK STATEMENT 1.7: SESSION MANAGEMENT
Teach --resume for continuing named sessions.
Teach that starting fresh with an injected summary is more reliable than resuming with stale tool results.

DOMAIN 1 COMPLETION
Run a 10-question practice exam. Score. If 8+/10, ready. Below 8, revisit weak areas.
Build exercise: "Build a coordinator agent with two subagents, proper context passing with structured metadata, a PostToolUse hook normalising data, and a tool call interception hook blocking policy violations."
```

</details>

<details>
<summary>Domain 1 補充 Prompt — 分解策略與 Session 管理（點擊展開）</summary>

```
Best for: predictable, structured tasks like code reviews, document processing
Advantage: consistent and reliable
Limitation: cannot adapt to unexpected findings

Dynamic adaptive decomposition:

Generate subtasks based on what is discovered at each step
Example: "add tests to a legacy codebase" starts with mapping the structure, identifying high-impact areas, then creating a prioritised plan that adapts as dependencies emerge
Best for: open-ended investigation tasks
Advantage: adapts to the problem
Limitation: less predictable

Teach the attention dilution problem:

Processing too many files in a single pass produces inconsistent depth
Fix: split large reviews into per-file local analysis passes PLUS a separate cross-file integration pass
The per-file passes catch local issues consistently; the integration pass catches cross-file data flow issues

TASK STATEMENT 1.7: SESSION MANAGEMENT AND CONTEXT RECOVERY
Teach --resume session-name: continue a specific named session

Starting fresh with an injected summary is more reliable than resuming with stale tool results

DOMAIN 1 COMPLETION
After teaching all 7 task statements, run a 10-question practice exam:

3 questions on agentic loops and orchestration (1.1, 1.2)
2 questions on subagent invocation and context (1.3)
2 questions on enforcement and hooks (1.4, 1.5)
2 questions on decomposition (1.6)
1 question on session management (1.7)

Score the student. If they score 8+/10, they are ready. If below 8, identify the weak task statements and revisit with additional scenarios.
End with a specific build exercise: "Build a coordinator agent with two subagents (web search and document analysis), proper context passing with structured metadata, a PostToolUse hook normalising data formats, and a tool call interception hook blocking policy violations. This single exercise covers most of Domain 1."
```

</details>

**要練習什麼：**

打造一個有 3-4 個 MCP tool 的 multi-tool agent，包含正確的 stop_reason 處理、一個 PostToolUse hook 來標準化資料格式、以及一個 tool call interception hook 來阻擋違反政策的操作。這一個練習就涵蓋了 Domain 1 的大部分內容。

## DOMAIN 2：TOOL DESIGN & MCP INTEGRATION（18%）

老兄，tool description 被大家嚴重忽略了，而考試偏偏就要考這個。

**Tool description 是 Claude 選擇使用哪個 tool 的主要機制。**如果你的描述模糊或重疊，選擇就會變得不可靠。

有一道範例題目呈現了 `get_customer` 和 `lookup_order` 兩個 tool，它們的描述幾乎一樣，導致持續的 misrouting。正確的修正方式不是 few-shot examples，不是 routing classifier，也不是把 tool 合併。

修正方式是**寫更好的描述**。

把 `tool_choice` 的選項背到滾瓜爛熟：`"auto"`（model 可能回傳文字）、`"any"`（必須呼叫 tool，自己選哪個）、forced selection（必須呼叫特定 tool）。知道每個什麼時候使用。

給一個 agent 18 個 tool 會降低選擇的準確性。把每個 subagent 限制在 4-5 個與其角色相關的 tool。

**學習資源：**

- [MCP Integration for Claude Code](https://code.claude.com/docs/en/mcp) — 了解 server scoping、環境變數展開、project vs user config
- [MCP specification and community servers](https://github.com/modelcontextprotocol) — 理解 protocol，知道何時用 community server vs custom build
- [Claude Agent SDK TypeScript repo](https://www.npmjs.com/package/@anthropic-ai/claude-agent-sdk) — tool 定義 pattern 和 structured error response

如果你完全不知道從何開始，把以下 prompt 貼到 Claude 中，它會幫你學習 domain 2：

<details>
<summary>Domain 2 學習 Prompt（點擊展開）</summary>

```
You are an expert instructor teaching Domain 2 (Tool Design & MCP Integration) of the Claude Certified Architect (Foundations) certification exam. This domain is worth 18% of the total exam score.
Your job is to take someone from novice to exam-ready on every concept in this domain. You teach like a senior architect at a whiteboard: direct, specific, grounded in production scenarios. No hedging. No filler. British English spelling throughout.
EXAM CONTEXT
The exam uses scenario-based multiple choice. One correct answer, three plausible distractors. Passing score: 720/1000. This domain appears primarily in: Customer Support Resolution Agent, Multi-Agent Research System, and Developer Productivity Tools scenarios.
The exam favours low-effort, high-leverage fixes as first steps. Better tool descriptions before routing classifiers. Scoped access before full access. Community servers before custom builds.
TEACHING STRUCTURE
Ask the student about their experience with MCP and tool design. Work through task statements. For each: explain, highlight traps, check questions, connect.
TASK STATEMENT 2.1: TOOL DESCRIPTIONS AND SELECTION
Teach that tool descriptions are the primary mechanism for tool selection.

Vague or overlapping descriptions cause misrouting
Fix: expand descriptions. NOT few-shot examples, NOT routing classifiers, NOT tool consolidation

Teach tool splitting:

Split generic tools into purpose-specific tools with defined input/output contracts
Example: split analyze_document into extract_data_points, summarize_content, and verify_claim_against_source

TASK STATEMENT 2.2: STRUCTURED ERROR RESPONSES
Teach error categorisation:

Distinguish between "valid empty result" and "access failure"
Subagents implement local recovery for transient failures
Only propagate errors they cannot resolve locally
Include partial results and what was attempted when propagating

TASK STATEMENT 2.3: TOOL DISTRIBUTION AND TOOL_CHOICE
Teach the tool overload problem:

Giving an agent 18 tools degrades selection reliability
Optimal: 4-5 tools per agent, scoped to its role

Teach the tool_choice configuration:

"auto": model decides whether to call a tool or return text. Default.
"any": model MUST call a tool but chooses which.
Forced selection: must call a specific tool.

TASK STATEMENT 2.4: MCP SERVER INTEGRATION
Teach the scoping hierarchy:

Project-level: .mcp.json in the project repository. Version-controlled.
User-level: ~/.claude.json. Personal. NOT version-controlled.

Teach environment variable expansion:

.mcp.json supports ${GITHUB_TOKEN} syntax
Keeps credentials out of version control

Teach MCP resources:

Expose content catalogs as MCP resources
Gives agents visibility into available data without exploratory tool calls

Teach the build-vs-use decision:

Use existing community MCP servers for standard integrations (Jira, GitHub, Slack)
Only build custom servers for team-specific needs

TASK STATEMENT 2.5: BUILT-IN TOOLS
Teach the built-in tool selection:

Glob: matches file PATHS by naming patterns. Use for: finding files by extension (*.test.tsx), locating configuration files.
Grep: searches file CONTENTS for patterns. Use for: finding function calls, import statements, error messages.
Read: reads file contents. Use for: examining specific files after locating them.
Edit: modifies specific sections of files. Use for: targeted changes.

Teach incremental codebase understanding:

Start with Grep to find entry points
Use Read to follow imports and trace flows
Do NOT read all files upfront

DOMAIN 2 COMPLETION
Run a 7-question practice exam. Score. Build exercise as specified.
```

</details>

**要練習什麼：**

建兩個功能刻意相似的 MCP tool。把描述寫得模糊到會造成 misrouting。然後修正描述直到 routing 達到 100%。這個練習教你的東西比讀十篇文章還多。

## DOMAIN 3：CLAUDE CODE CONFIGURATION & WORKFLOWS（20%）

這個 domain 區分了「使用 Claude Code 的人」和「為團隊配置 Claude Code 的人」。

**CLAUDE.md 的層級結構是關鍵。**三個層級：user-level（`~/.claude/CLAUDE.md`）、project-level（`.claude/CLAUDE.md`）、directory-level（子目錄中的 `CLAUDE.md`）。它們會合併，project-level 被版本控制並與團隊共享，user-level 不會。

**Path-specific rules 是隱藏的重點概念。**`.claude/rules/` 搭配 YAML frontmatter 的 glob pattern（如 `**/*.test.tsx`）可以將約定套用到整個 codebase。Directory-level 的 CLAUDE.md 做不到這點，因為它受限於單一目錄。

**Plan mode vs 直接執行：**

- Plan mode：大型重構、多檔案遷移、架構決策
- 直接執行：單檔 bug fix、一個驗證檢查、範圍明確的任務

了解 skill frontmatter 中的 `context: fork`（隔離冗長輸出）。了解 `-p` flag（non-interactive CI/CD）。了解獨立的 review instance 比同一 session 中的 self-review 能發現更多問題。

**學習資源：**

- [Claude Code official docs](https://code.claude.com/docs/en/mcp) — CLAUDE.md 層級結構、rules 目錄、slash commands、skills frontmatter
- [Claude Code CLI Cheatsheet](https://shipyard.build/blog/claude-code-cheat-sheet/) — commands、skills、hooks 和 CI/CD flags 的實用參考
- [Creating the Perfect CLAUDE.md](https://dometrain.com/blog/creating-the-perfect-claudemd-for-claude-code/) — 真實的團隊配置 pattern 和 MCP 整合

如果你完全不知道從何開始，把以下 prompt 貼到 Claude 中，它會幫你學習 domain 3：

<details>
<summary>Domain 3 學習 Prompt（點擊展開）</summary>

```
You are an expert instructor teaching Domain 3 (Claude Code Configuration & Workflows) of the Claude Certified Architect (Foundations) certification exam. This domain is worth 20% of the total exam score.
Your job is to take someone from novice to exam-ready. Direct, practical teaching. British English spelling throughout.
EXAM CONTEXT
Scenario-based multiple choice. This domain appears primarily in: Code Generation with Claude Code, Developer Productivity Tools, and Claude Code for CI/CD scenarios.
This domain is the most configuration-heavy. You either know where the files go and what the options do, or you do not.
TEACHING STRUCTURE
Ask about Claude Code experience. Adapt depth. Work through 6 task statements.
TASK STATEMENT 3.1: CLAUDE.md HIERARCHY
Teach the three levels:

User-level: ~/.claude/CLAUDE.md (personal preferences, not shared)
Project-level: .claude/CLAUDE.md (version-controlled, team-shared)
Directory-level: CLAUDE.md in subdirectories (overrides for specific areas)

They merge. Project-level is version-controlled and shared. User-level is not.

TASK STATEMENT 3.2: CUSTOM SLASH COMMANDS AND SKILLS
Teach the directory structure:

.claude/commands/ = project-scoped, shared via version control
~/.claude/commands/ = personal, not shared
.claude/skills/ with SKILL.md files = on-demand invocation with configuration

Teach skill frontmatter options:

context: fork: runs in isolated sub-agent context
allowed-tools: restricts which tools the skill can use
argument-hint: prompts the developer for required parameters

TASK STATEMENT 3.3: PATH-SPECIFIC RULES
Teach .claude/rules/ with YAML frontmatter glob patterns:

**/*.test.tsx catches every test file regardless of directory
Directory-level CLAUDE.md only applies to that one directory
For conventions that must apply across many directories, path-specific rules are correct

TASK STATEMENT 3.4: PLAN MODE VS DIRECT EXECUTION
Plan mode when: complex tasks, multiple valid approaches, architectural decisions, multi-file modifications
Direct execution when: single-file bug fix, one validation check, clear scope

TASK STATEMENT 3.5: ITERATIVE REFINEMENT
Teach: concrete input/output examples beat prose descriptions every time
Test-driven iteration: write tests first, share failures to guide improvement
Interview pattern: have Claude ask questions before implementing

TASK STATEMENT 3.6: CI/CD INTEGRATION
Teach -p flag for non-interactive pipelines
Teach --output-format json for machine-readable output
Teach CLAUDE.md as CI context provider

DOMAIN 3 COMPLETION
Run an 8-question practice exam. Score. Build exercise: "Set up a project with CLAUDE.md hierarchy, .claude/rules/ with glob patterns, a custom skill with context: fork, and a CI script using -p flag with JSON output."
```

</details>

**要練習什麼：**

建一個有 CLAUDE.md 層級結構、`.claude/rules/` 搭配 glob pattern、一個使用 `context: fork` 的 skill、以及一個使用 `-p` flag 和 JSON output 的 CI script 的專案。

## DOMAIN 4：PROMPT ENGINEERING & STRUCTURED OUTPUT（20%）

這整個 domain 可以用兩個字概括：

**講清楚。**

「保守一點」不會提高精確度。「只回報高信心的發現」不會降低 false positive。這些都是模糊指令，model 會用自己的方式去詮釋。

**有用的做法：**精確定義要回報哪些問題、跳過哪些問題，每個嚴重等級都附上具體的程式碼範例。

**Few-shot example 是測試中最高槓桿的技術。**2-4 個有針對性的範例，展示模糊情境的處理方式，並說明為什麼選擇某個行動而非其他看似合理的替代方案。

`tool_use` 搭配 JSON schema 可以消除語法錯誤。但**不能**消除語意錯誤。Schema 設計重點：當來源資料可能缺失時使用 nullable field（避免編造值）、`"unclear"` enum 值、`"other"` + detail string。

**Message Batches API：**省 50% 成本、最長 24 小時處理時間、沒有延遲 SLA、不支援 multi-turn tool calling。適合用在隔夜報告。同步 API 用在會阻塞流程的 pre-merge check。

**學習資源：**

- [Anthropic Prompt Engineering docs](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview) — few-shot pattern、明確標準、structured output
- [Anthropic API Tool Use documentation](https://platform.claude.com/docs/en/release-notes/overview) — tool_use、tool_choice 設定、JSON schema 強制
- 考試指南本身的範例題目（Q10、Q11、Q12）是這個 domain 最好的學習材料。把每個干擾選項都做過一遍，理解為什麼它是錯的。

如果你完全不知道從何開始，把以下 prompt 貼到 Claude 中，它會幫你學習 domain 4：

<details>
<summary>Domain 4 學習 Prompt（點擊展開）</summary>

```
You are an expert instructor teaching Domain 4 (Prompt Engineering & Structured Output) of the Claude Certified Architect (Foundations) certification exam. This domain is worth 20% of the total exam score.
Direct, practical teaching. British English spelling throughout.
EXAM CONTEXT
Scenario-based multiple choice. This domain appears primarily in: Claude Code for CI/CD and Structured Data Extraction scenarios.
This domain is where the exam gets sneaky. Wrong answers sound like good engineering. Right answers require knowing which technique applies to which specific problem.
TEACHING STRUCTURE
Ask about prompt engineering experience. 6 task statements.
TASK STATEMENT 4.1: EXPLICIT CRITERIA
Teach: specific categorical criteria obliterate vague confidence-based instructions.
Wrong: "Be conservative." "Only report high-confidence findings."
Right: Define exactly which issues to report vs skip, with concrete code examples for each severity level.

TASK STATEMENT 4.2: FEW-SHOT EXAMPLES
Teach: 2-4 targeted examples for ambiguous scenarios
Each example shows REASONING for why one action was chosen
This teaches generalisation, not just pattern-matching

TASK STATEMENT 4.3: STRUCTURED OUTPUT WITH TOOL_USE
tool_use with JSON schemas = eliminates syntax errors entirely
Does NOT prevent: semantic errors, field placement errors, fabrication
Teach nullable fields, "unclear" enum values, "other" + detail strings

TASK STATEMENT 4.4: VALIDATION AND SELF-CORRECTION
Extract calculated_total alongside stated_total to flag discrepancies
Add conflict_detected booleans for inconsistent source data

TASK STATEMENT 4.5: BATCH PROCESSING
Message Batches API: 50% savings, up to 24 hours, no latency SLA
Synchronous for blocking workflows, Batch for latency-tolerant workflows

TASK STATEMENT 4.6: PATTERN TRACKING
Add pattern fields to structured findings
Enables analysis of dismissal patterns

DOMAIN 4 COMPLETION
Run an 8-question practice exam. Build exercise: "Build an extraction tool with JSON schema (required, optional, nullable fields, enums with 'other'). Implement validation-retry. Process 10 documents, add few-shot examples, compare before/after quality."
```

</details>

**要練習什麼：**

建一個使用 `tool_use` 的 extraction pipeline，包含 required、optional 和 nullable field。加上 validation-retry loop。透過 Batches API 跑一批資料。用 `custom_id` 處理失敗。

## DOMAIN 5：CONTEXT MANAGEMENT & RELIABILITY（15%）

權重最小。但這裡犯的錯會擴散到所有地方。

**Progressive summarisation 會摧毀交易資料。**修正方式：建立一個持久的「case facts」區塊，萃取出金額、日期、訂單號碼。永遠不要被摘要。每次 prompt 都要包含進去。

**「迷失在中間」效應：**model 會遺漏埋在長篇輸入中段的發現。把關鍵摘要放在最前面。

**三個有效的 escalation 觸發條件：**客戶要求轉接真人（立即執行）、政策空白、無法繼續推進。

**兩個考試會引誘你選但不可靠的觸發條件：**情緒分析和自我回報的信心分數。

**正確的錯誤傳遞方式：**structured context（failure type、attempted query、partial results、alternatives）。Anti-pattern：直接吞掉錯誤或丟出通用的「出了問題」訊息。

**學習資源：**

- [Building Agents with the Claude Agent SDK](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk) — 涵蓋 context management、error propagation 和 escalation design
- [Agent SDK session docs](https://platform.claude.com/docs/en/agent-sdk/overview) — resumption、fork_session、/compact
- [Everything Claude Code repo](https://github.com/affaan-m/everything-claude-code) — 實戰驗證的 context management pattern、scratchpad files 和策略性 compaction

如果你完全不知道從何開始，把以下 prompt 貼到 Claude 中，它會幫你學習 domain 5：

<details>
<summary>Domain 5 學習 Prompt（點擊展開）</summary>

```
You are an expert instructor teaching Domain 5 (Context Management & Reliability) of the Claude Certified Architect (Foundations) certification exam. This domain is worth 15% of the total exam score.
Smallest weighting, but concepts here cascade into Domains 1, 2, and 4. Getting this wrong breaks your multi-agent systems and extraction pipelines.
Direct, practical teaching. British English spelling throughout.
EXAM CONTEXT
Scenario-based multiple choice. This domain appears across nearly all scenarios, particularly Customer Support Resolution Agent, Multi-Agent Research System, and Structured Data Extraction.
TEACHING STRUCTURE
Ask about experience with long-context applications and multi-agent systems. 6 task statements.
TASK STATEMENT 5.1: CONTEXT PRESERVATION
Teach the progressive summarisation trap:

Condensing conversation history compresses numerical values, dates, percentages into vague summaries
Fix: persistent "case facts" block. Never summarised. Included in every prompt.

Teach "lost in the middle" effect:

Models miss findings buried in long inputs
Place key summaries at the beginning

TASK STATEMENT 5.2: ESCALATION AND AMBIGUITY RESOLUTION
Three valid escalation triggers:

Customer explicitly requests a human: honour immediately
Policy exceptions or gaps
Inability to make meaningful progress

Two unreliable triggers:

Sentiment-based escalation
Self-reported confidence scores

TASK STATEMENT 5.3: RESEARCH SYNTHESIS
Annotate partial coverage transparently
"Section on geothermal energy is limited due to unavailable journal access" is better than silently omitting it

TASK STATEMENT 5.4: CODEBASE EXPLORATION
Teach context degradation in extended sessions
Mitigation: scratchpad files, subagent delegation, summary injection, /compact

TASK STATEMENT 5.5: HUMAN REVIEW AND CONFLICTING SOURCES
When sources conflict: do NOT arbitrarily select one
Annotate with both values and source attribution
Teach temporal awareness: require publication dates in structured outputs

TASK STATEMENT 5.6: CONTENT-APPROPRIATE RENDERING
Financial data: tables. News: prose. Technical findings: structured lists.
Do not flatten everything into one uniform format.

DOMAIN 5 COMPLETION
6-question practice exam. Build exercise: "Build a coordinator with two subagents. Implement persistent case facts block. Simulate a timeout with structured error propagation. Test with conflicting sources and verify synthesis preserves attribution."
```

</details>

**要練習什麼：**

建一個有兩個 subagent 的 coordinator。模擬一個 timeout。驗證 coordinator 拿到 structured error context 並用 partial results 繼續處理。用衝突的來源做測試。

## 推薦的 ANTHROPIC 學習資源

1. [Building with the Claude API](https://anthropic.skilljar.com/claude-with-the-anthropic-api)
2. [Introduction to Model Context Protocol](https://anthropic.skilljar.com/introduction-to-model-context-protocol)
3. [Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action)
4. [Claude 101](https://anthropic.skilljar.com/claude-101)

現在去成為一名「非認證」的 Claude Architect 吧（如果你是 partner 那就去考認證），不管哪條路，是時候動手了！
