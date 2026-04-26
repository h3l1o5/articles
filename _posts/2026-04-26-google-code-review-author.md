---
title: "Google Code Review 指南 — CL Author 篇：如何讓你的程式碼變更被快速理解與接受"
date: 2026-04-26
categories: [Engineering]
tags: [google, code-review, engineering-practices, communication, career-growth]
---

> 原文：[Google Engineering Practices Documentation](https://google.github.io/eng-practices/) by **Google**
> 本文基於 Google 開源的工程實踐文件，重新整理撰寫為 CL Author（程式碼提交者）視角的實踐指南。

---

很多工程師把 code review 當成一道關卡——寫完程式碼，丟出去，等人放行。被 reviewer 要求修改時，覺得是對自己的否定；CL description 隨便寫個 "fix bug" 就送出；一次塞進上千行變更，然後困惑為什麼 review 拖了三天還沒人看。

但如果你換個角度看，code review 其實是工程師日常中最密集的「溝通訓練場」。每一次提交 CL，你都在練習三件事：

1. **把你做了什麼、為什麼這樣做，用文字精確表達出來**
2. **把一個複雜的問題拆解成別人能快速理解的單元**
3. **在收到回饋時，用建設性的方式推進討論**

這三件事，恰好也是跨團隊協作中最關鍵的能力——清楚的 status update、精準的問題定義、有效的非同步溝通。

Google 的工程實踐文件中，有一整個章節專門寫給 CL Author。這篇文章把其中的核心內容整理出來，加上我自己的理解和補充，希望能幫你從「只是把程式碼丟出去等 review」變成「主動讓 reviewer 快速理解並接受你的變更」。

---

## 一、CL Description：你留給未來的永久紀錄

CL description 不是寫給 reviewer 看完就丟的便條紙。它是一份**公開的永久歷史紀錄**。

這個觀念的轉換非常重要。未來可能有幾百個工程師會因為各種原因搜尋到你的 CL——debug 某個行為的起源、理解某段程式碼為什麼長這樣、評估某個舊設計決策是否該調整。他們唯一能依賴的，就是你當初寫的 description。

程式碼本身只能告訴人「what」——做了什麼改動。但「why」——為什麼要這樣改、當時的考量是什麼、有什麼替代方案被排除了——這些只有 description 能傳達。

### 第一行：讓人一眼知道這個 CL 在幹嘛

第一行是整個 description 最關鍵的部分。在 version control history 中，通常只會顯示這一行。它必須：

- **用祈使句**（imperative mood），像是下一道命令。不是 "Deleting the FizzBuzz RPC..."，而是 "Delete the FizzBuzz RPC and replace it with the new system."
- **是一個完整的句子**，能獨立存在、有意義
- **夠簡短，但夠具體**，讓人在快速掃過 commit log 時能立刻抓到重點

這個原則適用於任何需要「一句話摘要」的場景。當你寫 Slack 訊息更新進度、在 Jira ticket 寫 summary、或是在 standup 報告你做了什麼——同樣的技巧都適用。練習用一句祈使句精準描述你完成的事情，是一個值得刻意訓練的溝通能力。

### Body：提供足夠的 context

第一行之後，body 的內容應該包含：

- **問題背景**：為什麼需要這個改動？解決了什麼問題？
- **解法的 rationale**：為什麼選擇這個方案？有沒有其他方案被考慮過但排除了？
- **方案的已知限制**：目前的做法有什麼 shortcoming？是否有 follow-up 的計畫？
- **相關參考資料**：bug number、benchmark 數據、design document 的連結

有一點特別值得注意：**當你引用外部資源時，要在 description 裡提供足夠的摘要 context**。不要只貼一個 design doc 的連結就了事。未來的讀者可能沒有權限存取那份文件，或者那份文件已經被刪除了。你需要讓讀者光看 description 就能理解大致的脈絡。

這個原則延伸到日常溝通也一樣。當你在 Slack 跟另一個團隊的人說「詳情見這份文件」，對方可能根本打不開。養成在訊息中附帶關鍵摘要的習慣，會讓你的溝通效率大幅提升。

### 壞 Description vs. 好 Description

**這些 description 毫無價值：**

- "Fix bug"——哪個 bug？什麼症狀？怎麼修的？
- "Fix build"——什麼壞了？為什麼壞了？
- "Add patch"——patch 什麼？為什麼？
- "Moving code from A to B"——為什麼要搬？
- "Phase 1"——Phase 1 做了什麼？
- "Add convenience functions"——什麼 function？解決什麼問題？
- "kill weird URLs"——「weird」是什麼意思？為什麼要移除？

這些 description 的共同問題是：它們只描述了動作，沒有提供任何 context。未來的讀者看到這些紀錄，完全無法理解這個改動的意義。

**好的 description 長這樣：**

> **Remove size limit on RPC server message freelist.**
>
> Servers like FizzBuzz have very large messages and would benefit from reuse. Make the freelist larger, and add a goroutine that frees freelist entries slowly over time, so that idle servers eventually release all freelist entries.

這個 description 做對了什麼？第一行清楚說明做了什麼改動。Body 解釋了為什麼——哪些 server 會受益、具體的實作策略、以及對 idle server 的處理方式。

再看一個例子：

> **Create a Python3 build rule for status.py.**
>
> This allows consumers who are already using this in Python3 to depend on a rule that is next to the original status build rule instead of somewhere in their own tree. It encourages new consumers to use Python3 if they can, instead of Python2, and significantly simplifies some automated build file refactoring tools being worked on currently.

這裡不只解釋了「做了什麼」，還清楚說明了三個「為什麼」：方便現有使用者、引導新使用者、簡化自動化工具。讀者能立刻理解這個改動的動機和預期影響。

### 送出前的最後檢查

一個容易被忽略的步驟：**在送出 review 之前，重新讀一次你的 description，確認它還準確反映最終的 CL 內容**。

開發過程中，scope 經常會變。你可能一開始打算改 A 和 B，但最後決定把 B 拆到下一個 CL。如果 description 還寫著要改 B，reviewer 會困惑，未來的讀者也會被誤導。

### Tags（選用）

你可以在第一行加上 tag，例如 `[tag]`、`#tag` 或 `tag:`。這對大型專案的分類搜尋有幫助。但記得：即使加了 tag，第一行仍然要保持簡短。

---

## 二、Small CLs：讓你的改動容易被消化

如果說好的 CL description 是「讓 reviewer 理解你在做什麼」，那 small CL 就是「讓 reviewer 有辦法真的 review」。

這是很多工程師低估的一點：**CL 的大小直接影響 review 的品質和速度**。一個 3000 行的 CL，就算你的 description 寫得再完美，reviewer 也很難在合理時間內仔細審查每一行。而且 Google 的文件裡直接說了：

> Reviewers have discretion to reject your change outright for the sole reason of it being too large.

沒錯，reviewer 可以單純因為你的 CL 太大，就直接拒絕 review。

### 為什麼 Small CL 這麼重要

八個具體的好處：

1. **Review 更快**——reviewer 更容易找到 5 分鐘看一個小 CL，而不是擠出連續 30 分鐘看一個大 CL
2. **Review 更徹底**——大 CL 容易讓 reviewer 產生疲勞和挫折感，導致該抓到的問題被放過
3. **更少 bug**——改動範圍小，更容易推理影響範圍
4. **被拒絕時損失更小**——如果一個小 CL 被打回，你浪費的工作量遠比一個大 CL 少
5. **更容易 merge**——小 CL 遇到 merge conflict 的機率低很多
6. **更好的設計品質**——小範圍的改動更容易被打磨和優化
7. **減少 review blocking**——你可以把工作拆成多個獨立的小 CL，在等待 review 的同時繼續做其他部分
8. **Rollback 更簡單**——如果出了問題，rollback 一個小 CL 比 rollback 一個大 CL 安全得多

這些好處加起來，其實就是在說：**Small CL 讓整個開發流程的摩擦力降到最低**。

### 多小算「小」？

Google 的建議：

- 一個 CL 應該是**一個 self-contained 的改動**——通常是某個 feature 的一部分，而不是整個 feature
- **100 行通常是合理的大小，1000 行通常太大了**
- 檔案的分散程度也很重要：200 行集中在一個檔案是 OK 的，200 行散在 50 個檔案就太分散了
- 相關的 test code 應該包含在同一個 CL 裡

### 什麼時候可以例外？

有些情況下大 CL 是合理的：

- **整個檔案的刪除**——刪掉一整個過時的模組，即使涉及很多行，邏輯上仍然是簡單的
- **自動化的重構**——由工具產生的批量修改，reviewer 只需要確認工具的正確性

### 如何把大改動拆小

這是真正需要技巧的地方。拆分 CL 本身就是一種設計能力——你需要找到改動之間的邊界，確保每個 CL 都是 self-contained 且有意義的。

幾種常見的拆分策略：

**Stacking（堆疊）：** 寫完一個小 CL，基於它繼續開發下一個。每個 CL 都建立在前一個之上，形成一個 CL chain。這是最常見的做法。

**By files（按檔案分組）：** 把改動按照涉及的檔案群組拆分成不同的 CL。例如修改 API 定義是一個 CL，修改 client code 是另一個。

**Horizontally（水平拆分）：** 先提交 shared code 或 stub，再提交各個使用它的層。例如先提交一個新的 utility function，再分別提交使用它的 service layer 和 controller layer。

**Vertically（垂直拆分）：** 把獨立的 full-stack feature 拆成不同的 CL。例如 feature A 的前後端改動是一個 CL，feature B 的前後端改動是另一個。

**混合使用：** 實務上通常需要組合以上策略。

### 重構要單獨拆出來

這一點特別重要：**Refactoring 通常應該放在獨立的 CL 裡，和 feature change 或 bug fix 分開**。

原因很實際：當 refactoring 和功能改動混在一起時，reviewer 很難分辨哪些改動是「行為不變的搬移」，哪些是「真的改了邏輯」。這會大幅增加 review 的認知負擔，也讓未來的人更難理解每個 CL 的目的。

### 不要讓 build 壞掉

不管怎麼拆分，有一個底線：**每一個 CL 送出後，系統都必須能正常運作**。不能因為你把 feature 拆成五個 CL，中間某個 CL 會讓 build 壞掉或 test 失敗。

### Test 不能缺席

Google 的標準很明確：每個 CL 都應該包含相關的 test code。不是「等功能全部寫完再補測試」，而是每一個 CL 都帶著對應的測試一起送出。

> Tests are expected for all Google changes.

這個做法的好處不只是品質保證。它也讓 reviewer 更容易理解你的 CL——test code 其實是最好的「使用範例」，reviewer 可以通過看測試來理解你的改動預期達到什麼效果。

---

## 三、Handling Reviewer Comments：把 Review 當成合作而不是對抗

這是很多工程師最掙扎的部分。你花了好幾個小時寫出來的程式碼，被人指出問題，本能反應往往是防禦性的——「他根本沒看懂」、「這樣改沒必要」、「我有我的理由」。

Google 的指南在這裡給出了一個很根本的心態調整：

### 不要把 review comments 當成人身攻擊

Code review 的目的是維護 codebase 的品質——它針對的是程式碼，不是你這個人。當 reviewer 說「這段邏輯可以簡化」，他不是在說「你很笨」；他是在說「這段程式碼可以變得更好」。

> Never respond in anger to code review comments.

如果你讀到一個 comment 覺得不舒服，先離開螢幕，冷靜一下，等情緒過去之後再回覆。帶著怒氣回覆的 review comment 幾乎永遠會讓事情變得更糟。

### 修改程式碼本身，而不是在 review tool 裡解釋

這是一個非常實用的原則：**如果 reviewer 看不懂你的程式碼，問題出在程式碼上，不是 reviewer 上**。

正確的做法是：

- 改善程式碼本身，讓它更清楚
- 在程式碼裡加上 comment 解釋為什麼這樣做
- 如果程式碼本身已經夠清楚，但涉及一些非顯而易見的決策，在 review tool 裡補充說明**也**可以——但這只是補充，不是替代

> Writing a response in the code review tool doesn't help future code readers.

在 review tool 裡的對話，只有參與這次 review 的人會看到。但程式碼裡的 comment，未來每一個讀到這段程式碼的人都會受益。

### 用合作的心態思考

收到 comment 時，問自己：

- 這個回饋是否有助於改善 codebase 和產品？
- reviewer 指出的問題我是不是真的沒考慮到？
- 就算我不完全同意，有沒有值得參考的部分？

**不好的做法：** 直接拒絕。「不，我覺得這樣就好了。」

**好的做法：** 解釋你的理由，討論 tradeoff，問澄清問題。「我選擇這個方式是因為 X 和 Y 的考量。你覺得在 Z 的情境下也適用嗎？」

> Courtesy and respect should always be a first priority.

即使你認為 reviewer 的建議不對，保持禮貌和尊重是底線。Code review 的本質是專業對話，不是辯論賽。

---

## 四、Emergency CLs：什麼才算是「緊急」

最後一個主題，看似跟日常工作關係不大，但其實涉及一個很重要的判斷力——**區分真正的緊急和「感覺很急」**。

### 真正的 Emergency CL

Emergency CL 是一個小改動，用來處理以下情況：

- 讓一個重大發布能繼續進行，而不是被迫 rollback
- 修復一個正在嚴重影響 production 用戶的 bug
- 處理一個緊迫的法律問題
- 關閉一個重大的安全漏洞

Emergency CL 可以在 review 流程上有特殊待遇——但前提是它真的是 emergency。

### 這些「不算」emergency

- 希望這週而不是下週發布
- 開發者已經在這個 feature 上工作很久，很想趕快收尾
- Reviewer 在不同時區
- 今天是週五，想在週末前送出
- Manager 給了一個「軟性 deadline」，沒有真正的後果

這些情境可能讓你「感覺」很急，但它們不構成跳過正常 review 流程的理由。

### Hard Deadline 的判斷標準

真正的 hard deadline 是指：**如果錯過，會發生災難性的後果**。例如合約義務、硬體出貨視窗。

如果錯過 deadline 的結果只是「不太方便」或「老闆會不高興」，那它不是 hard deadline。

這個框架延伸到日常工作中也很有用。當你評估一件事的優先級時，問自己：「如果這件事延遲一週，會發生什麼具體的、不可逆的後果？」如果答不出來，它可能沒有你以為的那麼緊急。

---

## 結語：Code Review 是溝通能力的具體實踐

回到開頭說的三個技能——清楚表達你做了什麼和為什麼、精準定義問題、有效地給出 status update——Google 的 CL Author 指南其實提供了一套非常具體的訓練框架：

- **寫 CL description** 訓練你用文字精確傳達 context 和 rationale
- **拆分 small CL** 訓練你把複雜問題分解成可消化的單元
- **回應 reviewer comments** 訓練你在專業對話中保持建設性

這些技能不只在 code review 中有用。每次你寫一封跨團隊的 email、在 Slack 更新專案進度、或是在會議中解釋一個技術決策時，你都在使用同樣的能力。

差別只在於：code review 每天都會發生，而且有明確的回饋循環——你的 description 寫得好不好、CL 拆得合不合理、回覆 comment 的方式是否有效，reviewer 的反應會直接告訴你。

把每一次 code review 都當成一次刻意練習的機會，而不只是一個等待放行的關卡。長期下來，你會發現改善的不只是 review 的通過速度，而是你整體的工程溝通能力。
