---
title: "長時間運行應用程式的 Harness 設計"
date: 2026-03-29
categories: [AI]
tags: [ai-engineering, harness, agents, anthropic, claude, frontend-design, multi-agent]
image:
  path: /assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/banner.png
  alt: "Abstract geometric pattern representing harness design"
---

> 原文：[Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps) by **Prithvi Rajasekaran**
> 發佈日期：2026 年 3 月 24 日

---

過去幾個月，我一直在研究兩個相互關聯的問題：如何讓 Claude 產出高品質的 frontend 設計，以及如何讓它在無需人工介入的情況下完整建構應用程式。這項工作源自我們更早期在 frontend 設計 skill 和長時間運行的 coding agent harness 上的探索——我和同事透過 prompt engineering 與 harness 設計，成功將 Claude 的表現大幅提升到超越基準線——但兩者最終都遇到了瓶頸。

為了突破這個天花板，我開始探索新穎的 AI 工程方法，希望在兩個截然不同的領域都能奏效：一個由主觀品味定義，另一個則以可驗證的正確性和可用性為標準。受到生成對抗網路（GAN）的啟發，我設計了一個包含生成器（generator）與評估器（evaluator）agent 的多 agent 架構。要建構一個能可靠評分、且具備品味的 evaluator，首先必須開發出一套評分標準，將「這個設計好嗎？」這類主觀判斷轉化為具體、可評分的條件。

接著，我將這些技術應用到長時間運行的自主 coding 上，並從早期 harness 工作中帶入兩個心得：將建構過程拆解為可處理的區塊，以及使用結構化 artifact 在 session 之間傳遞 context。最終成果是一個由規劃者（planner）、生成器（generator）和評估器（evaluator）組成的三 agent 架構，能在長達數小時的自主 coding session 中產出完整的 full-stack 應用程式。

## 為何直觀的實作往往不夠用

我們先前已證明，harness 設計對長時間 agentic coding 的效果有顯著影響。在早期的實驗中，我們使用一個初始化器（initializer）agent 將產品規格拆解成任務清單，再讓 coding agent 一次實作一個功能，並在 session 之間透過 artifact 傳遞 context。更廣泛的開發者社群也得出了類似的結論，例如「Ralph Wiggum」方法就是透過 hooks 或腳本讓 agent 保持持續的迭代循環。

但有些問題依然持續存在。對於更複雜的任務，agent 還是容易隨著時間推移而偏離軌道。兩種常見的失敗模式：

第一，隨著 context window 被填滿，模型在長時間任務中往往會失去連貫性。有些模型還會出現「context 焦慮」的現象——當它們認為自己接近 context 上限時，會開始提前收尾。Context reset——完全清除 context window 並啟動一個全新的 agent，同時搭配結構化的交接（handoff），將前一個 agent 的狀態和下一步驟傳遞下去——能同時解決這兩個問題。

這與 compaction 不同。Compaction 是將對話的早期部分在原地進行摘要，讓同一個 agent 能在縮短的歷史記錄上繼續工作。雖然 compaction 保留了連續性，但它並不給 agent 一個乾淨的起點，這意味著 context 焦慮仍可能持續存在。Reset 則提供了一個乾淨的起點，代價是交接 artifact 必須包含足夠的狀態，讓下一個 agent 能順利接手工作。在我們早期的測試中，Claude Sonnet 4.5 的 context 焦慮問題嚴重到單靠 compaction 無法支撐強健的長任務表現，因此 context reset 成為 harness 設計的關鍵要素。

第二個問題是自我評估。當被要求評估自己產出的工作時，agent 傾向於自信地讚揚——即便在人類觀察者看來，品質明顯平庸。這個問題在設計等主觀任務上尤為突出，因為這類任務沒有像可驗證的軟體測試那樣的二元判斷。

將執行工作的 agent 與判斷工作品質的 agent 分離，被證明是解決這個問題的有力手段。光靠這種分離並不能立即消除寬鬆的傾向——evaluator 仍然是一個傾向對 LLM 產出寬容的 LLM。但將獨立的 evaluator 調整為持懷疑態度，遠比讓 generator 對自身工作保持批判性更容易實現。

## Frontend 設計：讓主觀品質變得可評分

我從 frontend 設計開始實驗，因為自我評估的問題在這裡最為明顯。在沒有任何干預的情況下，Claude 通常傾向於安全、可預測的版面，技術上可用，但視覺上毫無亮點。

兩個洞察塑造了我為 frontend 設計建構的 harness。首先，美感可以透過編碼設計原則與偏好的評分標準來提升。「這個設計美嗎？」很難一致地回答，但「這是否符合我們對好設計的原則？」就給了 Claude 具體的評分依據。其次，透過將 frontend 生成與 frontend 評分分離，我們可以建立一個驅動 generator 朝更強輸出前進的回饋迴路。

基於此，我制定了四個同時提供給 generator 和 evaluator 的評分標準：

