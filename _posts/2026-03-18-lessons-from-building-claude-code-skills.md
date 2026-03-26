---
title: "打造 Claude Code 的經驗談：我們如何運用 Skills"
date: 2026-03-18
categories: [DevTools]
tags: [claude-code, skills, agent, workflow]
image:
  path: /assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/01-header.jpg
  alt: "Lessons from Building Claude Code Skills"
---

> 原文：[Lessons from Building Claude Code: How We Use Skills](https://x.com/trq212/status/2033949937936085378) by **Thariq** (@trq212)
> 發佈日期：2026 年 3 月 18 日

---

Skills 已經成為 Claude Code 中最常被使用的擴充機制之一。它們靈活、容易製作，也便於分享。

但正因為這份靈活性，反而讓人不確定怎麼做才是最好的。哪些類型的 skill 值得做？寫出一個好 skill 的訣竅是什麼？什麼時候該把它分享給其他人？

我們在 Anthropic 內部大量使用 Claude Code 的 skills，目前有數百個 skill 正在日常使用中。以下是我們在利用 skills 加速開發過程中所學到的經驗。

## 什麼是 Skills？

如果你剛接觸 skills，建議先[閱讀我們的文件](https://code.claude.com/docs/en/skills)或觀看我們最新的 [Skilljar Agent Skills 課程](https://anthropic.skilljar.com/introduction-to-agent-skills)，這篇文章會假設你已經對 skills 有一定的了解。

一個常見的誤解是，skills「不過就是 markdown 檔案」。但 skills 最有趣的地方，恰恰在於它們不只是文字檔——它們是資料夾，裡面可以包含腳本、素材、資料等等，讓 agent 自行探索、發掘並加以運用。

在 Claude Code 中，skills 還擁有[豐富的設定選項](https://code.claude.com/docs/en/skills#frontmatter-reference)，包括動態註冊 hooks。

我們發現，許多最有意思的 skills 正是巧妙地運用了這些設定選項和資料夾結構。

# Skills 的類型

在盤點了我們所有的 skills 之後，我們注意到它們大致可以歸納為幾個反覆出現的類別。好的 skill 通常明確屬於其中一類；而令人困惑的 skill 則往往橫跨多個類別。這不是一份定義性的清單，但當你想檢視組織內是否有所遺漏時，它是很好的思考框架。

![Skills 的類型](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/02-types-of-skills.jpg)

## 1. 函式庫與 API 參考

說明如何正確使用某個函式庫、CLI 或 SDK 的 skills。這些可以是內部函式庫，也可以是 Claude Code 偶爾會用錯的常見函式庫。這類 skill 通常會包含一個參考程式碼片段的資料夾，以及一份 Claude 在撰寫腳本時應避免的常見陷阱清單。

範例：
- billing-lib — 你的內部計費函式庫：邊界情況、容易踩坑之處等
- internal-platform-cli — 內部 CLI 封裝的每個子指令，附帶使用時機的說明
- frontend-design — 讓 Claude 更善於運用你的設計系統

## 2. 產品驗證

描述如何測試或驗證程式碼是否正常運作的 skills。這類 skill 通常會搭配外部工具，如 Playwright、tmux 等來執行驗證。

驗證型 skills 對於確保 Claude 的產出正確性極為有用。讓一位工程師花一整週專門打磨你的驗證型 skills，絕對是值得的投資。

可以考慮的技巧包括：讓 Claude 錄下它的操作過程以便事後查看實際測了什麼，或是在每個步驟強制執行程式化的狀態斷言。這些通常是透過在 skill 中附帶多個腳本來實現的。

範例：
- signup-flow-driver — 在 headless 瀏覽器中跑完「註冊 → 信箱驗證 → 新手引導」的完整流程，每一步都有狀態斷言的 hook
- checkout-verifier — 使用 Stripe 測試卡驅動結帳 UI，驗證發票確實進入正確狀態
- tmux-cli-driver — 用於需要 TTY 的互動式 CLI 測試

## 3. 資料擷取與分析

連接你的資料源與監控系統的 skills。這類 skill 可能會包含帶有認證資訊的資料擷取函式庫、特定的 dashboard ID 等，以及常見工作流程或取得資料的方法說明。

範例：
- funnel-query — 「要 join 哪些事件才能看到註冊 → 啟用 → 付費的轉換」，加上那張真正存有標準 user_id 的表
- cohort-compare — 比較兩個使用者群組的留存率或轉換率，標記統計顯著的差異，並附上群組定義的連結
- grafana — 資料源 UID、叢集名稱、問題 → dashboard 對照表

## 4. 業務流程與團隊自動化

將重複性工作流程自動化為一道指令的 skills。這類 skill 的指示內容通常相當簡單，但可能會依賴其他 skills 或 MCP。對於這類 skill，將先前的執行結果保存在日誌檔中，有助於模型保持一致性，並能回顧過去的執行狀況。

範例：
- standup-post — 彙整你的 ticket tracker、GitHub 動態和之前的 Slack 訊息 → 格式化的站會報告，僅顯示差異
- create-\<ticket-system\>-ticket — 強制遵循 schema（合法的列舉值、必填欄位），加上建立後的工作流程（通知 reviewer、在 Slack 張貼連結）
- weekly-recap — 已合併的 PR + 已關閉的 ticket + 部署紀錄 → 格式化的週報

## 5. 程式碼腳手架與模板

為程式碼庫中特定功能生成框架樣板的 skills。你可以將這些 skills 與可組合的腳本搭配使用。當你的腳手架包含無法純粹用程式碼涵蓋的自然語言需求時，這類 skill 特別有用。

範例：
- new-\<framework\>-workflow — 依照你的標註搭建新的 service/workflow/handler
- new-migration — 你的資料庫遷移檔案模板，附帶常見陷阱
- create-app — 新的內部應用程式，預先接好認證、日誌和部署設定

## 6. 程式碼品質與審查

在組織內強化程式碼品質並輔助程式碼審查的 skills。這類 skill 可以包含確定性的腳本或工具以達到最大的穩健性。你可能會想透過 hooks 或 GitHub Action 來自動執行這些 skills。

範例：
- adversarial-review — 啟動一個全新視角的子 agent 來挑毛病，實施修正，反覆迭代直到發現的問題退化為瑣碎的吹毛求疵
- code-style — 強制程式碼風格，特別是 Claude 預設表現不佳的風格
- testing-practices — 關於如何撰寫測試以及該測試什麼的指引

## 7. CI/CD 與部署

協助你在程式碼庫中拉取、推送和部署程式碼的 skills。這類 skill 可能會引用其他 skills 來收集資料。

範例：
- babysit-pr — 監控一個 PR → 重試不穩定的 CI → 解決合併衝突 → 啟用自動合併
- deploy-\<service\> — 建置 → 冒煙測試 → 逐步導流並比對錯誤率 → 偵測到退化時自動回滾
- cherry-pick-prod — 隔離的 worktree → cherry-pick → 衝突解決 → 使用模板建立 PR

## 8. 運維手冊

接收一個症狀（例如 Slack 討論串、告警或錯誤特徵），走過一套多工具的調查流程，最終產出結構化報告的 skills。

範例：
- \<service\>-debugging — 將症狀對應到工具與查詢模式，適用於你流量最高的服務
- oncall-runner — 擷取告警 → 檢查常見嫌疑項 → 輸出格式化的調查結果
- log-correlator — 給定一個 request ID，從所有可能經手的系統中拉取對應的日誌

## 9. 基礎設施運維

執行例行維護和運維程序的 skills——其中部分涉及破壞性操作，因此需要加上防護機制。這些 skills 讓工程師在進行關鍵操作時更容易遵循最佳實踐。

範例：
- \<resource\>-orphans — 找出孤立的 pod/volume → 張貼到 Slack → 觀察期 → 使用者確認 → 級聯清理
- dependency-management — 你組織內的相依套件審批流程
- cost-investigation — 「為什麼我們的儲存/出口流量帳單暴增」，附帶具體的 bucket 名稱和查詢模式

# 製作 Skills 的技巧

![製作 Skills 的技巧](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/03-tips-header.jpg)

當你決定好要做哪個 skill 之後，該怎麼寫？以下是我們發現的一些最佳實踐、技巧和訣竅。

我們最近也發佈了 [Skill Creator](https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills)，讓你在 Claude Code 中更容易建立 skills。

## 不要陳述顯而易見的事

Claude Code 對你的程式碼庫已有相當多的了解，Claude 本身對程式設計也懂很多，包括許多預設的觀點。如果你要發佈的 skill 主要是知識性的，請盡量聚焦在那些能把 Claude 推出其慣常思維模式的資訊上。

[frontend design skill](https://github.com/anthropics/skills/blob/main/skills/frontend-design/SKILL.md) 就是一個很好的例子——它是由 Anthropic 的一位工程師在與客戶反覆迭代的過程中打造的，目的是提升 Claude 的設計品味，避免那些老套的模式，例如 Inter 字體和紫色漸層。

## 建立常見陷阱區段

![常見陷阱區段](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/04-gotchas.jpg)

任何 skill 中訊號密度最高的內容就是「常見陷阱」（Gotchas）區段。這些區段應該從 Claude 在使用你的 skill 時遇到的常見失敗點逐步累積而成。理想情況下，你會隨著時間持續更新你的 skill 來捕捉這些陷阱。

## 善用檔案系統與漸進式揭露

![檔案系統與漸進式揭露](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/05-file-system.jpg)

如同前面所說，skill 是一個資料夾，不只是一個 markdown 檔案。你應該把整個檔案系統視為上下文工程和漸進式揭露的一種形式。告訴 Claude 你的 skill 中有哪些檔案，它會在適當的時機去讀取。

最簡單的漸進式揭露形式是指向其他 markdown 檔案供 Claude 使用。例如，你可以把詳細的函式簽名和使用範例拆分到 `references/api.md` 中。

另一個例子：如果你的最終產出是一個 markdown 檔案，你可以在 `assets/` 中放一個模板檔讓 Claude 複製使用。

你可以建立 references、scripts、examples 等資料夾，幫助 Claude 更有效率地工作。

## 避免過度限制 Claude

![避免過度限制 Claude](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/06-avoid-railroading.jpg)

Claude 通常會努力遵循你的指示，而因為 skills 具有高度的重複使用性，你要小心不要在指示中寫得過於具體。給 Claude 它需要的資訊，但同時保留讓它因地制宜的彈性。

## 仔細規劃初始設定

![仔細規劃初始設定](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/07-setup.jpg)

有些 skills 可能需要先從使用者那裡取得上下文才能運作。例如，如果你做了一個把站會內容發到 Slack 的 skill，你可能會希望 Claude 先詢問要發到哪個頻道。

一個好的模式是將這些設定資訊存在 skill 目錄下的 `config.json` 檔案中，如上圖所示。如果設定尚未完成，agent 就會主動向使用者詢問所需資訊。

如果你希望 agent 呈現結構化的多選問題，可以指示 Claude 使用 AskUserQuestion 工具。

## description 欄位是給模型看的

![description 欄位](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/08-description-field.jpg)

當 Claude Code 啟動一個 session 時，它會建立一份所有可用 skills 及其 description 的清單。Claude 掃描這份清單來決定「有沒有適合這個請求的 skill？」這意味著 description 欄位不是摘要——而是對「何時應該觸發這個 skill」的描述。

## 記憶與資料儲存

![記憶與資料儲存](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/09-memory.jpg)

有些 skills 可以透過在內部儲存資料來實現一種記憶機制。你可以用最簡單的方式儲存，像是純文字附加日誌或 JSON 檔案，也可以用 SQLite 資料庫這樣複雜的方式。

例如，一個 standup-post skill 可以保留一個 `standups.log`，記錄它寫過的每一篇站會報告。這樣下次執行時，Claude 會讀取自己的歷史紀錄，就能告訴你從昨天以來有什麼變化。

儲存在 skill 目錄中的資料可能會在你升級 skill 時被刪除，因此你應該將資料存放在穩定的資料夾中。目前我們提供了 `$CLAUDE_PLUGIN_DATA` 作為每個 plugin 專屬的穩定儲存路徑。

## 內建腳本與動態生成程式碼

你能給 Claude 最強大的工具之一就是程式碼。提供腳本和函式庫給 Claude，能讓它把精力花在組合與決策上，而非重複建構樣板程式碼。

例如，在你的資料科學 skill 中，你可能會準備一組從事件來源擷取資料的工具函式。為了讓 Claude 能進行複雜的分析，你可以提供一組輔助函式。

![腳本與輔助函式](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/10-scripts-helpers.jpg)

接著 Claude 就能即時生成腳本來組合這些功能，針對「星期二發生了什麼事？」這類提問做更深入的分析。

![腳本組合](/assets/img/posts/2026-03-18-lessons-from-building-claude-code-skills/11-scripts-compose.jpg)

## 隨需啟用的 Hooks

Skills 可以包含只在被呼叫時才啟用、並持續到 session 結束的 hooks。這適合用來放那些帶有強烈主觀性、你不想隨時運行、但在特定情境下極為有用的 hooks。

範例：
- /careful — 透過 PreToolUse matcher 攔截 Bash 中的 `rm -rf`、`DROP TABLE`、force-push、`kubectl delete`。只有在你知道自己正在操作正式環境時才需要——如果全程啟用會讓你抓狂
- /freeze — 攔截任何不在指定目錄內的 Edit/Write。在除錯時特別實用：「我只想加 log，但 Claude 老是順手『修正』不相關的程式碼」

# 分享 Skills

Skills 最大的好處之一是你可以把它們分享給團隊。

分享 skills 有兩種方式：
- 將 skills 簽入你的 repo（放在 `.claude/skills` 下）
- 製作成 plugin，上架到 Claude Code Plugin marketplace，讓使用者可以自行安裝（詳見[文件](https://code.claude.com/docs/en/plugin-marketplaces)）

對於規模較小、只維護少數 repo 的團隊，把 skills 簽入 repo 就很好用。但每一個簽入的 skill 都會為模型增加一點上下文。隨著規模成長，內部 plugin marketplace 能讓你分發 skills，同時讓團隊成員自行決定要安裝哪些。

## 管理 Marketplace

如何決定哪些 skills 該上架到 marketplace？大家怎麼提交？

我們並沒有一個中央團隊來做決定，而是讓最有用的 skills 自然浮現。如果你有一個想讓大家試用的 skill，你可以把它上傳到 GitHub 的 sandbox 資料夾，然後在 Slack 或其他論壇分享連結。

當一個 skill 獲得了足夠的關注（由 skill 擁有者自行判斷），他們就可以提 PR 把它移入 marketplace。

提醒一點：建立品質不佳或重複的 skills 其實很容易，因此在正式發佈前確保有一定的篩選機制是很重要的。

## 組合 Skills

你可能會希望 skills 之間能互相依賴。例如，你可能有一個檔案上傳 skill 負責上傳檔案，還有一個 CSV 生成 skill 負責製作 CSV 然後上傳。這種相依管理目前尚未原生內建於 marketplace 或 skills 中，但你可以直接在 skill 中以名稱引用其他 skills，只要它們已被安裝，模型就會自動呼叫它們。

## 衡量 Skills 的成效

為了了解一個 skill 的使用狀況，我們使用 PreToolUse hook 來記錄公司內部的 skill 使用情形（[範例程式碼在此](https://gist.github.com/ThariqS/24defad423d701746e23dc19aace4de5)）。透過這種方式，我們可以找出哪些 skills 很受歡迎，或是哪些 skills 的觸發頻率低於預期。

# 結語

Skills 是極為強大且靈活的 agent 工具，但現在仍處於早期階段，我們都還在摸索如何把它們用到最好。

與其把這篇文章當成權威指南，不如把它視為一袋我們驗證過有效的實用技巧。理解 skills 最好的方式就是動手開始做、不斷實驗、看看什麼對你有用。我們大多數的 skills 一開始都只有幾行文字和一條注意事項，後來因為大家在 Claude 遇到新的邊界情況時持續補充，才逐漸變得完善。

希望這篇文章對你有幫助，如果有任何問題歡迎提出。
