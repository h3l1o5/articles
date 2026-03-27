---
title: "如何讓你的 Claude Skills 提升 10 倍（使用 Karpathy 的 autoresearch 方法）"
date: 2026-03-28
categories: [AI]
tags: [Claude, skills, Karpathy, autoresearch]
image:
  path: /assets/img/posts/2026-03-28-10x-claude-skills-karpathy-autoresearch/banner.jpg
  alt: "How to 10x your Claude Skills using Karpathy's autoresearch method"
---

> 原文：[How to 10x your Claude Skills (using Karpathy's autoresearch method)](https://x.com/itsolelehmann/status/2033919415771713715) by **Ole Lehmann** (@itsolelehmann)
> 發佈日期：2026 年 3 月 17 日

---

你的 Claude skills 大概有 30% 的時候會失敗，而你根本沒注意到。

我建立了一套方法，可以在 autopilot 模式下自動改進任何 skill，這篇文章會告訴你如何自己執行它。

你只需要啟動它，agent 就會反覆測試和優化 skill，完全不需要你動手。

我的 landing page 文案 skill 從 56% 的品質檢查通過率提升到了 92%。完全零人工作業。

Agent 就這樣自己不斷測試和收緊 prompt。

以下是完整方法，以及我建立的 skill，讓你可以在自己的東西上執行：

## 這方法從何而來

Andrej Karpathy（OpenAI 共同創辦人、前 Tesla AI 負責人、創造「vibe coding」一詞的人）發布了一個叫做 autoresearch 的方法。

概念很簡單：與其你手動改進某樣東西，不如讓 AI agent 在一個迴圈裡幫你做。

它嘗試一個小改動。檢查結果是否變好。如果變好就保留，如果沒有就丟棄。

然後再做一次。再一次。

他用這方法來改進 machine learning 程式碼。但這個方法適用於任何你能衡量和改進的東西。

包括你在 Claude 裡建立的 skills。

我把他的方法轉化成一個 skill，可以在 Claude Code 和 Cowork 裡運作。我只需要在我設置中的任何其他 skill 上執行它。

我說「run autoresearch on my landing page skill」，它就會處理一切。

## 一個迴圈如何自動改進你的 skills

這樣想。

你有一個食譜，10 次裡有 7 次做出來很棒。另外 3 次有些不對。可能醬汁太淡，可能調味不對。

與其從頭重寫整個食譜，你只改一種食材。用這個改動做 10 次。

變好了？保留改動。
變差了？換回原本的食材。

然後改下一樣東西。再做 10 次。變好還是變差？保留或還原。

經過 50 輪之後，你的食譜 10 次裡有 9.5 次都能成功。

這就是 autoresearch 對你的 skills 做的事。

「食譜」就是你的 skill prompt。
「烹飪」就是執行 skill。
「品嚐」就是對 output 評分。

你唯一需要提供的是評分標準。

## 告訴 agent「好」是什麼意思的 checklist

你給 agent 一份簡單的 checklist，定義「好」長什麼樣。這是你在整個流程中唯一的工作。

你用一份簡單的 yes/no 問題 checklist 來完成。

每個問題檢查 output 的一個具體面向。通過或不通過。就這樣。

Agent 用這份 checklist 來評分每一次的 output，而這些分數告訴它改動是有幫助還是有害。

想像一個老師用 checklist 批改作文。

但不是「替寫作品質評 1-10 分」（這很模糊而且每次都不一樣），checklist 上的每一項都是明確的 yes or no：

- 學生有沒有包含論點陳述？Yes or no。
- 每個來源都有引用嗎？Yes or no。
- 是否在 5 頁以內？Yes or no。

你可以用這份 checklist 批改 100 篇作文，每次都得到一致的結果。

這裡的概念一樣。對於 landing page 文案 skill，你的 checklist 可能長這樣：

- 「標題是否包含具體的數字或成果？」（抓出像「Grow Your Business」這種模糊標題）
- 「文案是否不含 buzzwords，例如『revolutionary』『synergy』『cutting-edge』『next-level』？」
- 「CTA 是否使用了具體的動詞片語？」（抓出像「Learn More」或「Click Here」這種弱 CTA）
- 「第一行是否點出了具體的痛點？」（抓出像「In today's fast-paced world...」這種通用開頭）
- 「總文案是否在 150 字以內？」（抓出讓讀者流失的臃腫頁面）

你不需要自己想出這些問題。當你啟動 autoresearch 時，agent 會引導你完成。

它會問你「好」長什麼樣，幫你把感覺轉化成具體的 yes/no 問題，如果你有現有的 style guides，它甚至會提議從中擷取。

3-6 個問題是最佳數量。超過這個數量，skill 會開始鑽 checklist 的漏洞（就像一個學生背答案卻不理解內容）。

## 執行方式

**步驟 1：** 下載 skill。抓取檔案後放到你 Claude Code 或 Cowork 的 skills 資料夾。

**步驟 2：** 選一個要改進的 skill。說「run autoresearch on my [skill name] skill」。挑最讓你困擾的那個——那個有一半時間給你好結果、另一半時間給你垃圾的 skill。

**步驟 3：** Agent 會問你 3 件事。要優化哪個 skill。要用什麼測試輸入（像是「write landing page copy for an AI productivity tool」）。以及你的 checklist 問題是什麼。

**步驟 4：** 它執行你的 skill 並顯示你的起始分數。這是 baseline。我的 landing page skill 起始是 56%。模糊標題、buzzword 堆砌、弱 CTA。超過一半的檢查都失敗了。

**步驟 5：** 它在瀏覽器中開啟一個 live dashboard。分數圖表隨時間上升。每個 checklist 問題的 pass/fail 明細。每次嘗試改動的紀錄。每 10 秒自動更新。

![autoresearch live dashboard](/assets/img/posts/2026-03-28-10x-claude-skills-karpathy-autoresearch/dashboard.jpg)

**步驟 6：** 走開就好。Agent 進入迴圈。分析哪裡失敗。對 skill prompt 做一個小改動。再測試一次。如果分數上升就保留改動，如果下降就復原。

然後再做一次。再一次。它會持續自主運行，直到你停止它或連續 3 次達到 95% 以上。

你可以看 dashboard，也可以完全走開。它不需要你就能運行。而且它會把改進版本存為獨立檔案，所以你的原始 skill 完全不受影響。

## 我的 landing page skill 發生了什麼

我在 landing page 文案 skill 上執行了它。結果如下：

56% → 92%。4 輪改動。3 個保留，1 個復原。

以下是 agent 實際對我的 skill prompt 做了什麼改動：

- 針對最常見的失敗新增了一條具體規則：「你的標題必須包含具體的數字或成果。絕對不要使用像『Transform Your Business』這樣的模糊承諾。」
- 新增了禁用 buzzwords 清單：「絕對不要使用：revolutionary、cutting-edge、synergy、next-level、game-changing、leverage、unlock、transform。」
- 新增了一個強 landing page 段落的範例，標註了痛點開頭和 CTA，讓 skill 能看到好的範例而不是靠猜測。
- 嘗試了更嚴格的字數限制，但復原了，因為文案變得太單薄，CTA 品質下降。（系統會捕捉那些單獨看起來像改進、但整體上損害 output 的改動。）

完成後，我得到了：

- 改進後的 skill，獨立儲存（原始版本保持不動，方便你隨時還原）
- 結果紀錄，顯示每一輪的分數
- 變更日誌，解釋每一次嘗試的改動、agent 為什麼嘗試它、以及它是否有幫助
- 原始 skill 的備份，以防你想回到原版

那份變更日誌可能是最有價值的部分。它是一份完整的紀錄，記載了對那個特定 skill 什麼有效、什麼無效。

當未來更聰明的模型出來時，你把那份變更日誌交給它們，它們就能從上一個 agent 停下的地方繼續。

## 這不只適用於 skills

這個方法適用於任何你能評分的東西。

- **網站速度：** 有人用這方法改進頁面載入時間。改一樣東西、測量速度、保留或復原。在 67 輪中從 1100ms 降到 67ms。
- **Cold outreach：** 定義你的 checklist：「有沒有提到潛在客戶的公司？是否在 75 字以內？結尾是否有具體的問題？」讓 agent 跑 50 個變體。
- **Newsletter 開頭：**「開場有沒有包含個人化細節？」和「是否沒有老套用語？」讓 agent 在 autopilot 模式下收緊你的寫作。
- **任何你重複使用的 prompt。**

如果你能評分，你就能用 autoresearch 改進它。

## 去試試吧

挑你表現最差的 skill。啟動 autoresearch。回來時就能看到一個真正能用的東西。
