---
title: "SCQA 框架：軟體工程師的結構化溝通指南"
date: 2026-04-27
categories: [Engineering]
tags: [scqa, communication, structured-thinking, career-growth, engineering-practices]
---

> 本文為原創文章，基於 Barbara Minto 的 Pyramid Principle 與 SCQA 框架，針對軟體工程師的日常溝通場景重新整理撰寫。

---

## 你的訊息為什麼沒人回

你花了十五分鐘寫一則 Slack 訊息，詳細描述了你遇到的技術問題、嘗試過的方案、查過的文件、讀過的 source code。發出去之後——沒有人回應。

不是因為同事不想幫你，而是因為他們讀完之後不知道你到底需要什麼。

這個問題幾乎每個工程師都遇過。我們受過的訓練是 bottom-up 思維：蒐集資料、分析、最後得出結論。這在寫程式時完美運作——compiler 不在乎你把結論放在哪裡。但在人與人的溝通中，這種思維方式會讓你的訊息變成一堵牆，讀者必須從頭到尾讀完才能理解你要什麼。

結果就是：

- Status update 沒人看完
- Cross-team 的請求石沉大海
- Incident report 讓人更困惑而不是更清楚
- Standup 說了五分鐘但沒人記得你說了什麼
- 老闆永遠在問「所以你的 point 是什麼？」

這不是你的技術能力問題，這是溝通結構的問題。而 SCQA 是解決這個問題最簡單有效的框架。

## 什麼是 SCQA

SCQA 由 Barbara Minto 在 1970 年代於 McKinsey 工作時發展出來，是 Pyramid Principle 的核心敘事結構。四個字母分別代表：

### Situation（情境）

當前的狀態、背景、所有人都同意的事實。這是你和讀者之間的共同起點。

對工程師來說，這通常是：目前的系統狀態、sprint 的目標、已經達成的進度、大家都知道的既有條件。

**關鍵原則**：Situation 裡不該出現任何讓人驚訝的資訊。如果讀者看到 Situation 就皺眉，你放錯東西了。

### Complication（複雜因素）

什麼東西變了？什麼東西壞了？什麼挑戰出現了？這是製造張力的地方。

對工程師來說，這通常是：發現的 bug、意外的技術限制、timeline 的衝突、dependency 的變動、production 的問題。

**關鍵原則**：Complication 要具體。「遇到一些問題」不是 complication，「payment API 在高流量時 P99 latency 飆升到 3 秒」才是。

### Question（問題）

從 Complication 自然衍生出的問題。這讓讀者知道你接下來要回答什麼。

對工程師來說，這通常是：「我們該怎麼處理？」「需要誰的協助？」「是否該改變計畫？」

**關鍵原則**：很多時候 Question 是隱含的，不需要明確寫出來。如果 Complication 寫得好，讀者心中自然會產生那個問題。

### Answer（答案）

你的建議、請求、結論、或你需要對方做的事。

對工程師來說，這通常是：你的解法提案、你需要的 approval、你請求的資源、或你建議的 timeline 調整。

**關鍵原則**：Answer 要 actionable。讀者看完之後應該知道下一步是什麼。

### 為什麼這個順序有效

人類處理資訊的方式是：先確認自己在哪（熟悉感）→ 感受到張力（好奇心）→ 想知道答案（期待）→ 得到解答（滿足）。

SCQA 完美對應這個認知流程。當你跳過 Situation 直接丟出一堆細節，讀者的大腦會花大量精力試圖建立 context，根本沒有餘力處理你真正要傳達的訊息。

### 與 Pyramid Principle 的關係

Pyramid Principle 的核心原則是「先給結論，再給支撐論點」。SCQA 則是引導到結論之前的敘事框架。兩者搭配使用：SCQA 負責「為什麼你該在乎這件事」，Pyramid Principle 負責「以下是我的結構化論證」。關於 Pyramid Principle 的更多細節，可以參考本站的 McKinsey 結構化問題解決法一文。

## 實戰範例：Before vs. After

以下六個場景涵蓋工程師最常見的溝通情境。每個範例的「Before」都是真實世界中工程師常見的寫法——不是故意寫得爛，而是自然的 brain dump 風格。

### 場景一：Slack status update（向團隊回報進度）

**背景**：你在實作一個新功能時發現了效能問題。

