---
title: "完整解析 Boris Cherny 的 Claude Code Skill 如何改變你的軟體開發方式"
date: 2026-03-28
categories: [AI]
tags: [claude-code, skills, ai-engineering, workflow, parallel-agents]
image:
  path: /assets/img/posts/2026-03-28-boris-cherny-claude-code-skill/banner.jpg
  alt: "42 Tips — One AI Engineering System"
---

> 原文：[The Complete Boris Cherny Claude Code Skill Changes How You Build Software](https://x.com/NainsiDwiv50980/status/2037558089386430578) by **Nainsi Dwivedi** (@NainsiDwiv50980)
> 發佈日期：2026 年 3 月 27 日

---

[@bcherny](https://x.com/bcherny) 並沒有分享 42 個 Claude Code 小技巧。他分享的是一套完整的 AI 驅動開發作業系統。

而且現在⋯⋯它已經是一個可安裝的 skill 了。

以下是完整合集真正揭示的內容（大多數人只看到了 Part 1）：

## Claude Code 技術棧（所有人都漏掉的部分）

大多數開發者只複製了：

- Parallel sessions
- Plan mode
- CLAUDE.md

有用——但不完整。

真正的工作流程只有在 Parts 3–5 串聯起來時才會浮現。

這不是一份清單。這是一個**分層架構**。

Foundation → Team layer → Customization → Isolation → Parallel agents

一旦你看懂了⋯⋯Claude Code 就不再是聊天機器人，而是開始像一個 **AI 工程團隊**一樣運作。

## Layer 1 — 基礎層（Part 1）

這就是當初爆紅的內容。

- 同時執行 5–10 個平行 Claude sessions
- 以 plan mode 開始（Shift+Tab）
- 計畫穩固後開啟 auto-accept
- 每次修正後更新 CLAUDE.md

**結果：**
Claude 不再重複犯錯，開始**累積知識**。

光是這一點就能把 review cycles 從幾天縮短到幾小時。

但這只是第一步。

## Layer 2 — 團隊記憶（Part 2）

現在 Claude 變成了共享基礎設施。

- 共享的 CLAUDE.md
- 團隊 hooks
- 簽入的 permissions
- 標準化的 agents

Claude 不再是個人工具，而是成為**團隊認知**。

每位工程師都能從每一個只需修復一次的錯誤中受益。

## Layer 3 — 客製化層（Part 3）

這是事情開始改變的地方。

大多數開發者忽略了這個部分。這是個錯誤。

**Output styles = 認知模式**

- Explanatory → Claude 解釋推理過程
- Learning → Claude 邊寫程式邊教學
- Minimal → Claude 靜默執行

這改變的是 Claude **如何思考**，而不只是如何書寫。

然後是 custom agents：

- `backend-reviewer`
- `migration-guard`
- `code-simplifier`
- `verify-app`

Claude 不再是單一 AI，而是成為**專業化的子代理人**。

## Layer 4 — 原生 Worktree 平行化（Part 4）

這解鎖了真正的 AI 平行化。

每個 Claude agent：

- 擁有自己的 git worktree
- 擁有自己的任務
- 擁有自己的測試
- 擁有自己的 PR

範例：

> 「將同步 IO 遷移為非同步
> 啟動 10 個 agents
> 每個都測試並開 PR」

這不是 prompting。這是 **AI orchestration**。

## Layer 5 — 複合操作（Part 5）

現在是魔法指令：

### /simplify

自動執行平行的 code reviewers。

捕捉：

- 重複邏輯
- 巢狀條件式
- 不良查詢
- 重用機會

在人類 review 開始之前就完成。

### /batch

互動式規劃 → 數十個 agents → 平行的 PRs

範例：

```
/batch migrate Solid to React
```

多個 agents、多個 PRs、平行執行。

這是**規模化的 AI 重構**。

## 真正的洞見

Boris 分享的不是技巧，而是一個**技術棧**：

1. **Parallelism** — 平行化
2. **Memory** — 記憶
3. **Customization** — 客製化
4. **Isolation** — 隔離
5. **Orchestration** — 編排

每一層都解鎖下一層。

如果你跳過某些層——它會崩壞。
如果你按順序來——它會**複利成長**。

## 為什麼這很重要

Claude Code 的目的不是取代開發者。

它的目的是：

- 執行遷移
- 處理重構
- 審查結構
- 平行化執行
- 累積知識

**你專注於架構。Claude 處理剩下的。**

## Skill 安裝

所有 42 個技巧現在都可以安裝：

```bash
mkdir -p ~/.claude/skills/boris
curl -L -o ~/.claude/skills/boris/SKILL.md \
  https://howborisusesclaudecode.com/api/install
```

然後執行：

```
/skills boris
```

Claude 會載入完整的工作流程。

這不再是 prompting。這是 **AI-native development**。

而 Boris Cherny 悄悄展示了這份藍圖。
