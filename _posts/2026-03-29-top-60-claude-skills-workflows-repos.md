---
title: "2026 年 60 大 AI 工具完整清單：Claude Skills、Workflows 與 GitHub Repos"
date: 2026-03-29
categories: [AI]
tags: [claude-code, skills, mcp, tools, github, agents, workflow, resources]
image:
  path: /assets/img/posts/2026-03-29-top-60-claude-skills-workflows-repos/banner.jpg
  alt: "A large modern library with many bookshelves representing a curated collection"
---

> 原文：[Top 60 Claude Skills, Workflows, and GitHub Repos for AI — The Complete List](https://x.com/eng_khairallah1/status/2037816689665147355) by **Khairallah AL-Awady** (@eng_khairallah1)
> 發佈日期：2026 年 3 月 28 日

---

我花了超過 100 小時測試 AI 工具，所以你不必這樣做。

把這個存起來。

2026 年的 AI 工具生態系令人不知所措。每週都有新的 framework，每天都有新的 agent，每天早上 GitHub 上都有新的 repo 在 trending。

大部分都是炒作。有些確實有用。少數幾個會從根本上改變你的工作方式。

我過濾了雜訊。以下是 60 個現在真正重要的工具——依類別整理，親自測試過，並附上對每個工具實際用途的誠實說明。

把這個加入書籤。你會回來看的。

## Part 1：AI Coding Agents 與 IDE

這些是讓 AI 代表你撰寫、review 和管理程式碼的工具。在真實工作流程中實際有用的——不只是 demo。

**01. Claude Code**
Anthropic 的命令列 coding agent。讀取檔案、撰寫程式碼、執行測試，直接在你的本地環境中操作。當你想要完全控制時，這是 AI 輔助開發的黃金標準。
🔗 https://docs.anthropic.com/en/docs/claude-code

**02. Cursor**
建立在 VS Code 上的 AI 優先程式碼編輯器。Inline 補全、與你的 codebase 對話、多檔案編輯。對於想要 AI 整合進現有工作流程的開發者來說，這是最好的編輯器。
🔗 https://www.cursor.com

**03. Codex CLI**
OpenAI 的終端 coding agent。接受自然語言指令、讀取你的 codebase、撰寫並執行程式碼。在多步驟實作任務上表現強勁。
🔗 https://github.com/openai/codex

**04. Windsurf**
Codeium 的 AI coding IDE。Cascade agent 用於多檔案編輯、深度 codebase 理解，以及心流狀態的 coding。成長快速。
🔗 https://codeium.com/windsurf

**05. Superpowers**
20+ 個經過實戰測試的 Claude Code skills。TDD、debugging、plan-to-execute pipelines。GitHub 上 96,000+ stars。如果你使用 Claude Code，先安裝這個。
🔗 https://github.com/obra/superpowers

**06. Spec Kit (GitHub)**
Spec 驅動開發。先寫規格，AI 從中生成程式碼。強迫你在動手之前先思考。50,000+ stars。
🔗 https://github.com/github/spec-kit

**07. Aider**
在終端機裡的 AI pair programming。支援任何 LLM。在與現有 codebase 合作方面表現強勁。30,000+ stars。
🔗 https://github.com/paul-gauthier/aider

## Part 2：Agent Frameworks

建立能夠思考、行動和迭代的自主系統。

**08. OpenClaw**
病毒式傳播的開源 AI agent。持久性、多頻道（WhatsApp、Telegram、Discord），自己撰寫 skill。210,000+ stars 且持續增長。個人 AI agent 最容易上手的入口點。
🔗 https://github.com/openclaw/openclaw

**09. LangGraph**
以程式碼形式表達的多 agent 協作。將 agent 建立為帶有分支邏輯、human-in-the-loop 和持久狀態的圖。26,000+ stars。
🔗 https://github.com/langchain-ai/langgraph

**10. CrewAI**
具有角色、目標和背景故事的多 agent framework。每個 agent 都有定義好的角色和責任。適合類似團隊的工作流程。
🔗 https://github.com/crewAIInc/crewAI

**11. AutoGPT**
長時間任務的全自主 agent 平台。OG agent framework。自早期以來已大幅成熟。
🔗 https://github.com/Significant-Gravitas/AutoGPT

**12. Dify**
開源 LLM 應用程式建構器。將 workflows、RAG、agents 和模型管理整合在一個平台中。適合想建立 AI 應用程式的非開發者。
🔗 https://github.com/langgenius/dify

**13. OWL**
多 agent 協作 framework。在 GAIA benchmark 上名列前茅。將最前沿的研究轉化為可用的程式碼。
🔗 https://github.com/camel-ai/owl

**14. CopilotKit**
將 AI copilot 直接嵌入 React 應用程式。在你的產品中發布 AI 功能，而不只是你的工作流程。
🔗 https://github.com/CopilotKit/CopilotKit

**15. pydantic-ai**
建立在 Pydantic 上的型別安全 agent framework。適合想要結構化、驗證過的 agent 輸出的 Python 開發者。
🔗 https://github.com/pydantic/pydantic-ai

## Part 3：MCP Servers 與工具整合

MCP（Model Context Protocol）讓 AI 能夠連接到外部世界。Skills 教它「如何做」；MCP 給它「存取權」。

**16. Tavily**
為 AI agent 打造的搜尋引擎。不是藍色連結——而是乾淨、結構化、LLM 友好的資料。四個工具：search、extract、crawl、map。一分鐘內連接為 remote MCP。
🔗 https://github.com/tavily-ai/tavily-mcp

**17. Context7**
將最新的函式庫文件注入你的 LLM context。不再有幻覺 API 或已棄用的方法。在你的 prompt 中加上「use context7」，它就會拉取當前文件。支援數千個函式庫。
🔗 https://github.com/upstash/context7

**18. Task Master AI**
你 AI 的專案經理。給它一份 PRD，它會生成帶有相依性的結構化任務。Claude 逐一執行它們。將混亂的 session 變成有組織的 pipeline。
🔗 https://github.com/eyaltoledano/claude-task-master

**19. MCP Playwright**
LLM 的瀏覽器自動化。透過自然語言控制真實瀏覽器。測試、爬蟲、互動。
🔗 https://github.com/executeautomation/mcp-playwright

**20. fastmcp**
用最少量的 Python 建立 MCP servers。為 Claude 或任何 MCP 相容模型創建自定義工具整合的最快方法。
🔗 https://github.com/jlowin/fastmcp

**21. markdownify-mcp**
將 PDF、圖片和音訊轉換為 Markdown。將任何文件類型注入你的 AI 工作流程。
🔗 https://github.com/zcaceres/markdownify-mcp

**22. MCPHub**
透過 HTTP 管理多個 MCP servers。所有工具連接的單一儀表板。
🔗 https://github.com/samanhappy/mcphub

## Part 4：Claude Skills 精選

Skills 教 Claude 專業化的工作流程。社群中有 80,000+ 個 skills。這些是值得安裝的。

**23. PDF Processing（官方）**
讀取、提取表格、填寫表單、合併和分割 PDF。對知識工作者來說效用最高的 skill。
🔗 https://github.com/anthropics/skills/tree/main/skills/pdf

**24. Frontend Design（官方）**
建立真實的設計系統、大膽的排版、生產級 UI。逃脫「AI 垃圾」的審美。277,000+ 次安裝。
🔗 https://github.com/anthropics/skills/tree/main/skills/frontend-design

**25. Skill Creator（官方）**
元 skill。用純英文描述一個工作流程，五分鐘後就能得到完整的 SKILL.md。無需撰寫任何設定就能建立新 skill。
🔗 https://github.com/anthropics/skills/tree/main/skills/skill-creator

**26. Marketing Skills by Corey Haines**
20+ 個 skills，涵蓋 CRO、文案、SEO、email 序列、成長策略。行銷團隊需要的一切，以 skill 形式呈現。
🔗 https://github.com/coreyhaines31/marketingskills

**27. Claude SEO**
全站稽核、schema 驗證、關鍵字分析。12 個子 skills，涵蓋完整的 SEO 工作流程。
🔗 https://github.com/AgriciDaniel/claude-seo

**28. Obsidian Skills**
由 Obsidian CEO 打造。自動標籤、自動連結、vault 原生操作。如果你使用 Obsidian，這是必備的。
🔗 https://github.com/kepano/obsidian-skills

**29. Context Optimization**
降低 token 成本並提升 KV-cache 效率。讓昂貴的 API 工作流程顯著變便宜。13,900+ stars。
🔗 https://github.com/muratcankoylan/agent-skills-for-context-engineering

**30. Deep Research Skill**
8 階段研究，附自動延續功能。當你需要 Claude 深入某個主題時使用——而不只是粗略瀏覽。
🔗 https://github.com/199-biotechnologies/claude-deep-research-skill

## Part 5：本地 AI 與模型執行

在自己的硬體上執行模型。隱私、速度、零 API 費用。

**31. Ollama**
用一個終端機指令在本地執行開源 LLM。支援 Llama、Mistral、Gemma 和數十個更多模型。從零到本地 AI 最快的路徑。
🔗 https://github.com/ollama/ollama

**32. Open WebUI**
自架的 ChatGPT 風格介面。乾淨、快速、功能齊全。與 Ollama 搭配可打造完全私人的 AI 設置。
🔗 https://github.com/open-webui/open-webui

**33. LlamaFile**
將整個 LLM 打包成單一可執行檔。零依賴。下載即可執行。簡單到荒謬的程度。
🔗 https://github.com/Mozilla-Ocho/llamafile

**34. Unsloth**
以 2 倍速度、70% 更少記憶體微調模型。如果你需要用自己的資料訓練自定義模型，從這裡開始。
🔗 https://github.com/unslothai/unsloth

**35. vLLM**
高吞吐量推論引擎。比一般服務快 2 到 4 倍。開源模型生產部署的標準。
🔗 https://github.com/vllm-project/vllm

## Part 6：Workflow 與自動化

將 AI 連接到你現有的工具和流程。

**36. n8n**
具有 400+ 整合和 AI 節點的開源工作流程自動化。可自架。AI 驅動自動化的最佳視覺化建構器。
🔗 https://github.com/n8n-io/n8n

**37. Langflow**
Agent pipeline 的視覺化拖放工具。140,000+ stars。無需寫程式碼即可建立複雜的 agent 工作流程。
🔗 https://github.com/langflow-ai/langflow

**38. Huginn**
用於監控、警報和資料收集的自架 web agent。在你自己伺服器上執行的隱私優先自動化。
🔗 https://github.com/huginn/huginn

**39. DSPy**
程式化（而非 prompt）基礎模型。史丹福研究轉化的 framework。當 prompting 不夠確定性時使用。
🔗 https://github.com/stanfordnlp/dspy

**40. Temporal**
長時間運行流程的持久化工作流程引擎。當你的自動化需要在崩潰、重試和超時中存活時使用。
🔗 https://github.com/temporalio/temporal

## Part 7：搜尋、資料與 RAG

將資訊輸入和輸出 AI 系統。

**41. GPT Researcher**
生成彙整報告的自主研究 agent。給它一個主題，得到附有來源的徹底分析。
🔗 https://github.com/assafelovic/gpt-researcher

**42. Firecrawl**
將任何網站轉換為 LLM 友好的資料。專為 AI pipeline 設計的網頁爬蟲。
🔗 https://github.com/mendableai/firecrawl

**43. Vanna AI**
自然語言轉 SQL。用英文提問，得到資料庫查詢。適合需要從資料庫取得資料但不想寫 SQL 的人。
🔗 https://github.com/vanna-ai/vanna

**44. Instructor**
使用 Pydantic 模型從任何 LLM 獲取結構化 JSON 輸出。支援 OpenAI、Anthropic、Google 和 15+ 個供應商。生產 AI 工程師實際使用的工具。
🔗 https://python.useinstructor.com

**45. Chroma**
開源向量資料庫。為你的 AI 應用程式添加語義搜尋和長期記憶的最簡單方法。
🔗 https://github.com/chroma-core/chroma

**46. dlt**
來自 5,000+ 來源的 LLM 原生資料 pipeline。從任何地方將資料取得到你的 AI 工作流程中。
🔗 https://github.com/dlt-hub/dlt

**47. ExtractThinker**
文件智慧的 ORM。從任何文件類型提取結構化資料。
🔗 https://github.com/enoch3712/ExtractThinker

## Part 8：API 與基礎架構

讓一切在生產環境中運作的管道。

**48. FastAPI**
用於服務 AI 應用程式的 Python web framework。卓越的文件。內建 Pydantic 驗證。
🔗 https://github.com/tiangolo/fastapi

**49. Portkey Gateway**
透過一個 API 路由請求到 250+ 個 LLM。無需更改程式碼即可切換模型。
🔗 https://github.com/Portkey-AI/gateway

**50. OmniRoute**
44+ 個 AI 供應商的 API proxy。負載均衡、fallback 和成本優化。
🔗 https://github.com/diegosouzapw/OmniRoute

**51. lmnr**
追蹤和評估 agent 行為。確切看到你的 agent 在做什麼，並測量它是否做得好。
🔗 https://github.com/lmnr-ai/lmnr

**52. Codebase Memory MCP**
將你的 codebase 轉換為持久的知識圖譜。Claude 在不同 session 間記住你的整個專案結構。
🔗 https://github.com/DeusData/codebase-memory-mcp

## Part 9：精選合集與學習資源

哪裡可以找到更多，以及如何持續學習。

**53. Awesome Claude Skills**
最佳精選 skill 清單。22,000+ stars。尋找新 skill 時從這裡開始。
🔗 https://github.com/travisvn/awesome-claude-skills

**54. Anthropic Skills Repo**
來自 Anthropic 的官方參考實作。skill 應該如何建立的黃金標準。
🔗 https://github.com/anthropics/skills

**55. Awesome Agents**
一個精選清單中的 100+ 個開源 agent 工具。
🔗 https://github.com/kyrolabs/awesome-agents

**56. PromptingGuide**
全面的 prompt 工程參考，涵蓋從基礎到進階 agent prompting 的每種技術。
🔗 https://www.promptingguide.ai

**57. Anthropic Prompt Engineering Tutorial**
9 章的實作練習，附 Jupyter notebooks。學習 prompting 最好的結構化方式。
🔗 https://github.com/anthropics/prompt-eng-interactive-tutorial

**58. SkillsMP**
擁有 80,000+ 社群 skills 的市集。發現 Claude skills 的最大目錄。
🔗 https://skillsmp.com

**59. MAGI//ARCHIVE**
每日新鮮 AI repos 的訊息流。掌握正在發布的最新動態。
🔗 https://tom-doerr.github.io/repo_posts/

**60. Anthropic Official Docs**
涵蓋 API、prompting 最佳實踐、tool use、agents 以及其他一切。在認真建構任何東西之前先從頭到尾讀過這份文件。
🔗 https://docs.anthropic.com

## 如何實際使用這份清單

不要試圖一次安裝所有 60 個工具。那是通往不知所措和浪費時間的配方。

以下是我推薦的順序：

**如果你是開發者：**
從 Claude Code（01）+ Superpowers（05）+ Context7（17）+ Tavily（16）開始。這給了你一個強大的 AI coding 設置，附帶搜尋和文件存取。

**如果你是創作者或知識工作者：**
從 OpenClaw（08）+ Obsidian Skills（28）+ PDF Processing（23）+ Frontend Design（24）開始。這給了你一個具備檔案管理、文件處理和內容創建能力的 AI 助手。

**如果你在建立產品：**
從 FastAPI（48）+ Instructor（44）+ Chroma（45）+ LangGraph（09）開始。這給了你後端 framework、結構化輸出、記憶體和 agent 協作的基礎，用於生產 AI 應用程式。

**如果你想學習：**
從 Anthropic Tutorial（57）+ PromptingGuide（56）+ Anthropic Docs（60）開始。在堆疊工具之前先打好基礎。

選一條路。深入鑽研。隨著需求增長再添加更多工具。

## TL;DR

- **Skills** = 教 AI「如何」把事情做得更好
- **MCP** = 給 AI「存取」外部工具和資料的權限
- **Repos** = 驅動一切的開源引擎

三者結合，你就擁有了一個真正強大的 AI 工作流程——不只是在 demo 中令人印象深刻。

就這樣。60 個工具。現在去建造些什麼吧。