#### Before（不好的寫法）

> 我今天在做 user profile page 的 lazy loading 功能，然後發現 `getUserProfile` API 在 local 跑都要 800ms，我去看了一下 query，發現它 join 了 5 張 table 而且沒有用 index，然後我試了加 index 但是 migration 會 lock table 大概要 30 分鐘，我不確定能不能在 production 直接跑，然後我又看了一下能不能改成 batch query 但是 ORM 不太支援，所以我現在在想要不要自己寫 raw SQL，但是這樣會跟我們的 coding convention 衝突，目前還在研究中。明天應該會有結論。

#### After（好的寫法）

> **User profile page 的功能開發會延遲，需要先解決底層效能問題。**
>
> **Situation**：Profile page lazy loading 功能開發中，預計本週三完成。
>
> **Complication**：`getUserProfile` API 的 P50 latency 達 800ms（目標 < 200ms），根本原因是一個 5-table join 且缺少 index。加 index 的 migration 需要 lock table 30 分鐘，不適合直接在 production 執行。
>
> **Answer**：我計畫改用分段查詢（拆成 2 個 query + application-level join），預估明天完成 POC。如果成功，功能交付延後到週五。如果不行，會在明天 standup 提出來討論替代方案。

**為什麼 After 更好**：讀者在第一句話就知道結論（會延遲），接著理解為什麼（效能問題），最後知道你的計畫和 timeline。不需要讀完所有技術細節就能掌握全貌。

---

### 場景二：Cross-team 請求協助

**背景**：你需要另一個 team 更新他們的 API，因為它 blocking 了你的工作。

#### Before（不好的寫法）

> Hi Payment Team，我是 Order Team 的 Kevin。我們最近在做新的 order refund flow，然後我發現你們的 `/v2/refund` endpoint 回傳的 response 裡面沒有 `refund_id`，只有 `transaction_id`。我們這邊需要 `refund_id` 來追蹤 refund 狀態，因為我們的 DB schema 是用 `refund_id` 當 foreign key 的。我看了一下你們的 API spec 文件（好像是去年更新的），裡面寫說 `refund_id` 會在 v3 加入，但我不確定 v3 什麼時候出。我們的 deadline 是五月中，所以想問一下這個有沒有辦法處理？另外如果你們不方便改的話，我們可能需要自己 maintain 一個 mapping table，但這樣感覺不太好。

#### After（好的寫法）

> **Request：希望 `/v2/refund` response 加入 `refund_id` 欄位，或提供 workaround 方案。我們的 deadline 是 5/15。**
>
> **Situation**：Order Team 正在開發新的 refund flow（deadline 5/15），依賴 Payment Team 的 `/v2/refund` endpoint。
>
> **Complication**：`/v2/refund` response 目前只回傳 `transaction_id`，但我們需要 `refund_id` 來建立狀態追蹤（DB schema 以此為 foreign key）。API spec 提到 `refund_id` 會在 v3 加入，但 v3 的 timeline 不明確。
>
> **Question**：在 5/15 之前，有沒有可行的方案讓我們拿到 `refund_id`？
>
> **Answer（我們想到的選項）**：
> 1. **（首選）** 在 v2 response 中加入 `refund_id` 欄位（backward compatible）
> 2. 提供一個 lookup endpoint（用 `transaction_id` 查 `refund_id`）
> 3. 我們自己維護 mapping table（最不理想，會有 consistency 風險）
>
> 你們覺得哪個可行？或者有其他我們沒想到的方案？

**為什麼 After 更好**：對方一眼就知道你要什麼（加欄位）、為什麼（blocking 你的 deadline）、有多急（5/15）、以及你已經想過哪些方案。他們可以直接回覆「方案 1 可以，我排下週」，而不需要來回問三次才搞清楚你到底需要什麼。

---

### 場景三：Incident report / postmortem summary

**背景**：一次由 database migration 引起的 production outage。

#### Before（不好的寫法）

> 今天下午 2:15 我跑了 DB migration 然後 production 就掛了。原因是那個 migration 會 add column with default value，然後 PostgreSQL 在舊版本會 rewrite 整張 table，我們的 users table 有 1200 萬筆資料所以 lock 了很久。monitoring 在 2:18 就有 alert 了但是我那時候沒看到因為我在等 migration 跑完。後來是 on-call 的 Sarah 在 2:25 發現 error rate 飆高才開始處理。我們先試了 cancel migration 但是 PostgreSQL 不讓我們 cancel，所以只好等它跑完。2:47 migration 跑完之後 service 就恢復了。total downtime 大概 32 分鐘。之後我們應該要在 staging 先測過。

