---
title: "我偷了 Anthropic 的內部密技，讓 Claude 第一次就給出完美輸出"
date: 2026-03-28
categories: [AI]
tags: [Claude, Cowork, skills, prompt engineering]
image:
  path: /assets/img/posts/2026-03-28-anthropic-cheat-code-perfect-claude-outputs/banner.jpg
  alt: "One-shot your outputs"
---

> 原文：[I stole Anthropic's internal cheat code for getting perfect Claude outputs on the first try](https://x.com/itsolelehmann/status/2037481298978099461) by **Ole Lehmann** (@itsolelehmann)
> 發佈日期：2026 年 3 月 27 日

---

Anthropic 的工程師剛剛揭露了他們從 Claude 取得完美輸出的確切方法。我把它改編成一個免費的 skill。讀完這篇文章後，你幾乎不需要再編輯 Claude 的輸出了。

**問題是：** 每次你必須編輯 Claude 的輸出，你都在浪費時間修正 Claude 猜錯的決定。

Anthropic 自己的工程師也遇到了同樣的問題，然後想出了一個看似簡單的修正方法：直接停止編輯輸出。

改為透過建立一個「planner agent」來控制輸入。

我深入研究了他們的方法，並將其改編為適用於所有輸出（不只是程式碼）。

它讓 Claude 將你的 prompt 擴展成一份完整的規格書（就像 Anthropic 的 planner 做的那樣），然後在開始建構之前，就它本來會猜測的確切決定來面試你。

前期 2 分鐘的規劃和面試，省掉了後端整個來回編輯的循環。

這讓你每週省回好幾個小時，可以專注在真正推動成果的事情上。

讓我解釋它是如何運作的，以及為什麼它能讓輸出好 10 倍。

## 為什麼 Claude 不夠好（即使你已經做對了一切）

你們大多數人打的不是那種懶惰的一行 prompt。

你可能已經設定好了 skills，你貼入 context，你給 Claude 一份真正的 brief。

輸出回來還不錯。大概到了 70% 的程度。

但要創造任何東西，Claude 需要回答一堆你從未明確回答的問題。

這到底是給誰的？主要的反對意見是什麼？情感弧線是什麼？你能承諾的那一個結果是什麼？

你的 prompt 涵蓋了其中一些。你的 context 檔案涵蓋了更多一些。

Claude 悄悄地猜了剩下的，而且它猜向了中間值。

這就像告訴一位主廚：「我想吃義大利麵，這是我買的一些食材，我喜歡義大利料理。」

還不錯的 brief。

但主廚仍然得猜：紅醬還是奶油？多辣？清爽還是濃郁？

餐點回來還行，但你不斷送回去：「少點奶油、多點大蒜、其實做辣一點。」

這就是編輯循環。輸出到了 70% 是因為 70% 的決定是對的。另外 30% 是假設，而那些假設正是輸出失敗的地方。

Anthropic 自己的工程師撞上了完全相同的牆。

上週他們悄悄發布了一份內部工程文件，關於他們團隊如何從 Claude 取得更好的結果。

他們的修正方法簡單到不行：**停止修正輸出。改為控制輸入。**

## 修正方法：在 Claude 建構任何東西之前加入 planner 步驟

在 Claude 建構任何東西之前，一個獨立的 agent 將短 prompt 擴展成一份完整計畫：範圍、相依性、受眾、設計語言，全部。

一位工程師給了 Claude 一句話：「Create a 2D retro game maker with features including a level editor, sprite editor, entity behaviors, and a playable test mode.」

Planner 把它變成了一份橫跨 10 個建構階段的 16 項功能規格書。全部來自一句話。

沒有 planner 的版本產出了一個物理引擎壞掉、無法玩的遊戲。有 planner 的版本產出了一個有完整可玩 gameplay 的精美應用。

**他們的關鍵發現：**

> 控制輸入比修正輸出產生更好的結果。

而這個教訓適用於你用 AI 建構的任何東西，不只是程式碼。

所以我採用了他們的概念，並加入了對你正在創造的任何東西真正重要的部分：

不是猜測一個更好的計畫，而是 Claude 找到具體的缺口並直接問你。

## 我建了一個幫你做規劃的 skill

這個 skill 叫做 **the-interviewer**。

你告訴 Claude 建構某樣東西，它會自動觸發（基本上在每個 session 中），在任何生產開始前做 3 件事：

1. **將你的 prompt 擴展成完整規格書。** Anthropic 的方法。Claude 讀取你的 context 檔案，建構你的請求最完整版本的大綱：每個章節都定義了其策略目的。然後它在繼續之前向你展示整個東西。

2. **就缺口面試你。** Anthropic 的 planner 跳過的部分。不是猜測它無法從 context 中解決的東西，而是直接問你。對於每個問題，它提出一個建議答案，所以你是在微調而不是從零開始產生。它持續進行直到每個缺口都被填滿。

3. **建立 brief 並開始創作。** 將所有東西組裝成一份 creative brief，然後從那份 brief 開始寫。

整個過程只需要幾分鐘。

## 如何設定

1. 從 [GitHub](https://github.com) 下載這個 skill。
2. 將檔案貼到 Claude（Cowork 或 Claude Code）並說「add this as a skill」。
3. 告訴 Claude 創造某樣東西。Skill 會自動觸發、執行面試、建立 brief，然後開始製作。

它適用於 landing pages、newsletters、email sequences、簡報、課程大綱、提案——任何你要求 Claude 從 prompt 建構的東西。

## 去試試吧

把它放進去，然後告訴 Claude 創造一樣你這週正在做的東西。

2 分鐘的回答問題，省下潛在的無盡編輯。

那是你每週可以省回好幾個小時，花在真正讓你的業務成長的工作上。
