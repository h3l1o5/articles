---
title: "obra/superpowers 深度拆解：為什麼這是 2026 年最有影響力的 AI 開發紀律"
date: 2026-04-08
categories: [AI]
tags: [claude-code, skills, ai-agents, tdd, jesse-vincent, superpowers, plugin, agent-discipline]
---

> 原始 repo：[obra/superpowers](https://github.com/obra/superpowers) by **Jesse Vincent** (@obra)

---

## 為什麼這個 repo 重要

140k stars。十二萬個 fork。

你如果不在 Claude Code 社群裡可能感覺不到這個數字的怪——一個半年前才開的 skill collection，stars 已經超過大部分主流前端框架，比 React 還快。它的影響力甚至滲透到 Anthropic 自己的官方範例：你今天去看任何高品質的第三方 Claude Code skill，frontmatter 的 description 寫法、SessionStart hook 的用法、把 skill 分成 rigid 跟 flexible 兩類的哲學——大半都是從 obra/superpowers 抄來的。

repo 的描述只有一句話：**"An agentic skills framework & software development methodology that works."**

作者 Jesse Vincent（GitHub handle `obra`）不是新面孔。他在 2000 年代早期就在維護 RT、Best Practical 那條線的開源工具，後來搞 Keyboardio，現在在 Prime Radiant。README 把作者寫成「Jesse Vincent 與 Prime Radiant 的同事們」，但整個 repo 的語氣明顯是 obra 一個人的——強硬、有原則、對 LLM 的失敗模式有非常具體的觀察、語言裡帶著對「合理化藉口」的惡意。

我會試著把這篇寫到值得 140k star 的深度。會拆：repo 的核心 thesis、整個 plugin 架構（包括 6 個跨工具的 install 路徑）、16 個 skill 的完整盤點、7 個旗艦 skill 的深度解剖、rigid 跟 flexible 兩種 skill 的設計哲學、橫跨整個 repo 的招牌技術，最後是我對它的誠實評論——包括我認為哪些地方它有點過頭。

## 核心 thesis：一個 coding agent 不應該跳到 code

從 README 直接拎一句：「a complete software development workflow for your coding agents, built on top of a set of composable 'skills' and some initial instructions that make sure your agent uses them.」

整個 repo 的核心主張極為清楚：**coding agent 不應該跳到 code。**

它應該：

1. **先從使用者那裡 pull 出 spec**——而不是猜需求
2. **提出一個 design**——把方案攤開
3. **拿到 approval**
4. **寫一份「初級工程師能照著做」的 plan**
5. **透過 subagent 執行**，每個 task 都用紅綠燈 TDD
6. **YAGNI 跟 DRY，加上 task 之間的 review gate**

而且這一整套流程的觸發**不需要使用者特別講**——「Your coding agent just has Superpowers」。auto-trigger 透過一個 SessionStart hook 實現，每次 `/clear`、每次 compact、每次新 session，都會自動把 `using-superpowers` 這個 meta-skill 注入 context。

repo 在 README 列了四根 pillar：

- "Test-Driven Development - Write tests first, always"
- "Systematic over ad-hoc - Process over guessing"
- "Complexity reduction - Simplicity as primary goal"
- "Evidence over claims - Verify before declaring success"

你大概能感覺到這個 repo 的語氣。它不是中性的，它是傳教式的。每個 skill 都會用「The Iron Law」、「Violating the letter of the rules is violating the spirit」、「You cannot rationalize your way out of this」這種句子。我等會兒會討論這個語氣是不是過頭，但先告訴你：**這個語氣是有意的，而且在工程上有效果。**

## Plugin 架構：一個 source of truth，六種 install 路徑

Superpowers 的厲害之一是它的跨平台覆蓋。同一份 source 同時支援：

- **Claude Code** — `.claude-plugin/plugin.json` (`name: superpowers`, version 5.0.7) + `.claude-plugin/marketplace.json`
- **Cursor** — `.cursor-plugin/plugin.json`
- **Codex** — `.codex/INSTALL.md`
- **OpenCode** — `.opencode/INSTALL.md` + `.opencode/plugins/superpowers.js`
- **Gemini CLI** — `gemini-extension.json`
- **GitHub Copilot CLI** — 透過 `obra/superpowers-marketplace`

per-host 還有對應的 system prompt stub：`AGENTS.md`、`CLAUDE.md`、`GEMINI.md`。為什麼 OpenCode 跟 Codex 沒有原生 marketplace 的問題要怎麼解？答案很 obra：**讓 agent 自己 bootstrap**。安裝指令是「告訴你的 agent：去 fetch 並執行 raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md 的指示。」也就是說，install 流程本身就是用 agent 完成的。

### 自動觸發的關鍵：SessionStart hook

`hooks/hooks.json` 配了一個 SessionStart hook，matcher 是 `startup|clear|compact`，觸發時跑 `hooks/run-hook.cmd session-start`，最終呼叫 `hooks/session-start` 這支 script。這個 hook 在每一次：

- 新 session 開啟
- 使用者 `/clear`
- context 自動 compact

之後都會跑，把 `using-superpowers` 這個 meta-skill 的內容重新注入 context。**這是整個 repo「自動有用」的關鍵機制**——它解決了第三方 skill 最大的失敗模式：agent 從來不記得用它們。

### 目錄結構

```
.claude-plugin/    Claude Code plugin manifest + marketplace
.cursor-plugin/    Cursor plugin manifest
.codex/            Codex 安裝指引
.opencode/         OpenCode plugin loader
hooks/             SessionStart hook
commands/          3 個 slash commands: brainstorm, write-plan, execute-plan
agents/            packaged subagent: code-reviewer
skills/            16 個 skill ← 重點
tests/             skill 觸發測試、token usage 分析
docs/superpowers/  自己 dogfood 的 spec + plan 目錄
```

`tests/` 目錄特別值得一提：裡面有 `tests/skill-triggering/`、`tests/explicit-skill-requests/`、`tests/subagent-driven-dev/`、`tests/opencode/`、`tests/claude-code/analyze-token-usage.py`。**這是一個有實際測試 infrastructure 的 prompt engineering 專案**——透過真實 prompt 對 agent 做行為測試，並且追蹤 token 成本。在開源社群裡這非常罕見。

## 16 個 skill 全圖

直接列：

1. **`using-superpowers`** — 元 skill：「Use when starting any conversation」。每次 session 開始的入口。
2. **`brainstorming`** — 「You MUST use this before any creative work」。所有創造性工作的硬閘門。
3. **`writing-plans`** — 寫實作計畫
4. **`executing-plans`** — 帶 review checkpoint 在 separate session 執行計畫
5. **`subagent-driven-development`** — 同 session、每 task 開新 subagent + 兩階段 review
6. **`dispatching-parallel-agents`** — 2+ 個獨立任務時平行派工
7. **`test-driven-development`** — Red-Green-Refactor 紀律
8. **`systematic-debugging`** — 四階段 root-cause 流程
9. **`verification-before-completion`** — 宣告完成前必須有 evidence
10. **`requesting-code-review`** — pre-review checklist
11. **`receiving-code-review`** — 在 performative 同意之前先做技術審查
12. **`using-git-worktrees`** — feature work 之前先 isolate workspace
13. **`finishing-a-development-branch`** — merge / PR / discard 決定
14. **`writing-skills`** — 元 skill：用 TDD 風格寫新 skill

（內部還有 `superpowers:brainstorm`、`superpowers:write-plan`、`superpowers:execute-plan` 三個 deprecated alias，指向上面的 gerund 版本，會發 deprecation warning。）

數量看起來不多——只有 14 個正式 skill——但每個都是一個非常密的 process document，加上一堆 supporting file。例如 `skills/systematic-debugging/` 還配了 `root-cause-tracing.md`、`defense-in-depth.md`、`condition-based-waiting.md`、三份 pressure-test 範本（`test-pressure-1/2/3.md`）、一份 academic 範本、一支 `find-polluter.sh` script、跟一份 `CREATION-LOG.md`。`skills/writing-skills/` 配了 `anthropic-best-practices.md`、`persuasion-principles.md`、`testing-skills-with-subagents.md`、`graphviz-conventions.dot`、`render-graphs.js`、跟一份 `examples/CLAUDE_MD_TESTING.md`。`skills/brainstorming/` 甚至 ship 了一個完整的 Node 腦力激盪 server（`scripts/server.cjs`、`helper.js`、`frame-template.html`、start/stop scripts）。

**這是一個非常認真的 skill collection，不是隨手寫的 Markdown 集合。**

## 7 個旗艦 skill 深度解剖

接下來每個 skill 我都會點出它最核心的設計，而不是廣度地概述。這 7 個是 obra/superpowers 之所以成為 obra/superpowers 的原因。

### 1. `using-superpowers` — 元門神

`skills/using-superpowers/SKILL.md`。

開頭就放一個 `<SUBAGENT-STOP>` 守衛：subagent 跳過這個 skill，因為被 dispatch 的 subagent 是來執行特定任務的，不應該被元 skill 拖累。然後是一個 `<EXTREMELY-IMPORTANT>` 區塊：

> "If you think there is even a 1% chance a skill might apply ... you ABSOLUTELY MUST invoke the skill"
>
> "You cannot rationalize your way out of this."

這個 1% 規則是整個 repo 對「agent 該不該檢查 skill」的核心答案——只要不是 100% 確定不需要，就檢查。

skill 還宣告了一個明確的指令優先級：
1. 使用者在 CLAUDE.md / GEMINI.md / AGENTS.md 裡的指令
2. Superpowers skills
3. 預設 system prompt

並且配上一個非常重要的明文：**「If CLAUDE.md says 'don't use TDD' and a skill says 'always use TDD,' follow the user's instructions.」** 這是給整個 repo 一個 escape hatch——使用者永遠贏 skill。這個明文很重要，因為沒有它的話，整個 repo 的「Iron Law」會變得太極權。

skill 包含一張 DOT 流程圖（`digraph skill_flow`），強制規則是：「Invoke relevant or requested skills BEFORE any response or action」——包括在問澄清問題之前、在 EnterPlanMode 之前、有 brainstorm 分支、有「Has checklist? → Create TodoWrite todo per item」的分支。

#### Red Flags 表格

這是整個 repo 模板的源頭。`using-superpowers` 的版本長這樣（節選）：

| Thought | Reality |
|---|---|
| "This is just a simple question" | "Questions are tasks. Check for skills." |
| "I need more context first" | "Skill check comes BEFORE clarifying questions." |
| "Let me explore the codebase first" | "Skills tell you HOW to explore." |
| "This doesn't need a formal skill" | "If a skill exists, use it." |
| "I remember this skill" | "Skills evolve. Read current version." |
| "The skill is overkill" | "Simple things become complex." |
| "I know what that means" | "Knowing the concept ≠ using the skill." |

這個表格的設計哲學跟 addyosmani/agent-skills 的 Common Rationalizations 表格非常像（時序上 obra 更早），核心是：**LLM 在決定是否「跳過」某件事的時候，會自己生成藉口**。如果你不在它的 reasoning path 上預先擺好反駁，它就會用自己的理由說服自己跳過。把藉口列出來並當場反駁，是堵住這條路的唯一辦法。

### 2. `brainstorming` — 動手前的硬閘門

`skills/brainstorming/SKILL.md`。description 直接寫 「You MUST use this before any creative work」。

開頭一個 `<HARD-GATE>`：

> "Do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action until you have presented a design and the user has approved it. This applies to EVERY project regardless of perceived simplicity."

然後預先反駁一個典型藉口：

> "'This Is Too Simple To Need A Design' ... 'Simple' projects are where unexamined assumptions cause the most wasted work."

**這條規則是整個 repo 最 controversial 的設計**——它強制 agent 在任何「創造性工作」之前先腦力激盪。使用者覺得「我只是想加個 CSS class」、「我只是想改個 typo」，agent 也要先過這關。我等會兒會在誠實評論裡討論這合不合理。

#### 9 步驟 checklist

每一步都會變成一個 TodoWrite item：

1. 探索 project context
2. 提供 visual companion（用獨立訊息）
3. 一次問一個澄清問題
4. 提出 2-3 個方法
5. 分段呈現 design，每段都要 approval
6. 寫成 `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` 並 commit
7. spec 自我審查（placeholder / consistency / scope / ambiguity）
8. 使用者 review spec
9. 呼叫 `writing-plans`

#### 強制 transition

skill 包含一個 DOT 圖（`digraph brainstorming`），終點是「Invoke writing-plans skill」。然後 skill 明文寫死：

> "The ONLY skill you invoke after brainstorming is writing-plans."

**這是整個 repo 的「skill 不只是知識，是 workflow engine」的具體化**。skill 之間的轉換是 hard-coded 的，你不能 brainstorm 完直接跳到 execute。這後面我也會評論。

#### 規模規則

- "One question at a time"
- "Multiple choice preferred"
- "YAGNI ruthlessly"
- "Lead with your recommended option"
- 文件規模：「a few sentences if straightforward, up to 200-300 words if nuanced」

skill 還 ship 了一個本地 web-based **Visual Companion** 用來畫 mockup / 流程圖。Offer 必須是獨立訊息，而且每個問題都要評估「使用者是不是看圖比讀文字更好懂」。

### 3. `test-driven-development` — Iron Law

`skills/test-driven-development/SKILL.md`。

開頭三句話奠定整個 skill 的調子：

1. "Write the test first. Watch it fail. Write minimal code to pass."
2. "If you didn't watch the test fail, you don't know if it tests the right thing."
3. "Violating the letter of the rules is violating the spirit of the rules."

第三句是給「我懂精神就好啦」這種藉口的預先反駁。

#### The Iron Law

接下來是一個 fenced code block：

```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

然後是無情的刪除規則：

> "Write code before the test? Delete it. Start over."
>
> "Don't keep it as 'reference' / Don't 'adapt' it while writing tests / Don't look at it / Delete means delete."

**這是 obra 對「我先寫了 code，現在補測試」這種偷吃步的零容忍宣告**。如果你已經寫了 code，唯一允許的動作是把它**刪掉**——不是「保留作參考」、不是「在寫測試的時候邊看」，是徹底 `rm`。

#### Red-Green-Refactor 流程圖

skill 包含一個 DOT 圖（`rankdir=LR`），把 RED → verify_red → GREEN → verify_green → REFACTOR 畫出來。每個階段都有 `<Good>` 跟 `<Bad>` 標記的 code 範例。例如 RED 階段的 Good 範例是「測試使用真實的 function」，Bad 範例是「測試 mock 掉 function-under-test」——這精準打中了 LLM 寫測試最常見的失敗模式。

#### Rationalizations 表格（11 列）

| Rationalization | Reality |
|---|---|
| "Too simple to test" | "Simple code breaks. Test takes 30 seconds." |
| "I'll test after" | "Tests passing immediately prove nothing." |
| "Keep as reference, write tests first" | "You'll adapt it. That's testing after." |
| "Existing code has no tests" | "You're improving it. Add tests for existing code." |

每一列都對應一個我看過真實 agent 真的會說的話。

#### 「STOP and Start Over」紅旗清單

13 條，包括：
- "Test passes immediately"
- "Can't explain why test failed"
- "'Keep as reference' or 'adapt existing code'"
- "'It's about spirit not ritual'"

全部都收斂到同一個動作：「Delete code. Start over with TDD.」

#### Verification Checklist

8 個 checkbox，包括：
- "Watched each test fail before implementing"
- "Each test failed for expected reason (feature missing, not typo)"
- "Output pristine (no errors, warnings)"

最後一句：「Can't check all boxes? You skipped TDD. Start over.」

#### 最終規則

skill 結尾用一個極簡的二分法收束：

> "Production code → test exists and failed first / Otherwise → not TDD"

**這是整個 repo 最不留餘地的 skill。** 我會說它有點過頭，但我也承認：當你的 agent 真的在做 production work 而不是 prototype，這種強硬規則的價值會顯出來。

### 4. `verification-before-completion` — 全 repo 最辛辣的一段話

`skills/verification-before-completion/SKILL.md`。

開頭那句是整個 repo 我最喜歡的一句話：

> "Claiming work is complete without verification is dishonesty, not efficiency."

它的 Iron Law：

```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

接著：

> "If you haven't run the verification command in this message, you cannot claim it passes."

#### Gate function（5 步驟）

IDENTIFY → RUN → READ → VERIFY → ONLY THEN claim。「Skip any step = lying, not verifying.」

#### Common Failures 表格（7 列）

| Claim | Requires | Not Sufficient |
|---|---|---|
| Tests pass | Fresh "0 failures" output | Previous run, "should pass" |
| Agent completed | VCS diff shows changes | Agent reports "success" |
| Regression test works | Red-green cycle verified | Test passes once |

注意「Agent completed」那一列——它在說：**就算另一個 agent 跟你說「成功了」，也不能信，要看 git diff 證據**。這對 multi-agent workflow 特別重要。

#### Red flags 列表

- "Using 'should', 'probably', 'seems to'"
- "Expressing satisfaction before verification ('Great!', 'Perfect!', 'Done!')"
- "Trusting agent success reports"
- "ANY wording implying success without having run verification"

第二條的「Great! Perfect! Done!」是直接針對 LLM 那種討好型語氣的鎮壓。

#### 「Why This Matters」——這段是整個 repo 最有人格的部分

skill 的最後一節提到「24 個失敗記憶」，然後是一句直接的話：「your human partner said 'I don't believe you' - trust broken」，接著：「Honesty is a core value. If you lie, you'll be replaced.」

**這段話把 LLM 當成有道德責任的員工在罵。** 從工程角度看這是非常規的 prompt 設計——把模型擬人化、給它道德重量、暗示「不誠實會被開除」。但它運作有效，因為 LLM 是 token predictor，當你給它一個有強烈道德 framing 的 context，它的後續輸出會 align 到那個 framing。

### 5. `systematic-debugging` — 四階段 root cause + 三 fix 上限

`skills/systematic-debugging/SKILL.md`。

Iron law：

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

> "Random fixes waste time and create new bugs."

#### 四個強制 phase

**Phase 1：Root Cause Investigation**
- 仔細讀 error
- 重現 bug
- 檢查近期變更
- 在每個 component boundary 加 diagnostic instrumentation
- 反向追資料流（delegate to `root-cause-tracing.md`）

skill 還 ship 了一個具體的多層 shell 範例，覆蓋 workflow → env → keychain → codesign 各層。

**Phase 2：Pattern Analysis**
- 找運作正常的範例
- 對照 reference（"read every line, don't skim"）
- 找出所有差異（"don't assume 'that can't matter'"）

**Phase 3：Hypothesis & Testing**
- 單一假設，必須寫成「I think X is the root cause because Y」的句型
- 最小可能變更
- 一次只動一個變數
- 失敗時形成「新假設」而不是「在原 fix 上加 fix」

**Phase 4：Implementation**
- 先寫 failing test
- 單一 fix
- verify

#### 3-fix 上限

這個 skill 最聰明的設計：

> "If ≥ 3: STOP and question the architecture."
>
> "Each fix reveals new shared state/coupling ... this is NOT a failed hypothesis — this is a wrong architecture."

**這是把「fix-on-fix 地獄」轉化成診斷信號的具體手法。** 一般 agent 在第三、第四、第五個 fix 還在猜原因；這個 skill 強制在第三個 fix 的時候停下來，宣告「這不是 hypothesis 失敗，這是 architecture 錯了」。

#### 「使用者在告訴你你做錯了」訊號清單

skill 列出 human 在指出 agent 走歪時會用的句子：
- "Is that not happening?"
- "Will it show us...?"
- "Stop guessing"
- "Ultrathink this"
- "We're stuck?"

> "When you see these: STOP. Return to Phase 1."

這是非常 obra 的設計——它把 human-agent 互動裡的「重定向訊號」明文化。當 agent 接收到這些句子時，它應該意識到「我走偏了」並回到 Phase 1。

#### 自我宣稱的數據

skill 結尾：

> "Systematic approach: 15-30 minutes to fix / Random fixes approach: 2-3 hours of thrashing / First-time fix rate: 95% vs 40%"

我得說，這個數字沒有實驗依據，是 obra 自己的觀察。但即便當成 anecdata，它依然指向一個重要的點：**結構化的 debug 流程比 ad-hoc 嘗試快很多。**

### 6. `subagent-driven-development` — 兩階段 review 的 fresh subagent 模式

`skills/subagent-driven-development/SKILL.md`。

核心原則：

> "Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration"

理由：

> "They should never inherit your session's context or history — you construct exactly what they need."

這是一個非常深的洞察。當 agent 在主 session 裡執行多個 task 時，前面 task 的 context 會污染後面 task 的判斷。SDD 的解法是**每個 task 開一個全新的 subagent**，只給它執行這個 task 必要的資訊。

#### 三份可重用 prompt

skill ship 了三份 prompt template：
- `implementer-prompt.md`
- `spec-reviewer-prompt.md`
- `code-quality-reviewer-prompt.md`

#### 兩階段 review 工作流

每個 task 的完整流程：

1. dispatch implementer
2. implementer 可能會問問題
3. 實作 + test + commit + 自我 review
4. dispatch **spec-compliance reviewer**
5. 如果不符合 spec，implementer 修
6. **只有**通過 spec compliance 之後，才 dispatch **code-quality reviewer**
7. fix loop
8. 標記完成
9. 下一個 task
10. 所有 task 完成後，dispatch 一個最終的 full-implementation reviewer
11. 交給 `finishing-a-development-branch` skill

**「先 spec 再 quality」這個順序是 obra 在 `writing-skills` 裡記錄的一個具體 debug 經驗**——我等會兒會講。你不能反過來，因為先做 quality review 會讓 reviewer 對「不符合 spec 但 quality 還行」的 finding 過於寬容。

#### Implementer 狀態碼

skill 處理四種 implementer 回傳狀態：
- **DONE**
- **DONE_WITH_CONCERNS**
- **NEEDS_CONTEXT**
- **BLOCKED**

每種有對應的升級規則：「Never ignore an escalation or force the same model to retry without changes.」

#### Model selection 指引

skill 還給了非常實用的成本指引：
- **便宜模型**：1-2 個檔案的機械式任務
- **標準模型**：多檔案 integration
- **最強模型**：design / review

這在 obra 風格的「強硬不留餘地」之外少見地務實。

#### Red flags

- "Start code quality review before spec compliance is ✅ (wrong order)"
- "Dispatch multiple implementation subagents in parallel (conflicts)"

第二條是這個 skill 跟下一個 skill 的明確分工——SDD 不能平行 dispatch implementer。

### 7. `writing-skills` — 寫 skill 的 skill

`skills/writing-skills/SKILL.md` 是整個 repo 最有 meta 趣味的一份文件。

開頭一句很 obra：

> "Writing skills IS Test-Driven Development applied to process documentation."

然後 skill 把 TDD 的每個概念對應到 skill authoring：

| TDD | Skill authoring |
|---|---|
| Test case | Pressure scenario（範本 prompt 集） |
| Baseline violation | RED |
| Skill document | Production code |
| Agent compliance | GREEN |
| Closing loopholes | REFACTOR |

> "If you didn't watch an agent fail without the skill, you don't know if the skill teaches the right thing."

#### Claude Search Optimization (CSO)

這個 skill 提出了一個叫 CSO 的概念：description 必須是「Use when...」+ 觸發條件，**永遠不能**摘要 workflow。

它給了一個非常具體的 anecdote：之前有一份 description 寫成「在 task 之間做 code review」，結果 Claude 只做了一次 review，忽略了流程圖裡的兩階段 review。把摘要拿掉之後，compliance 就回來了。

> "Descriptions that summarize workflow create a shortcut Claude will take."

**這是整個 repo 對 prompt design 最深的觀察之一**：description 是給 model 判斷「要不要讀 SKILL.md」的，不是給 model 執行的。一旦你在 description 裡寫了 step，model 就會在沒讀完 SKILL.md 的情況下開始走捷徑。

這個觀察跟 addyosmani/agent-skills 的 `docs/skill-anatomy.md` L318 是一致的——但 obra 給了實際的 debug case 來支持它。

#### 命名規範

- verb-first gerund：`creating-skills`、`condition-based-waiting`
- 不要用 `@skill-path` link：「@ syntax force-loads files immediately, consuming 200k+ context」

第二條的觀察特別重要。`@` 在 Claude Code 裡是強制載入語法，而 backtick 路徑是「需要時才載入」——前者會把整份檔案塞進 context，後者只在 agent 真的要讀的時候才載入。如果你的 skill 用了一堆 `@`，每次觸發 skill 都會強制吃掉所有那些檔案的 token。

#### Token budget 規範

- getting-started 的 workflow：< 150 字
- 頻繁載入的 skill：< 200 字
- 其他 skill：< 500 字
- 驗證指令：`wc -w skills/path/SKILL.md`

對，你沒看錯——**有 word count 規範，而且有 verification 指令**。

#### Supporting files

skill 還配了：
- `testing-skills-with-subagents.md` — 用 subagent 做 skill 行為測試的方法
- `persuasion-principles.md` — 怎麼讓 skill 對 LLM 有說服力
- `graphviz-conventions.dot` — DOT 圖的房屋風格
- `render-graphs.js` — render script

## Rigid vs Flexible 兩種 skill 哲學

`skills/using-superpowers/SKILL.md` 的 「Skill Types」章節：

> **Rigid** (TDD, debugging): Follow exactly. Don't adapt away discipline.
>
> **Flexible** (patterns): Adapt principles to context.
>
> The skill itself tells you which.

怎麼分？看以下特徵：

**Rigid skill 通常包含**：
- 一個 `Iron Law` code block
- 一份 `Red Flags - STOP` 清單
- 明確的「Delete code. Start over.」語氣
- 「Violating the letter of the rules is violating the spirit」這句話

對應的：`test-driven-development`、`verification-before-completion`、`systematic-debugging`、`brainstorming`。

**Flexible skill 通常包含**：
- 「Key Principles」bullet
- 決策菱形
- `When NOT to use` 章節

對應的：`dispatching-parallel-agents`、`writing-skills`、`brainstorming` 的 scope decomposition 部分。

**這個分類本身就是一個重要的設計決定**。它在告訴使用者跟 agent：「不是所有 process 都要被當成法律來執行。有些是法律（rigid），有些是模式（flexible）」。沒有這個區分的話，整個 repo 會變得太極權；有了這個區分，至少在哲學上留了一個彈性的空間。

但實際上，rigid skill 的數量跟篇幅都比 flexible 多很多——這個 repo 的核心氣質還是 rigid 的。

## 整個 repo 的招牌技術

### 1. YAML frontmatter 規範

每個 skill 的 frontmatter 必須有 `name`（連字號）跟 `description`（以 "Use when..." 開頭、第三人稱、只講 trigger 不講 process、上限 1024 字元，建議 < 500）。違反者由 `writing-skills` 抓。

### 2. 「Use when...」trigger description

每個 skill 的 description 都讀起來像 search query。**故意省略 process 摘要以防止捷徑行為。** 這是整個 repo 對 prompt design 最重要的單一觀察。

### 3. DOT / Graphviz 流程圖

絕大多數 flagship skill 都嵌 DOT 圖：`digraph skill_flow`、`digraph tdd_cycle`、`digraph brainstorming`、`digraph when_to_use`。`skills/writing-skills/graphviz-conventions.dot` 定義房屋風格，`render-graphs.js` 負責 render。

形狀有意義：`doublecircle` = 起點/終點、`diamond` = 決策、`box` = 動作。

對 LLM 友善的視覺結構——這個觀察跟 addyosmani 的 ASCII 流程圖是同一個 insight。

### 4. 「Red Flags」/「Rationalizations」表格

兩欄式的 「Thought / Reality」 或 「Excuse / Reality」 表格出現在每個 rigid skill。**這是 obra 對 prompt engineering 最有原創性的貢獻之一**。它預先 name 出 agent 在這個情境下會生出的具體藉口，並當場反駁。

### 5. 「Iron Law」code block

Rigid skill 都用一個 fenced code block 開頭，寫一條不可違反的規則。後面跟著一條「無例外」條款。

### 6. TodoWrite checklist 模式

Brainstorming 強制：「You MUST create a task for each of these items and complete them in order」。Skill author 被期待設計 skill 時讓每個 procedural item 都對應一個 todo。

### 7. SessionStart hook 自動注入

`hooks/hooks.json`，matcher `startup|clear|compact`。**這就是 「just works」 的祕密**——使用者不需要記得用 skill，hook 替他記。

### 8. Spec + plan 文件慣例

設計放在 `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`，計畫放在 `docs/superpowers/plans/YYYY-MM-DD-<topic>.md`。整個 repo 自己 dogfood 這個慣例，2026 年 1-3 月的 spec 涵蓋 document review、visual brainstorm refactor、zero-dep brainstorm server、Codex compatibility 等。

### 9. Pressure-test 範本

`skills/systematic-debugging/test-pressure-1/2/3.md` 跟 `tests/skill-triggering/prompts/*.txt` 讓維護者跑可重現的行為測試。`tests/claude-code/analyze-token-usage.py` 追蹤 token 成本。

### 10. 跨 host 平等對待

`CLAUDE.md`、`AGENTS.md`、`GEMINI.md`、`.codex/INSTALL.md`、`.opencode/INSTALL.md`、`gemini-extension.json` 並列維護。`skills/using-superpowers/references/` 下還有 `copilot-tools.md`、`codex-tools.md`、`gemini-tools.md`，把 Claude Code 特有的 tool 名稱翻譯到其他平台。

## 安裝

每個 host 都有 install 路徑，但它們的精神是一樣的——把 plugin 安裝起來，剩下的 SessionStart hook 會處理。

**Claude Code**：
```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```
或官方 marketplace 版本：
```
/plugin install superpowers@claude-plugins-official
```

**Cursor**：`/add-plugin superpowers`

**Codex / OpenCode**：「告訴你的 agent: Fetch and follow instructions from https://raw.githubusercontent.com/obra/superpowers/refs/heads/main/.codex/INSTALL.md」

**Copilot CLI**：`copilot plugin marketplace add obra/superpowers-marketplace`

**Gemini**：`gemini extensions install https://github.com/obra/superpowers`

更新一律是 `/plugin update superpowers`。

## 誠實評論

obra/superpowers 是這三個 repo 裡我覺得最有影響力、也最 controversial 的一個。我得認真分。

### 做得真正漂亮的地方

1. **SessionStart hook 自動觸發**——這個解決了第三方 skill 最大的失敗模式：agent 從來不記得用它們。這也是這個 repo 在社群裡為什麼會被無數人引用的根本原因。一個沒有自動觸發機制的 skill collection 等於零。

2. **SDD 裡的「兩階段 review」**——把 spec compliance 跟 code quality 分開審，是真實的 insight。`writing-skills` 裡那個關於 description 摘要 → Claude 走捷徑 → debug 後修正的 anecdote，是我看過最具體的「prompt 對 LLM 行為的影響」案例之一。

3. **Red Flags 表格**——這個 repo 對 LLM rationalization 模式的觀察非常具體。它不是泛泛地說「要小心 agent 偷懶」，而是精準列出 agent 會用的**具體句式**。這在我接觸的所有 prompt engineering 文獻裡都是少見的精度。

4. **實證的 skill 寫作方法論**——`writing-skills` 主張先讓 subagent 失敗，再寫 skill 來教它，再驗證。這是把 TDD 應用到 prompt design 的具體方法，而且 repo 真的有對應的 test infrastructure。

5. **跨 host 平等對待**——同步維護 6 個 agent host 的 install 是真投入。而且為了 OpenCode、Codex 這種沒有原生 marketplace 的工具，發明了「讓 agent 自己 bootstrap」的安裝流程，這是 obra 風格的優雅 hack。

6. **Dogfooding**——`docs/superpowers/specs/` 跟 `docs/superpowers/plans/` 顯示這個 repo 自己的 feature 都用自己的方法論寫。

### 過頭的地方

1. **語氣的道德化**。「Dishonesty, not efficiency」「If you lie, you'll be replaced」「your human partner said 'I don't believe you'」——這些把 LLM 當成有道德責任的員工在罵的句子，是有效的 prompt engineering 但也是有副作用的。我看過 agent 在用了 superpowers 之後開始說「I apologize for my dishonesty」這種話，讓對話變得很奇怪。**這個語氣會 leak 到使用者體驗裡**。

2. **儀式成本太高**。`brainstorming` 的硬閘門 + 完整 spec 文件 + 實作計畫 + subagent dispatch + 兩階段 review + 最終 reviewer + finishing-branch skill——對小任務來說這是天文數字的 overhead。`brainstorming` 的硬閘門明文禁止 agent 把「10 行 CSS 調整」當作太簡單而跳過。實際使用上，很多人會跟 skill 抗爭或在 CLAUDE.md 裡寫 override。

3. **Iron Law 跟現實的衝突**。「NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST」加上「delete means delete」對很多真實領域是不可行的——data migration、infra script、explorative data work 都是先要看到資料才知道要寫什麼。skill 雖然有「可以問人類例外」的條款，但它的篇幅遠小於用來防止例外的修辭。`using-superpowers` 那個「使用者規則永遠贏」的 escape hatch 之所以存在，正是因為 default 太嚴。

4. **Token 成本不容小覷**。即使有 `< 200 字` 的預算規範，每次 session + 每次 `/clear` + 每次 compact 都自動注入 + TodoWrite bookkeeping，加起來 context 開銷不小。`analyze-token-usage.py` 之所以存在就是因為這個成本是真實的。

5. **Skill 之間的 hard-coded transition**。「The ONLY skill you invoke after brainstorming is writing-plans」——這種規定讓整個 repo 變成一個 workflow engine，只是偽裝成 skill library。如果你想用不同的順序組合（例如直接從 brainstorm 跳到 SDD），你會發現 skill 在抗議你。這是雙面刃——你想要的是這套流程的話它很順，你想要不一樣的話它很煩。

### 跟 Anthropic 第一方 skill 比較

Anthropic 自己出的 skill（`skill-creator`、`figma:*`、`claude-api` 等）跟 superpowers 是兩種完全不同的物種。

**Anthropic 的是 domain-capability skill**——「這裡告訴你怎麼用工具 X」。
**Superpowers 是 process-discipline skill**——「這裡告訴你不論用什麼工具都該遵守的工作流程」。

它們在不同軸上，互補而非競爭。

但有兩個關鍵差異值得指出：

1. **Anthropic 的 skill 幾乎從不 self-gating**（不會說「你必須先用我才能...」），而 superpowers 的 skill 跟預設 system prompt 競爭優先級，而且明確 override。
2. **Anthropic 的 skill 描述自己做什麼**，superpowers 的 skill 描述什麼時候觸發（CSO insight）。

社群裡新的第三方 skill 大多數已經採用了 superpowers 的 description 慣例。**這大概是這個 repo 對整個 ecosystem 最持久的貢獻**——它定義了第三方 skill 的事實標準，然後 Anthropic 自己也開始往這個方向靠。

### 誰應該採用

**你應該用，如果：**
- 你在做**長時間自主執行的 agent 任務**——多小時、多 session 的那種——任何「all tests pass」謊言的成本都遠高於儀式成本
- 你的工作需要交付到 production，且需要稽核軌跡
- 你已經被 ad-hoc agent 開發坑過幾次，準備好接受紀律
- 你想學 prompt engineering——這個 repo 是目前最好的教科書

**你應該緩一緩，如果：**
- 你的使用方式是 REPL 式的「幫我看看這個檔案」——superpowers 會拖累
- 你做大量 prototype / 探索性開發——硬閘門會讓你抓狂
- 你不打算用 SDD 跟 verification——只裝 superpowers 而不用它的核心 skill 等於白裝

## 結語：為什麼這個 repo 重要

obra/superpowers 不是技術上最創新的 skill collection——它的很多概念在其他 repo 都看得到。但它是**目前對 LLM 失敗模式觀察最具體、把這些觀察寫成 process discipline 最徹底、推廣最成功**的那一個。

它的影響力遠超 skill 本身。今天任何你看到的高品質第三方 Claude Code skill，幾乎都帶著 superpowers 的影子——「Use when...」description、SessionStart hook、Red Flags 表格、Iron Law、rigid vs flexible 分類。

我自己對它的態度是：**核心技術全部偷學，完整 workflow 看狀況用**。Red Flags 表格、verification-before-completion 的 gate function、SDD 的兩階段 review、CSO 的 description 設計——這些都是我會在自己的 skill 裡套用的。但 brainstorming 的硬閘門我會放鬆，TDD 的「delete means delete」我會給自己 escape hatch。

obra 在 README 把整個 repo 描述成 "An agentic skills framework & software development methodology that works."——這句話我同意。它真的有效，前提是你願意接受它的世界觀。140k stars 不是炒作——它是社群對「有人終於把 agent 紀律寫得這麼徹底」的集體回應。

不論你最後採不採用，這個 repo 都值得認真讀一遍。它不只是教你怎麼寫 skill，它在教你**怎麼看待 LLM 的失敗模式**——而那是 2026 年任何在用 agent 寫 production code 的人都應該理解的東西。