- **設計品質（Design quality）**：這個設計整體看起來是一個連貫的整體，還是一堆零件的拼湊？優秀的作品意味著顏色、字體、版面、圖像和其他細節共同創造出鮮明的情緒與識別感。
- **原創性（Originality）**：是否有客製化決策的跡象，或者只是模板版面、套件預設值和 AI 生成的模式？未修改的現成元件——或像白底卡片配紫色漸層這類 AI 生成的明顯特徵——在此項不及格。
- **工藝（Craft）**：技術執行層面：字體層次、間距一致性、色彩和諧、對比度。這是能力檢查，而非創意檢查。
- **功能性（Functionality）**：獨立於美感的可用性。使用者能否理解介面的用途、找到主要操作，並在不猜測的情況下完成任務？

我將設計品質和原創性的權重置於工藝和功能性之上。Claude 在工藝和功能性方面本來就表現不錯，但在設計和原創性上，Claude 常常產出充其量只是乏味的輸出。這些標準明確懲罰高度通用的「AI slop」模式，推動模型進行更具美感冒險性的嘗試。

我以 Claude Agent SDK 建構了這個迴路。Generator agent 首先根據使用者的 prompt 建立一個 HTML/CSS/JS frontend。我給 evaluator 配備了 Playwright MCP，讓它在評分每個標準並撰寫詳細評語之前，能直接與正在運行的頁面互動。這些回饋作為下一次迭代的輸入流回給 generator。每次生成我執行 5 到 15 次迭代，每次迭代通常都會將 generator 推向更具特色的方向。完整的運行時間長達四小時。

有一個值得一提的例子：我提示模型為一家荷蘭藝術博物館建立網站。到第九次迭代時，它產出了一個乾淨的深色主題登陸頁，供一個虛構的博物館使用。然後，在第十個循環，它完全拋棄了這個方案，重新構想網站為一個空間體驗：一個以 CSS perspective 渲染的棋盤格地板 3D 房間，藝術品以自由位置懸掛在牆上，並透過門道式導覽在展廳之間穿梭。這是我以前從未在單次生成中見過的創意飛躍。

## 擴展到 Full-stack Coding

有了這些發現，我將這個受 GAN 啟發的模式應用到 full-stack 開發中。Generator-evaluator 迴路自然地對應到軟體開發生命週期，其中 code review 和 QA 扮演著與設計 evaluator 相同的結構性角色。

### 架構

在我們早期的長時間 harness 中，我們透過初始化器 agent、一次處理一個功能的 coding agent，以及 session 之間的 context reset，解決了多 session 連貫 coding 的問題。Opus 4.6 在很大程度上自行消除了 context 焦慮的行為，因此我能夠完全從這個 harness 中移除 context reset。所有 agent 在整個建構過程中作為一個連續的 session 運行，由 Claude Agent SDK 的自動 compaction 處理 context 增長。

我建構了一個三 agent 系統：

**Planner**：接收一個 1 到 4 句的簡短 prompt，並將其擴展為完整的產品規格。被提示在範圍上保持野心，專注於產品 context 和高層次的技術設計，而非詳細的技術實作——以避免過度指定細節所導致的級聯錯誤。

**Generator**：以 sprint 方式工作，每次從規格中挑選一個功能。每個 sprint 使用 React、Vite、FastAPI 和 SQLite stack 實作應用程式，在每個 sprint 結束時自我評估，然後交接給 QA。

**Evaluator**：使用 Playwright MCP 像使用者一樣點擊瀏覽正在運行的應用程式，測試 UI 功能、API endpoint 和資料庫狀態。根據發現的 bug 以及涵蓋產品深度、功能性、視覺設計和程式碼品質的一系列標準，對每個 sprint 進行評分。每個標準都有一個硬性閾值——如果任何一項低於閾值，sprint 就失敗，generator 會收到詳細的回饋。

在每個 sprint 開始之前，generator 和 evaluator 協商一份 sprint contract：在任何程式碼撰寫之前，先就「完成」的樣子達成共識。溝通透過檔案處理：一個 agent 寫入一個檔案，另一個讀取並回應。

### 執行 Harness

我使用了以下 prompt 來生成一個復古電玩遊戲製作工具：

> Create a 2D retro game maker with features including a level editor, sprite editor, entity behaviors, and a playable test mode.

| Harness | 執行時間 | 費用 |
|---------|----------|------|
| Solo | 20 分鐘 | $9 |
| Full harness | 6 小時 | $200 |

Full harness 的費用是 solo 的 20 倍以上，但輸出品質的差異立竿見影。

**Solo harness 結果** — 初始應用程式看起來似乎可以運行，但問題很快就浮現了。版面浪費了大量空間。工作流程僵硬。最關鍵的是，實際的遊戲壞掉了：實體出現在螢幕上，但沒有任何東西回應輸入。實體定義與遊戲執行環境之間的連接斷了。

![Solo harness: opening screen](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/solo-screen.png)

![Solo harness: sprite editor](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/solo-sprite.png)

![Solo harness: gameplay](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/solo-gameplay.png)

**Full harness 結果** — 這次執行從同一個一句話的 prompt 開始，但 planner 步驟將其擴展為橫跨十個 sprint 的 16 功能規格。除了核心的編輯器和遊玩模式之外，規格還要求 sprite 動畫系統、行為模板、音效和音樂、AI 輔助的 sprite 生成器和關卡設計師，以及帶有分享連結的遊戲匯出功能。

