---
title: "解剖 .claude/ 資料夾"
date: 2026-03-21
categories: [DevTools]
tags: [claude-code, cli, agent, configuration]
image:
  path: /assets/img/posts/2026-03-21-anatomy-of-the-claude-folder/01-header.jpg
  alt: "Anatomy of the .claude/ folder"
---

> 原文：[Anatomy of the .claude/ folder](https://x.com/akshay_pachaar/status/2035341800739877091) by **Akshay** (@akshay_pachaar)
> 發佈日期：2026 年 3 月 21 日

---

一份完整指南，涵蓋 CLAUDE.md、自訂指令、skills、agents 與權限設定，以及如何正確配置它們。

大多數 Claude Code 使用者把 .claude 資料夾當成一個黑盒子。他們知道它存在，也看過它出現在專案根目錄，但從來沒有打開過，更別說理解裡面每個檔案的作用了。

這是一個被忽略的機會。

.claude 資料夾是控制 Claude 在你的專案中如何行為的指揮中心。它保存了你的指示、自訂指令、權限規則，甚至 Claude 跨 session 的記憶。一旦你了解了什麼檔案放在哪裡、為什麼這樣放，你就能讓 Claude Code 完全按照團隊的需求來運作。

這篇指南將逐一走過整個資料夾的結構，從你每天都會用到的檔案，到那些設定一次就可以忘記的檔案。

# 兩個資料夾，不是一個

在深入之前，有一件事值得先說清楚：實際上有兩個 .claude 目錄，不是一個。

第一個在你的專案內，第二個在你的家目錄：

![兩個資料夾](/articles/assets/img/posts/2026-03-21-anatomy-of-the-claude-folder/02-two-folders.jpg)

專案層級的資料夾存放團隊配置。你把它提交到 git，團隊中的每個人都能得到相同的規則、相同的自訂指令、相同的權限策略。

全域的 `~/.claude/` 資料夾則存放你的個人偏好和本機狀態，像是 session 歷史紀錄和自動記憶。

# CLAUDE.md：Claude 的使用手冊

這是整個系統中最重要的檔案。當你啟動一個 Claude Code session 時，它讀取的第一個東西就是 CLAUDE.md。它會被直接載入系統提示詞，並在整個對話過程中持續生效。

簡單來說：**你在 CLAUDE.md 裡寫什麼，Claude 就會照做。**

如果你告訴 Claude 永遠在實作之前先寫測試，它會照做。如果你說「不要用 console.log 來處理錯誤，永遠使用自訂的 logger 模組」，它每次都會遵守。

在專案根目錄放一個 CLAUDE.md 是最常見的配置方式。但你也可以在 `~/.claude/CLAUDE.md` 放一個全域版本，適用於所有專案；甚至可以在子目錄裡放一個，針對特定資料夾設定規則。Claude 會讀取所有的 CLAUDE.md 並合併它們。

**CLAUDE.md 裡實際該寫什麼**

大多數人不是寫太多就是寫太少。以下是有效的寫法。

## 該寫的：

- 建置、測試和 lint 指令（npm run test、make build 等）
- 關鍵的架構決策（「我們使用 Turborepo 的 monorepo」）
- 不明顯的陷阱（「TypeScript 開了 strict mode，未使用的變數會報錯」）
- import 慣例、命名模式、錯誤處理風格
- 主要模組的檔案和資料夾結構

## 不該寫的：

- 應該放在 linter 或 formatter 設定裡的東西
- 你已經可以連結到的完整文件
- 解釋理論的長篇段落

把 CLAUDE.md 控制在 200 行以內。超過這個長度會開始佔用過多上下文，Claude 對指示的遵循度實際上會下降。

這是一個精簡但有效的範例：

```plaintext
# Project: Acme API

## Commands
npm run dev          # Start dev server
npm run test         # Run tests (Jest)
npm run lint         # ESLint + Prettier check
npm run build        # Production build

## Architecture
- Express REST API, Node 20
- PostgreSQL via Prisma ORM
- All handlers live in src/handlers/
- Shared types in src/types/

## Conventions
- Use zod for request validation in every handler
- Return shape is always { data, error }
- Never expose stack traces to the client
- Use the logger module, not console.log

## Watch out for
- Tests use a real local DB, not mocks. Run `npm run db:test:reset` first
- Strict TypeScript: no unused imports, ever
```

大約 20 行。它提供了 Claude 在這個程式碼庫中高效工作所需的一切資訊，不需要反覆確認。

# CLAUDE.local.md：個人覆寫設定

有時候你有一些偏好是只屬於你個人的，而非整個團隊。也許你偏好不同的測試工具，或者你希望 Claude 總是用特定的模式來開啟檔案。

在專案根目錄建立 CLAUDE.local.md。Claude 會和主要的 CLAUDE.md 一起讀取它，而且它會自動被 gitignore，你的個人調整永遠不會進入 repo。

![CLAUDE.local.md](/articles/assets/img/posts/2026-03-21-anatomy-of-the-claude-folder/03-claude-local-md.jpg)

# rules/ 資料夾：可擴展的模組化指示

CLAUDE.md 在單一專案中運作良好。但當團隊規模成長，你最終會得到一個 300 行、沒人維護、所有人都忽略的 CLAUDE.md。

rules/ 資料夾就是為此而生的。

**`.claude/rules/` 裡的每個 markdown 檔案都會自動和你的 CLAUDE.md 一起載入。** 你不再需要一個龐大的檔案，而是按關注點拆分指示：

```plaintext
.claude/rules/
├── code-style.md
├── testing.md
├── api-conventions.md
└── security.md
```

每個檔案聚焦且容易更新。負責 API 規範的團隊成員編輯 api-conventions.md，負責測試標準的人編輯 testing.md，誰也不會踩到誰。

真正強大的地方在於**路徑作用域規則**。在規則檔案中加上 YAML frontmatter，它就只會在 Claude 操作符合條件的檔案時才啟用：

```markdown
---
paths:
  - "src/api/**/*.ts"
  - "src/handlers/**/*.ts"
---
# API Design Rules

- All handlers return { data, error } shape
- Use zod for request body validation
- Never expose internal error details to clients
```

當 Claude 在編輯一個 React 元件時，不會載入這個檔案。只有在它操作 src/api/ 或 src/handlers/ 裡的程式碼時才會載入。沒有 paths 欄位的規則則會在每個 session 中無條件載入。

當你的 CLAUDE.md 開始顯得擁擠時，這就是正確的做法。

# commands/ 資料夾：你的自訂斜線指令

Claude Code 內建了 `/help` 和 `/compact` 等斜線指令。`commands/` 資料夾讓你可以新增自己的。

**你放進 .claude/commands/ 的每個 markdown 檔案都會變成一個斜線指令。**

一個名為 `review.md` 的檔案會建立 `/project:review`。一個名為 fix-issue.md 的檔案會建立 `/project:fix-issue`。檔名就是指令名稱。

![Commands 資料夾](/articles/assets/img/posts/2026-03-21-anatomy-of-the-claude-folder/04-commands-folder.jpg)

這是一個簡單的範例。建立 `.claude/commands/review.md`：

```markdown
---
description: Review the current branch diff for issues before merging
---
## Changes to Review

!`git diff --name-only main...HEAD`

## Detailed Diff

!`git diff main...HEAD`

Review the above changes for:
1. Code quality issues
2. Security vulnerabilities
3. Missing test coverage
4. Performance concerns

Give specific, actionable feedback per file.
```

在 Claude Code 中執行 `/project:review`，它會自動在 Claude 看到提示之前注入真實的 git diff。`!` 反引號語法會執行 shell 指令並嵌入輸出結果。這就是為什麼這些指令是真正有用的工具，而不只是存好的文字。

## 傳遞參數給指令

使用 `$ARGUMENTS` 來傳遞指令名稱之後的文字：

```markdown
---
description: Investigate and fix a GitHub issue
argument-hint: [issue-number]
---
Look at issue #$ARGUMENTS in this repo.

!`gh issue view $ARGUMENTS`

Understand the bug, trace it to the root cause, fix it, and write a
test that would have caught it.
```

執行 `/project:fix-issue 234` 就會直接把第 234 號 issue 的內容注入到提示中。

## 個人指令 vs. 專案指令

`.claude/commands/` 裡的專案指令會被提交並與團隊共享。如果你想要在所有專案中都能使用的指令，把它們放在 `~/.claude/commands/`。這些會以 `/user:command-name` 的形式出現。

實用的個人指令範例：每日站會助手、依照你的慣例生成 commit message 的指令，或是快速安全掃描。

# skills/ 資料夾：隨需調用的可重複使用工作流程

你現在已經知道 commands 是怎麼運作的了。Skills 表面上看起來相似，但觸發機制有根本性的不同。在繼續之前，先來看看它們的區別：

![Skills vs Commands](/articles/assets/img/posts/2026-03-21-anatomy-of-the-claude-folder/05-skills-vs-commands.jpg)

**Skills 是 Claude 能夠自行調用的工作流程**，不需要你輸入斜線指令，當任務符合 skill 的描述時就會觸發。Commands 等著你來呼叫。Skills 則是監聽對話，在適當的時機主動出手。

每個 skill 都住在自己的子目錄中，有一個 SKILL.md 檔案：

```markdown
.claude/skills/
├── security-review/
│   ├── SKILL.md
│   └── DETAILED_GUIDE.md
└── deploy/
    ├── SKILL.md
    └── templates/
        └── release-notes.md
```

SKILL.md 使用 YAML frontmatter 來描述何時應該使用它：

```markdown
---
name: security-review
description: Comprehensive security audit. Use when reviewing code for
  vulnerabilities, before deployments, or when the user mentions security.
allowed-tools: Read, Grep, Glob
---
Analyze the codebase for security vulnerabilities:

1. SQL injection and XSS risks
2. Exposed credentials or secrets
3. Insecure configurations
4. Authentication and authorization gaps

Report findings with severity ratings and specific remediation steps.
Reference @DETAILED_GUIDE.md for our security standards.
```

當你說「幫我檢查這個 PR 的安全問題」，Claude 讀取 description，辨認出它符合，就會自動調用這個 skill。你也可以用 `/security-review` 明確呼叫它。

與 commands 的關鍵差異在於：skills 可以在旁邊打包附帶的支援檔案。上面的 @DETAILED_GUIDE.md 引用會拉進一份就放在 SKILL.md 旁邊的詳細文件。Commands 是單一檔案，Skills 是套件。

個人 skills 放在 `~/.claude/skills/`，在所有專案中都能使用。

# agents/ 資料夾：專門化的子 agent 角色

當一個任務複雜到需要一個專門的專家來處理時，你可以在 .claude/agents/ 中定義子 agent 角色。每個 agent 是一個 markdown 檔案，有自己的系統提示詞、工具存取權限和模型偏好：

```plaintext
.claude/agents/
├── code-reviewer.md
└── security-auditor.md
```

以下是 code-reviewer.md 的樣子：

```markdown
---
name: code-reviewer
description: Expert code reviewer. Use PROACTIVELY when reviewing PRs,
  checking for bugs, or validating implementations before merging.
model: sonnet
tools: Read, Grep, Glob
---
You are a senior code reviewer with a focus on correctness and maintainability.

When reviewing code:
- Flag bugs, not just style issues
- Suggest specific fixes, not vague improvements
- Check for edge cases and error handling gaps
- Note performance concerns only when they matter at scale
```

當 Claude 需要執行程式碼審查時，它會在獨立的上下文窗口中啟動這個 agent。Agent 完成工作後，壓縮結果並回報。你的主要 session 不會被數千個 token 的中間探索過程所佔據。

tools 欄位限制了 agent 能做的事情。安全稽核員只需要 Read、Grep 和 Glob，它不需要寫入檔案。這個限制是刻意的，值得明確設定。

model 欄位讓你可以對聚焦型任務使用更便宜、更快的模型。Haiku 能勝任大多數唯讀探索工作。把 Sonnet 和 Opus 留給真正需要它們的任務。

個人 agents 放在 `~/.claude/agents/`，在所有專案中都能使用。

![Agents 資料夾](/articles/assets/img/posts/2026-03-21-anatomy-of-the-claude-folder/06-agents-folder.jpg)

# settings.json：權限與專案配置

`.claude/` 裡的 `settings.json` 檔案控制 Claude 能做和不能做什麼。你在這裡定義 Claude 可以執行哪些工具、可以讀取哪些檔案，以及執行某些指令前是否需要先徵求同意。

完整的檔案長這樣：

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Read",
      "Write",
      "Edit"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(curl *)",
      "Read(./.env)",
      "Read(./.env.*)"
    ]
  }
}
```

## 每個部分的作用

**`$schema` 行**在 VS Code 或 Cursor 中啟用自動完成和行內驗證。務必加上它。

**allow 清單**包含 Claude 可以不經確認直接執行的指令。對大多數專案來說，一個好的 allow 清單涵蓋：
- Bash(npm run \*) 或 Bash(make \*)，讓 Claude 可以自由執行你的腳本
- Bash(git \*)，用於唯讀的 git 指令
- Read、Write、Edit、Glob、Grep，用於檔案操作

**deny 清單**包含被完全封鎖的指令，無論如何都不能執行。一個合理的 deny 清單會封鎖：
- 破壞性的 shell 指令，如 rm -rf
- 直接的網路指令，如 curl
- 敏感檔案，如 .env 和 secrets/ 下的所有內容

如果某個操作不在任何一個清單中，Claude 會在執行前先徵求同意。這個中間地帶是刻意設計的。它提供了一道安全網，而不需要你事先預測每一個可能的指令。

## settings.local.json：個人覆寫設定

和 CLAUDE.local.md 同樣的概念。建立 `.claude/settings.local.json` 來放你不想提交的權限變更。它會自動被 gitignore。

# 全域 ~/.claude/ 資料夾

你不常直接操作這個資料夾，但了解裡面有什麼是有用的。

**~/.claude/CLAUDE.md** 會在每一個 Claude Code session 中載入，橫跨所有專案。這是放你個人程式設計原則、偏好風格，或任何你希望 Claude 無論在哪個 repo 都記住的事情的好地方。

**~/.claude/projects/** 儲存每個專案的 session 記錄和自動記憶。Claude Code 在工作時會自動為自己記筆記：它發現的指令、觀察到的模式、架構洞察。這些會跨 session 保留。你可以用 /memory 來瀏覽和編輯它們。

**~/.claude/commands/** 和 **~/.claude/skills/** 存放在所有專案中都可使用的個人指令和 skills。

通常你不需要手動管理這些。但知道它們的存在很有幫助——當 Claude 似乎「記得」你從未告訴過它的事情，或者當你想清除某個專案的自動記憶重新開始時。

## 全貌

以下是所有東西如何組合在一起的：

```plaintext
your-project/
├── CLAUDE.md                  # 團隊指示（已提交）
├── CLAUDE.local.md            # 你的個人覆寫（已 gitignore）
│
└── .claude/
    ├── settings.json          # 權限 + 配置（已提交）
    ├── settings.local.json    # 個人權限覆寫（已 gitignore）
    │
    ├── commands/              # 自訂斜線指令
    │   ├── review.md          # → /project:review
    │   ├── fix-issue.md       # → /project:fix-issue
    │   └── deploy.md          # → /project:deploy
    │
    ├── rules/                 # 模組化指示檔案
    │   ├── code-style.md
    │   ├── testing.md
    │   └── api-conventions.md
    │
    ├── skills/                # 自動調用的工作流程
    │   ├── security-review/
    │   │   └── SKILL.md
    │   └── deploy/
    │       └── SKILL.md
    │
    └── agents/                # 專門化的子 agent 角色
        ├── code-reviewer.md
        └── security-auditor.md