#### After（好的寫法）

> ## Incident Summary：Production outage 32 分鐘（users table migration lock）
>
> | 項目 | 內容 |
> |------|------|
> | 影響時間 | 2026-04-25 14:15 – 14:47 UTC+8（32 分鐘） |
> | 影響範圍 | 所有需要讀寫 users table 的 service，約 85% 的 API requests |
> | 嚴重程度 | SEV-1 |
> | 根本原因 | Migration ADD COLUMN WITH DEFAULT 觸發 full table rewrite + exclusive lock |
>
> **Situation**：作為 Q2 user profile 改版的一部分，需要在 users table（12M rows）新增 `preferred_language` 欄位。
>
> **Complication**：PostgreSQL 12 對 ADD COLUMN WITH DEFAULT VALUE 會執行 full table rewrite 並持有 exclusive lock。這在 staging（50K rows）只花了 2 秒，但在 production（12M rows）花了 32 分鐘。期間所有存取 users table 的 query 被 block，導致 service 全面不可用。
>
> **Answer（修復與預防）**：
> - **立即修復**：已完成，migration 自然結束後 service 恢復。
> - **預防措施**：
>   1. 所有涉及大型 table 的 migration 必須使用 `pg_repack` 或分段 migration（不持有 long-running lock）
>   2. CI 加入 migration lint（檢測 > 100K rows 的 table 是否使用 unsafe pattern）
>   3. Migration runbook 新增「production data volume 驗證」步驟
>
> **Timeline** 與 **Action Items** 詳見完整 postmortem 文件。

**為什麼 After 更好**：任何人（包括不在場的 VP Engineering）在 30 秒內就能理解：發生了什麼、為什麼發生、以及如何避免再次發生。Before 版本是一段敘事，讀者必須自己提取重點。

---

### 場景四：Standup 被 block 時的回報

**背景**：你已經被一個 dependency 問題卡住兩天了。

#### Before（不好的寫法）

> 嗯...我這兩天都在搞那個 notification service 的 integration。問題是他們的 SDK 只支援到 Node 18，但我們已經升到 Node 20 了。我試過 downgrade 但是會影響到其他 package。然後我看了他們的 GitHub，有人開了 issue 但是還沒修。我也試過直接 call 他們的 REST API 不用 SDK，但是 authentication 那段的文件寫得很不清楚，我試了幾種方式都拿不到 token。我昨天有在他們的 Slack channel 問了但還沒回。所以...就是被卡住了。

#### After（好的寫法）

> **Blocked 2 天**：Notification service integration，因為他們的 SDK 不支援 Node 20，REST API 文件不足。
>
> **Situation**：Sprint 目標之一是串接 notification service，原定週三完成。
>
> **Complication**：他們的 SDK 只支援 Node 18（我們是 Node 20，無法 downgrade）。嘗試直接用 REST API 但 authentication 文件不完整，無法成功取得 token。已在對方 Slack channel 提問但 48 小時無回應。
>
> **我需要的協助**：
> 1. 有沒有人有 notification team 的直接聯繫方式？他們的 Slack channel 似乎不太活躍。
> 2. 或者 PM 評估：是否先把這張 ticket 移到下個 sprint，我先做其他工作？

**為什麼 After 更好**：在 standup 有限的時間裡，團隊立刻知道（1）你被卡了多久（2）具體卡在哪裡（3）你需要什麼幫助。Before 版本像在講故事，大家聽完可能只記得「好像被卡住了」但不知道該怎麼幫你。

---

### 場景五：向 manager 解釋專案延遲

**背景**：專案因為意外的技術複雜度會 miss deadline。

#### Before（不好的寫法）