應用程式立刻展現出更多的精緻感和流暢度。Canvas 使用了完整的視口，面板尺寸合理，介面具有一致的視覺識別感。Sprite 編輯器更豐富、功能更完整，配備了更清晰的工具面板、更好的顏色選擇器和更好用的縮放控制項。應用程式還內建了 Claude 整合，讓我可以透過 prompting 生成遊戲的不同部分。

最大的差異在於遊玩模式：我實際上能夠移動我的實體並玩這個遊戲。

![Full harness: opening screen](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/harness-screen.png)

![Full harness: sprite editor](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/harness-sprite.png)

![Full harness: AI game design](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/harness-ai-design.png)

![Full harness: gameplay](/assets/img/posts/2026-03-29-anthropic-harness-design-long-running-apps/harness-gameplay.png)

Evaluator 讓實作與規格保持一致。每個 sprint，它都會逐一檢查 sprint contract 的測試標準，並透過 Playwright 操作正在運行的應用程式。這些 contract 非常細緻——光是 Sprint 3 就有 27 個涵蓋關卡編輯器的標準。以下是 evaluator 發現的一些問題範例：

| Contract 標準 | Evaluator 發現 |
|--------------------|-------------------|
| 矩形填充工具允許點擊拖動來填充矩形區域 | FAIL — 工具只在拖動的起始/結束點放置 tile，而非填充整個區域 |
| 使用者可以選取並刪除已放置的實體生成點 | FAIL — Delete 鍵處理器同時需要 selection 和 selectedEntityId，但點擊只設定了 selectedEntityId |
| 使用者可以透過 API 重新排序動畫幀 | FAIL — PUT /frames/reorder 路由定義在 /{frame_id} 路由之後；FastAPI 將 'reorder' 匹配為整數 |

要讓 evaluator 達到這個水準需要花費不少功夫。開箱即用的情況下，Claude 是個糟糕的 QA agent。在早期的執行中，我看著它發現了合理的問題，然後又說服自己這些問題其實無關緊要。

### 迭代 Harness

最初的結果令人振奮，但架構龐大且昂貴。Harness 中的每個組件都編碼了一個關於模型自身無法做到什麼的假設——這些假設值得壓力測試，既因為它們可能是錯誤的，也因為隨著模型改進，它們可能很快就過時。我們的部落格文章「Building Effective Agents」將此定義為「找到盡可能簡單的解決方案，只有在需要時才增加複雜度」。

我完全移除了 sprint 架構。Opus 4.6 在很大程度上自行消除了 context 焦慮，我也能夠放棄 context reset。所有 agent 作為一個連續的 session 運行，搭配自動 compaction。

為了測試更新後的 harness，我使用了以下 prompt：

> Build a fully featured DAW in the browser using the Web Audio API.

| Agent 與階段 | 執行時間 | 費用 |
|---------------|----------|------|
| Planner | 4.7 分鐘 | $0.46 |
| Build（第 1 輪） | 2 小時 7 分鐘 | $71.08 |
| QA（第 1 輪） | 8.8 分鐘 | $3.24 |
| Build（第 2 輪） | 1 小時 2 分鐘 | $36.89 |
| QA（第 2 輪） | 6.8 分鐘 | $3.09 |
| Build（第 3 輪） | 10.9 分鐘 | $5.88 |
| QA（第 3 輪） | 9.6 分鐘 | $4.06 |
| **總計** | **3 小時 50 分鐘** | **$124.70** |

QA agent 仍然發現了真實的缺口。在第一輪回饋中：

> This is a strong app with excellent design fidelity, solid AI agent, and good backend. The main failure point is Feature Completeness — while the app looks impressive, several core DAW features are display-only without interactive depth: clips can't be dragged/moved on the timeline, there are no instrument UI panels, and no visual effect editors.

最終的應用程式具備了一個完整音樂製作程式的所有核心功能：可運作的編曲視圖、混音器和傳輸控制。除此之外，我還能夠完全透過 prompting 完成一段短曲：agent 設定了速度和調性、鋪下旋律、建立鼓軌、調整混音器電平，並加入殘響效果。

## 接下來的方向

隨著模型持續改進，我們大致可以預期它們能夠工作更長時間，並處理更複雜的任務。在某些情況下，圍繞模型的 scaffold 隨著時間推移變得不那麼重要。另一方面，模型越好，就越有空間開發能夠完成超越模型基準能力的複雜任務的 harness。

幾個值得銘記的心得：

- 始終針對你正在開發的模型進行實驗，在現實問題上閱讀它的 trace，並調整它的表現。
- 在處理複雜任務時，有時可以透過拆解任務並對每個方面應用專門的 agent 來獲得更大的空間。
- 當新模型發布時，重新審視 harness——剝除不再是承重結構的部分，添加新部分以實現更大的能力。

隨著模型改進，有趣的 harness 組合空間並不會縮小。它會移動。對 AI 工程師來說，有趣的工作是持續找到下一個新穎的組合。
