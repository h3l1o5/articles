---
title: "為 Agent 打造 CLI"
date: 2026-03-25
categories: [翻譯]
tags: [cli, agent, 開發工具]
image:
  path: /assets/img/posts/2026-03-25-building-clis-for-agents/banner.jpg
  alt: "dev agent --force"
---

> 原文：[Building CLIs for agents](https://x.com/ericzakariasson/status/2036762680401223946) by **eric zakariasson** (@ericzakariasson)
> 發佈日期：2026 年 3 月 25 日

---

如果你曾經看過 agent 嘗試使用 CLI，你一定見過它卡在一個無法回答的互動式提示上，或是去解析一個沒有範例的說明頁面。大多數 CLI 在設計時都假設坐在鍵盤前的是一個人類。以下是一些我發現能讓 CLI 更適合 agent 使用的做法：

## 讓它變成非互動式的

如果你的 CLI 在執行到一半時跳出提示，agent 就會卡住。它沒辦法按方向鍵，也沒辦法在正確的時機輸入「y」。每一個輸入都應該可以透過 flag 傳入。把互動模式保留為缺少 flag 時的備用方案，而不是主要路徑。

```bash
# 這會讓 agent 卡住
$ mycli deploy
? Which environment? (use arrow keys)

# 這樣才行
$ mycli deploy --env staging
```

## 不要一次傾倒所有文件

Agent 執行 `mycli`，看到子指令，選一個，再執行 `mycli deploy --help`，取得它需要的資訊。不會浪費 context 在它不會用到的指令上。讓它按需探索。

## 讓 --help 真正有用

每個子指令都要有 `--help`，而且每個 `--help` 都要包含範例。範例才是做最多事的部分。Agent 從 `mycli deploy --env staging --tag v1.2.3` 這樣的範例進行模式匹配，比閱讀描述文字快得多。

```
$ mycli deploy --help

Options:
  --env    目標環境 (staging, production)
  --tag    映像檔標籤 (預設: latest)
  --force  跳過確認

Examples:
  mycli deploy --env staging
  mycli deploy --env production --tag v1.2.3
  mycli deploy --env staging --force
```

## 所有東西都接受 flag 和 stdin

Agent 的思維方式是 pipeline。它們想要串接指令，在工具之間用 pipe 傳遞輸出。不要要求奇怪順序的位置參數，也不要在缺少值時退回互動式提示。

```bash
cat config.json | mycli config import --stdin
mycli deploy --env staging --tag $(mycli build --output tag-only)
```

## 快速失敗，並提供可操作的錯誤訊息

如果缺少必要的 flag，不要掛在那裡。立即報錯，並顯示正確的呼叫方式。當你給 agent 一些可以參考的東西時，它們很擅長自我修正。

```bash
Error: No image tag specified.

mycli deploy --env staging --tag <image-tag>

Available tags:
  mycli build list --output tags
```

## 讓指令具有冪等性

Agent 會不斷重試。網路逾時、任務中途失去 context。執行同一個 deploy 兩次應該回傳「已部署，無操作」，而不是建立一個重複的部署。

## 為破壞性操作加上 --dry-run

Agent 應該能夠在正式執行之前預覽 deploy 或刪除會做什麼。讓它們先驗證計畫，再真正執行。

```bash
$ mycli deploy --env production --tag v1.2.3 --dry-run

Would deploy v1.2.3 to production
- 停止 3 個執行中的實例
- 拉取映像檔 registry.io/app:v1.2.3
- 啟動 3 個新實例

No changes made.

$ mycli deploy --env production --tag v1.2.3
✓ Deployed v1.2.3 to production
```

## 用 --yes / --force 跳過確認

人類會看到「你確定嗎？」，而 agent 傳入 `--yes` 來跳過。讓安全路徑成為預設，但允許繞過。

## 可預測的指令結構

如果 agent 學會了 `mycli service list`，它應該能猜到 `mycli deploy list` 和 `mycli config list`。選定一個模式（資源 + 動詞）然後到處使用。

## 成功時回傳資料

顯示 deploy ID 和 URL。Emoji 很可愛，但其實不太需要。

```bash
deployed v1.2.3 to staging
url: https://staging.myapp.com
deploy_id: dep_abc123
duration: 34s
```

---

如果你正在打造 agent 會使用的 CLI，這些模式能省下大量除錯時間。大部分的工作其實只是把人類隱性理解的東西變得明確而已！

我相信還有很多我遺漏的，歡迎告訴我！