> Hi Manager，想跟你說一下 payment integration 的進度。我們原本以為 Stripe 的 subscription API 可以直接用，但是後來發現我們的 billing model 比較特殊（有 per-seat + usage-based 的混合計費），Stripe 的 API 沒辦法直接支援這種模式。我們需要自己寫一層 billing engine 來計算每個 billing cycle 的金額，然後再透過 Stripe 來收款。這比原本預估的工作量大很多。我有跟 Stripe 的 support 確認過了，他們建議我們用 invoice API 搭配 custom line items，但這樣的話我們的 code path 會跟原本的設計差很多。另外 Stripe 那邊的 webhook 處理也比想像中複雜，有很多 edge case 要處理（例如 payment failed 之後的 retry logic、proration 的計算等等）。我覺得原本預估的 4 週可能不夠。

#### After（好的寫法）

> **Payment integration 預估需延後 3 週交付（原定 5/15 → 修正為 6/5），原因是 billing model 複雜度超出預期。需要你的 input 來決定下一步。**
>
> **Situation**：Payment integration 專案進行中，原定 4 週完成（deadline 5/15）。前 2 週已完成 Stripe 基礎串接與 subscription 建立 flow。
>
> **Complication**：我們的混合計費模型（per-seat + usage-based）無法直接對應 Stripe 的 subscription API。確認過 Stripe support 後，需要額外開發：
> - 自建 billing engine（計算混合費率）—— 約 2 週
> - 改用 invoice API + custom line items（取代原本的 subscription 設計）—— 約 1 週
> - Webhook edge case 處理（retry、proration）—— 包含在上述時間
>
> **Question**：以下三個方向，哪個最符合目前的 business priority？
>
> **Options**：
> 1. **接受延後 3 週**（6/5 交付完整功能）
> 2. **分階段交付**：5/15 先上 per-seat only（覆蓋 70% 客戶），usage-based 6/5 補上
> 3. **縮減 scope**：只做 per-seat，usage-based 移到下季度
>
> 我的建議是 Option 2，可以對大部分客戶先上線，同時給我們時間做好 usage-based 的部分。

**為什麼 After 更好**：Manager 在前兩行就知道核心資訊（延後多久、為什麼、需要決策）。更重要的是，你提供了選項而不只是問題——這展現了 ownership 和 problem-solving 的態度，而不是單純把問題往上拋。

---

### 場景六：Design doc / RFC 開頭段落

**背景**：你要提案在現有 API 前面加一層 caching layer。

#### Before（不好的寫法）

> 我們的 API response time 最近變得比較慢。我看了一下 metrics，發現有幾個 endpoint 的 latency 在過去三個月漲了不少。主要原因是 DB query 變多了（因為我們加了很多新功能），加上 traffic 也在成長。我覺得我們可以加一個 caching layer 來解決這個問題。Redis 是一個選擇，Memcached 也可以考慮。其實業界很多公司都在用 cache，像 Twitter、Facebook 都有很大的 caching infrastructure。以下是我的一些想法...

#### After（好的寫法）

> # RFC: API Response Caching Layer
>
> ## 摘要
>
> 提案在 API gateway 與 application layer 之間引入 Redis-based caching layer，目標是將 P95 latency 從目前的 1.2 秒降至 300ms 以內。
>
> ---
>
> **Situation**：我們的核心 API 在過去 6 個月從日均 50K requests 成長到 200K requests。同期間新增了 12 個 feature，使得 average query count per request 從 3 增加到 8。
>
> **Complication**：目前 P95 latency 已達 1.2 秒（SLA 目標為 500ms），且趨勢持續惡化。按當前成長率估算，2 個月內 P95 將突破 2 秒。單純 DB optimization 已進行過（Q1 完成 index tuning），效果有限。
>
> **Question**：如何在不進行大規模 architecture 改造的前提下，將 P95 latency 控制在 SLA 以內？
>
> **Answer**：引入 read-through caching layer（Redis），針對高頻率、低變動的 endpoint 進行 cache。預估可將命中率 > 60% 的 endpoint 的 latency 降至 50ms 以內，整體 P95 回到 300ms。
>
> 以下詳述 cache strategy、invalidation 設計、capacity planning 與 rollout plan。

**為什麼 After 更好**：任何 reviewer 在讀完第一頁就能決定「這個方向值不值得繼續讀下去」。Before 版本用了一半篇幅在鋪陳大家都知道的背景（「業界很多公司都在用 cache」），卻沒有清楚定義問題的嚴重性和 scope。

## 什麼時候不需要用 SCQA

SCQA 不是萬能工具。以下情境不需要（甚至不該用）：

