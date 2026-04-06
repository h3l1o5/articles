---
title: "不再撞上 Claude 用量限制——我改變的 10 件事"
date: 2026-04-06
categories: [AI]
tags: [claude, token-optimization, productivity, tips]
image:
  path: /assets/img/posts/2026-04-06-claude-usage-limits-10-tips/banner.jpg
  alt: "Free Claude Tokens: 10 Habits That Pay For Themselves"
---

> 原文：[I stopped hitting Claude's usage limits - 10 things I changed](https://x.com/0x_kaize/status/2038286026284667239) by **kaize** (@0x_kaize)
> 發佈日期：2026 年 3 月 30 日

---

大多數人都怪 Claude 限制太嚴格。我也曾經這樣怪過。

但最近我意識到一件事：**Claude 計算的不是訊息數量，而是 tokens。** 你只需要聰明地使用 tokens 就好，但不是每個人都知道該怎麼做，結果白白浪費了大量 tokens 和金錢。

我深入研究了這個問題，整理出一份最佳習慣清單，能幫你省下大量 tokens。

## 1. 修改你的 prompt，不要發後續訊息

當 Claude 沒有正確理解你的意思時，你可能會想發送：

1. 「不是，我的意思是 [你的訊息]」
2. 「唉，這不是我要的 [你的訊息]」

**不要這樣做！** 每一則後續訊息都會被加入對話歷史。Claude 每一輪都會重新讀取**全部**歷史紀錄——把 tokens 燒在根本沒幫助的 context 上。

每則訊息的 token 成本 = 所有之前的訊息 + 你的新訊息。

總計 = S × N(N+1) / 2（S = 每次交流的平均 tokens，N = 訊息數量）。

以每次交流約 500 tokens 來算：

- 5 則訊息：7,500 tokens
- 10 則訊息：27,500 tokens
- 20 則訊息：105,000 tokens
- 30 則訊息：232,000 tokens

**第 30 則訊息的成本是第 1 則的 31 倍。**

正確做法：點擊原始訊息的「Edit」→ 修改內容 → 重新產生。舊的交流會被替換，而不是堆疊上去。修正 prompt，而不是餵養歷史紀錄。

![Token cost growth chart](/assets/img/posts/2026-04-06-claude-usage-limits-10-tips/token-cost-chart.jpg)

## 2. 每 15–20 則訊息就開新對話

上一節我展示了 token 成本如何隨每則訊息增長。理想情況下，你應該每 15–20 則訊息就開一個新對話。

想像一下超過 100 則訊息的對話。以每次交流約 500 tokens 計算，那就是超過 **250 萬 tokens** 被燒掉——其中大部分只是在重新讀取舊的歷史紀錄。

有一位開發者追蹤了自己的用量，發現 **98.5% 的 tokens 花在重新讀取歷史紀錄上**，只有 1.5% 真正用在產出結果。

![Aniket Parihar's LinkedIn post](/assets/img/posts/2026-04-06-claude-usage-limits-10-tips/linkedin-post.jpg)

當對話變長時 → 請 Claude 總結所有內容 → 複製 → 開新對話 → 貼上作為第一則訊息。

## 3. 把問題整合成一則訊息

很多人認為把問題拆成多則訊息會得到更好的結果。但幾乎每次，事實恰好相反。

三個獨立的 prompt = 三次 context 載入。一個包含三個任務的 prompt = 一次 context 載入。

你省了兩次 tokens：更少的 context 重新載入，而且離用量上限更遠。

不要這樣做：

- 「幫我摘要這篇文章」
- 「現在列出重點」
- 「現在建議一個標題」

改成：「幫我摘要這篇文章、列出重點、並建議一個標題。」

額外好處：答案通常更好，因為 Claude 能立刻看到完整的全貌。

**三個問題。一則 Prompt。永遠如此！**

## 4. 把常用檔案上傳到 Projects

如果你在多個對話中上傳同一份 PDF，Claude 每次都會重新 tokenize 那份文件。

改用 Projects 功能。上傳檔案一次 → 它會被快取。該 project 內的每個新對話都會參照它，不會再消耗 tokens。

快取的 project 內容在你重複存取時不會計入用量。如果你經常處理合約、摘要、風格指南或任何長文件——光這一招就能大幅降低你的 token 開銷。

## 5. 設定 Memory 與 User Preferences

每一個沒有儲存 context 的新對話都會浪費 3–5 則訊息在設定上：「我是行銷人員，我用輕鬆的風格寫作，我偏好短段落……」

你可能看過很多人每次 prompt 都以「Act as a...」開頭——那就是重複燒掉 tokens。

Claude 可以永久記住這些。前往「Settings」→「Memory and User Settings」，一次儲存你的角色、溝通風格和設定。Claude 會自動套用到每一個新對話。

## 6. 關掉你沒在用的功能

Web search、connectors 和「Explore」模式——這些都會在每個回應中增加 tokens，即使你不需要它們。

在寫自己的內容？關掉「Search and Tools」功能。

「Advanced Thinking」功能也會消耗 tokens。預設保持關閉，只在第一次嘗試結果不滿意時才開啟。

**原則：如果你不是有意開啟的功能，就關掉它。**

## 7. 簡單任務用 Haiku

文法檢查、腦力激盪、格式化、快速翻譯、簡短回答——Haiku 處理這些的成本遠低於 Sonnet 或 Opus。

選擇正確的模型是你每天做的最重要決定。

Haiku 處理草稿和簡單任務 → 為真正需要強大模型的任務省下 50–70% 的預算。

心智模型：

- **Haiku** → 快速任務，低成本
- **Sonnet** → 正式工作，中等成本
- **Opus** → 深度思考，高成本

**簡單任務不需要強大的模型！**

## 8. 把工作分散到一整天

Claude 系統使用**滾動式 5 小時窗口**。它不會在午夜重置——你的限額是逐漸遞減的。

早上 9 點發的訊息到下午 2 點就不再計入了。如果你在一個早上的工作階段就用完整個限額，你一天中大部分的限額都會被浪費。

把一天分成 2–3 個工作階段：早上、下午、晚上。等你回來時，之前的用量已經不再計入，你又有新的限額了。

![Wow bro that's crazy](/assets/img/posts/2026-04-06-claude-usage-limits-10-tips/meme.jpg)

## 9. 在離峰時段工作

自 2026 年 3 月 26 日起：Anthropic 會在尖峰時段更快地消耗你的 5 小時 session 限額：

- **太平洋時間** 上午 5:00 到 11:00
- **東部時間** 上午 8:00 到下午 2:00（工作日）

同樣的查詢、同樣的對話——但在尖峰時段，它對你的限額影響更大。你的每週限額不變，但分配方式改變了。

在晚上或週末執行高資源消耗的任務，能顯著延伸你的方案額度。

如果你不在美國（歐洲、拉美或亞洲），尖峰時段可能落在你的下午時段，記得根據時區換算。

## 10. 啟用 Extra Usage 作為安全網

Pro、Max 5x 和 Max 20x 方案的訂閱者可以在「Settings」→「Usage」中啟用「Overage」功能。

當你的 session 限額用完時，Claude 不會封鎖你的存取，而是切換到以 API 費率計費的 pay-as-you-go 模式。你可以設定每月消費上限，避免收到意外帳單。

這不是為了省 tokens，而是為了**不在最糟糕的時刻失去你的工作進度**。

---

一開始要遵守所有這些規則會很困難，但一旦你能自動運用它們，你幾乎不會再撞上限額。你甚至可能從 Max 方案降級到一般方案——因為你的 tokens 綽綽有餘！

**Claude 不計算訊息數量。它計算的是 tokens。**