~/.claude/
├── CLAUDE.md                  # 你的全域指示
├── settings.json              # 你的全域設定
├── commands/                  # 你的個人指令（所有專案）
├── skills/                    # 你的個人 skills（所有專案）
├── agents/                    # 你的個人 agents（所有專案）
└── projects/                  # Session 歷史紀錄 + 自動記憶
```

# 實際上手的設定步驟

如果你從零開始，以下是一個行之有效的漸進式做法。

**步驟 1.** 在 Claude Code 中執行 /init。它會讀取你的專案並生成一份起始的 CLAUDE.md。把它精簡到只剩必要內容。

**步驟 2.** 加入 .claude/settings.json，設定適合你技術棧的 allow/deny 規則。至少要允許你的執行指令，並禁止讀取 .env。

**步驟 3.** 為你最常執行的工作流程建立一到兩個 commands。程式碼審查和 issue 修復是很好的起點。

**步驟 4.** 隨著專案成長，當 CLAUDE.md 變得擁擠時，開始把指示拆分到 .claude/rules/ 檔案中。在合理的地方用路徑來界定作用範圍。

**步驟 5.** 在 ~/.claude/CLAUDE.md 加入你的個人偏好。這可能是像「永遠在實作之前先寫型別定義」或「偏好函數式模式而非 class-based」之類的內容。

以上就是 95% 專案所需要的全部了。Skills 和 agents 是在你有值得打包的複雜重複性工作流程時才登場的。

## 核心洞察

.claude 資料夾本質上是一套協定，用來告訴 Claude 你是誰、你的專案做什麼、以及它應該遵守哪些規則。你定義得越清楚，花在糾正 Claude 上的時間就越少，它花在做有用工作上的時間就越多。

**CLAUDE.md 是你槓桿效益最高的檔案。** 先把它搞定，其他一切都是優化。

從小處開始，邊做邊改進，把它當作專案中的基礎設施來對待：一旦設定妥當，它每天都在為你產生回報。