**快速確認類訊息**：「Done」、「LGTM」、「Merged」、「+1」——加上 SCQA 結構只會顯得奇怪。

**雙方共享完整 context 的純技術討論**：你和 pair programming 的同事正在討論一個 function 的實作細節，兩人都看著同一段 code，不需要重建 Situation。

**Situation 對所有人都顯而易見時**：如果你在一個專門討論某個 bug 的 thread 裡回覆，大家都已經知道 Situation，直接從 Complication 或 Answer 開始即可。

**非正式的腦力激盪**：探索性對話中過度結構化會扼殺創意。

判斷的準則是：**當你的訊息需要對方採取行動、做出決策、或理解一個他們不完全了解的狀況時**，用 SCQA。其他時候，自然就好。

## 變體與快捷用法

### 一句話 SCQA（適合 Slack）

把四個元素壓縮成 2-3 句：

> Payment API timeout 問題（S: 昨天上線的 retry 機制）導致今天凌晨連鎖 timeout cascade（C），我已經先 rollback 那個 PR 並恢復服務（A），下午會寫 postmortem。

### CQ only（省略 S 和 A）

當 context 對所有人都很明確，而且你真的在尋求 input：

> 那個 migration 如果用 gh-ost 跑的話，estimated time 是 4 小時（C）。大家覺得排在週六凌晨跑可以嗎，還是有更好的 time slot？（Q）

### SCA（省略 Q）

當 question 從 complication 不言自明時：

> Staging 環境目前可以正常部署（S），但 integration test 有 3 個 flaky test 一直 random fail（C）。我已經先把它們 mark 成 skip 並開了 ticket 追蹤，不會 block release（A）。

### SC only（提出問題但不提供答案）

當你真的不知道答案，需要群體智慧：

> 我們的 Docker image build time 從 3 分鐘暴增到 15 分鐘（C），看起來是升級到 Node 20 之後 `npm install` 階段變慢了（S 的更多 context）。有人遇過類似的問題嗎？

## 如何練習

### 每天一次刻意練習

挑選你今天要發的一則訊息——可以是 Slack update、email、PR description——在發送前，用 SCQA 重新組織一次。不需要每則訊息都這樣做，一天一次就夠了。兩週後你會發現自己自然而然就在用這個結構思考。

### 從非同步溝通開始

文字訊息（Slack、email、document）比口頭溝通更容易練習，因為你有時間重新組織。等文字寫作熟練了，再把同樣的思維模式帶到口頭 standup 和 meeting 中。

### 用在 PR / CL description

每一個 PR description 本質上都是一個 SCQA：

- **S**：目前系統的行為（或 spec 的要求）
- **C**：為什麼需要這個改動（bug、新需求、tech debt）
- **Q**：（隱含）這個 PR 做了什麼改變？
- **A**：你的實作方式與關鍵設計決策

如果你的 PR description 只寫了「fix bug」或「add feature X」，試著用 SCQA 重寫一次。Reviewer 會感謝你的——這也是 Google Code Review Author Guide 所強調的：讓 reviewer 快速理解 context 是 author 的責任。

### 建立 template

如果你發現自己在某個情境反覆使用 SCQA（例如每週的 status report），做一個簡單的 template 存在你的 note-taking tool 裡：

```
[一句話總結]

Situation：
Complication：
Answer / Next step：
```

不需要每次都寫出 Q——大部分時候它是隱含的。

## 結語

Software engineering 的 career ladder 上，mid-level 到 senior 的跨越常常不是技術能力的差距——很多 mid-level 工程師的技術能力已經足夠。真正的差距在於：你能不能讓你的技術判斷有效地傳遞出去，讓對的人在對的時間做出對的決策。

當你的 incident report 讓 VP 在三十秒內決定要不要 escalate、當你的 cross-team request 在第一次就得到回應、當你的 design doc 讓 reviewer 在第一頁就 buy in 你的方向——這些都是 SCQA 能幫你做到的事。

它不是什麼高深的理論，就是四個步驟：先建立共識、再製造張力、然後讓讀者想知道答案、最後給出答案。

下次你發一則 Slack 訊息之前，花三十秒想一下：我的讀者看到第一句話的時候，知不知道我在說什麼、為什麼要在乎、以及他需要做什麼？

如果答案是「不知道」，重寫一次。用 SCQA。
