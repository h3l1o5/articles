---
title: "Claude Skills 終極指南（2026 年 3 月）"
date: 2026-03-25
categories: [AI]
tags: [claude, skills, productivity, anthropic, prompt-engineering]
image:
  path: /assets/img/posts/2026-03-25-claude-skills-ultimate-guide/banner.jpg
  alt: "Claude Skills Ultimate Guide"
---

> 原文：[Claude Skills: Ultimate Guide (March 2026)](https://x.com/aiedge_/status/2036815449225298369) by **AI Edge** (@aiedge\_)
> 發佈日期：2026 年 3 月 25 日

---

我這個月在 𝕏 上發了超過 200 篇文章。如果你只打算從我三月份的所有內容中實作一件事，就是這個。

Claude Skills 是 2026 年最大的生產力突破，也是整個 Claude 生態系中最划算的投資。

設定一次，之後每天使用 Claude 時就會自動累積複利。

過去三個月以來，我在內容製作和公司營運的每個主要工作流程中都使用了 Skills。我每天毫無例外地使用它們，而且我已經要求公司每個部門都必須建立自己的 Skills。

這篇指南涵蓋了一切：它們是什麼、怎麼建、怎麼優化、剛推出的新功能、實際工作流程，以及更多。這就是我剛開始接觸 Claude Skills 時希望自己擁有的那份指南，開始吧。

## Claude Skills 到底是什麼？

一句話說完：Claude Skills 是預先載入的指令集，以 markdown 檔案形式儲存。

在任何 Claude 對話或 project 中，你都可以呼叫一個 Skill，Claude 就會立即遵循你建立的指令。不用重新解釋、不用重新提示，也不用每次開新對話就複製貼上一堆 context。

這樣想：現在每次你開一個新的 Claude 對話，你都是從零開始。Claude 對你一無所知——不知道你的風格、你的標準、你喜歡怎麼做事。Skill 改變了這一切。它把 Claude 需要知道的一切打包成一個可重複使用的檔案，隨時可以呼叫。

舉個實際例子：想像一個 Brand Voice Skill，裡面包含了你公司的一切（語氣、風格、受眾、核心訊息、好文案的範例）。不用每次都重新解釋這些，你只要說：

「嘿 Claude，用我的 Brand Voice Skill 寫一篇關於 [X] 的 LinkedIn 貼文。」

可能性是無窮無盡的。寫作 Skill、研究 Skill、文法檢查器、外展模板 Skill。任何需要 context 和指令的重複性任務，都適合做成 Skill。

## 如何建立和使用 Skills

建立一個 Skill 只需要不到 30 分鐘，而且你只需要做一次，就能打造一個永久的生產力資產。建立 Skills 的流程再簡單不過了。

**第一步：啟用 Skill-Creator**

前往 Customize → Skills，確認你已經啟用了「Skill-Creator」。

這是一個用來建立 Skills 的 Skill（由 Anthropic 提供）。

**第二步：提示 Claude**

啟用後，你可以告訴 Claude：「我想建立一個用於 [工作流程] 的 Skill；幫我建立它。」

以下是從零開始建立第一個 Skill 的模板 prompt：

```
You are building a Claude Skill — a reusable instruction set in markdown format.

My Skill is for: [describe the task — e.g. writing X articles, checking grammar, researching topics]

Here is the context Claude needs to do this well:
- My name / brand: [name]
- My audience: [who you are writing for]
- My tone and voice: [how you want Claude to sound]
- My standards: [what good output looks like]
- What to avoid: [common mistakes, words, formats to never use]

Using this context, write a complete SKILL.md file that:
1. Opens with a one-line description of what the Skill does
2. Defines the role Claude plays when this Skill is active
3. Lists the exact rules Claude must follow
4. Includes at least one example of a great output
5. Ends with a quality checklist Claude runs before responding

Format it as a proper markdown file I can save and upload directly to a Claude Project.
```

**第三步：儲存 Skills**

Claude 建完 Skill 之後，會輸出一個 zip 和/或 markdown 檔案。點擊「Copy to Skills」就能把新 Skill 儲存到你的 Anthropic 帳號。

我也喜歡把 Skills 下載到我的電腦（放在專用資料夾裡），但這是選做的。

回到 Customize → Skills → My Skills，確認你的新 Skill 已經存在且已啟用。

**第四步：使用你的 Skill**

現在，你只需要提示 Claude 使用你的 Skill：「use my [x] Skill for [x]。」

範例 prompt：

```
Use my "Grammar Checker" Skill to proofread this text: [insert text]
```

就這樣！

## 如何建立優秀的 Skills

任何人都能建立 Claude Skills，但很少有人能建立真正好用的 Skills。

以下是我的建議（基於數月的經驗）：

1. **Reverse prompting**：告訴 Claude：「我想建立 [x] Skill，問我 10-50 個能幫你建立它的問題。」

2. **Reverse building**：告訴 Claude：「根據你對我的了解，什麼 Skills 對我會有幫助？」

3. **Context**：你提供的 context 越多越好。丟 PDF、附 Docs——你有的東西全部丟進去。

4. **Chats as context**：把你現有的對話當作 context 使用（也就是「把這個對話裡的所有內容變成一個 Skill」）。我的「Grammar Checker」Skill 就是這樣做的，它立刻就知道我想要什麼，因為我們聊了好幾個月。

5. **Iterations**：把 Claude 的第一版 Skill 輸出當作草稿就好。讀完整個檔案，寫下你想改的地方，然後提示 Claude 進行修改。我通常要建立 3 個版本才能得到 100% 滿意的結果。

6. **Include a real example**：提升 Skill 輸出品質最快的方法，就是貼入一個你認為完美的範例。Skill 檔案裡的一個強範例，勝過十條指令。

7. **QC**：每個 Skill 都以簡短的 checklist 結尾。三到五個問題讓 Claude 在產出前自問：語氣對嗎？有沒有用到禁用詞？格式對嗎？

8. **Manual review**：雖然聽起來很麻煩，但一定要手動審查 Skills。是的，這需要幾分鐘的人工，但換來的是一個從此為你節省時間的 Skill。

## Claude Skills 2.0

Anthropic 在三月推出了 Skills 框架的重大更新。

有三個值得關注的重大更新：

### 1. 內建 Evals 和測試

在這次更新之前，沒有可靠的方法知道你的 Skill 是否真的有效。你建一個、試幾次、覺得還行就繼續用。如果後來出了問題（例如模型更新後），你往往要等到產出結果不對才會發現。

Skills 2.0 解決了這個問題。在把 Skill 儲存到帳號之前，你就已經知道輸出會是什麼樣子。你寫幾個實際的 prompt（就是你平常會要 Claude 做的那種事），Claude 會跑兩次：一次載入你的 Skill，一次不載入。兩個輸出都會根據你定義的 assertion 評分，然後顯示在一個 viewer 裡讓你檢視實際輸出並留下回饋。

### 2. A/B Testing

A/B testing 讓你可以用相同的 prompt 比較同一個 Skill 的兩個版本。改了 Brand Voice Skill，想知道輸出是否真的改善了？現在你可以跑 A/B test。

### 3. Trigger Optimization

一個在錯誤時機觸發（或根本不觸發）的 Skill，無論裡面的指令寫得多好，都是個壞掉的 Skill。Skills 2.0 包含了一個自動化流程，會改寫和測試不同版本的 Skill description，直到找到一個能可靠觸發的版本。這解決了你說「嘿，用我的 [x] Skill...」但它就是不觸發的問題。

要使用這些新功能：開一個載入了你的 Skill 的 Claude 對話，告訴 Claude 你想做什麼。範例：

- "Run an eval on this Skill."
- "A/B test these two versions."
- "Optimize my trigger description."

## 實際工作流程和 Mega Prompts

現在你知道如何建立和優化 Skill 了，這裡是一些真實的工作流程，讓你看看實際上能做到什麼。

### 1. Brand Voice — 用於建立語氣/風格/寫作風格

建立一個包含你所有品牌資訊的 Skill：語氣 DNA（正式程度、句型風格、人稱視角、用語禁忌、幽默類型）、排版規則、內容範圍、好範例、以及品質標準。

### 2. PDF Generator — 把任何文字轉換成排版良好的 PDF

建立一個 SKILL.md，讓它接收任何文字並產出乾淨、可讀的 PDF。使用 reportlab 或 fpdf2，處理基本排版（段落、換行、基本標題），自動儲存到輸出目錄。不搞花俏的樣式，只要乾淨實用。

### 3. Document Summarizer — 幾秒鐘內摘要任何文字

建立一個文件摘要 Skill。設定預設格式（bullet points / 段落 / 執行摘要）、預設長度（短 / 中 / 長）、必須擷取的內容（重點、行動項目、結論）、以及要跳過的內容（範例、次要細節）。

### 其他值得建立的工作流程

- **ELI5**：一個用簡單語言解釋複雜主題的 Skill（只要貼入主題/文章/文字然後觸發 Skill）。
- **Job Application Skill**：了解你的履歷、經驗、以及你喜歡怎麼定位自己。即時客製化 cover letter。
- **Learning Skill**：你最佳的學習方式、什麼樣的類比對你有效、以及你目前在某個主題上的知識水平。
- **Fitness and Health Skill**：你的目標、限制和偏好。

## Skills 相關工具

幫你建立 Claude Skills 的工具：

1. **Claude Skills Docs** — Anthropic 的官方文件
2. **SkillsMSP** — 一個擁有超過 500,000 個 Claude Skills 的市集，可直接下載（*某些檔案可能有風險——只從已驗證/可靠的創作者下載*）
3. **Awesome Claude Skills** — 精選的實用 Claude Skills 清單
4. **The Complete Guide to Claude Skills (by Anthropic)** — Anthropic 的完整指南 PDF

## 結語

Claude Skills 絕對是我最喜歡的 Anthropic 功能。我的團隊和我對建立和部署它們已經到了著迷的程度——它確實改變了我們的工作方式。

記住：如果你只打算從我三月份的所有內容中實作一件事，就是這個。

我的建議：現在就讓 Claude 用 reverse-prompt 的方式，幫你想出一個實用的 Skill 來建立。

我在這裡介紹的所有內容都來自真實的實戰經驗——我已經在我三家公司的每個主要工作流程中運行 Skills 好幾個月了。如果這是你想在 feed 上看到的內容風格，記得追蹤 [@aiedge\_](https://x.com/aiedge_)。我每週發佈三篇文章，涵蓋 AI 領域最熱門的話題。
