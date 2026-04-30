## Overview

Jekyll blog using the Chirpy theme, published at `https://h3l1o5.github.io/articles`. All articles are translated from external sources (X/Twitter posts, websites, or local files) into Traditional Chinese.

## Commands

- `./tools/run.sh` — local dev server with live reload
- `./tools/test.sh` — production build + HTML validation (html-proofer)

## Article Workflow

Articles are never written from scratch. The workflow is: source article → translate to Traditional Chinese → create `_posts/YYYY-MM-DD-slug.md`.

When the source is a URL, use **Claude-in-Chrome** (MCP tools `mcp__claude-in-chrome__*`) to fetch content and images. Use `--headed` only when explicitly asked.

**Claude-in-Chrome notes**: Always call `tabs_create_mcp` to create a new tab — never reuse existing tabs that may belong to other agents. When checking tabs context, verify no other agents are actively using the tab group before proceeding.

### Source-specific fetching strategies

- **X/Twitter posts**: Must use browser (browser-use or Claude-in-Chrome). `WebFetch` returns 402 errors for X URLs. For X Articles (long-form), navigate to the article focus mode URL (`/article/` path) and scroll through the entire article to find all lazy-loaded images.
- **GitHub Gists**: Use `WebFetch` directly — no browser needed. More efficient and avoids interfering with other browser sessions.
- **General websites**: Try `WebFetch` first; fall back to browser if it fails.

### Image discovery

Do NOT rely solely on JavaScript DOM queries to find images. X Articles use lazy loading and complex DOM structures. The reliable approach:
1. Navigate to the article focus mode URL
2. Take screenshots and scroll through the entire article
3. Use JS to collect `pbs.twimg.com/media` image base URLs (strip query params to avoid cookie-related blocks)
4. Download with `curl` using `?format=jpg&name=large` suffix

### Front matter convention

The `date` field and filename use the date we create the article (UTC+8), not the original publication date — so new articles always sort to the top.

```yaml
---
title: "中文翻譯標題"
date: YYYY-MM-DD
categories: [AI]
tags: [tag1, tag2]
image:
  path: /assets/img/posts/YYYY-MM-DD-slug/banner.jpg
  alt: "English alt text from original"
---
```

Immediately after front matter, add the source attribution:

```markdown
> 原文：[Original Title](URL) by **Author** (@handle)
> 發佈日期：YYYY 年 M 月 D 日

---
```

### Translation style

- Translate to Traditional Chinese, but keep technical terms in English (e.g., Claude Code, CLAUDE.md, hooks, skills, worktree, PR)
- Download all images from the source article — do not link to external URLs

### Images

Store in `/assets/img/posts/YYYY-MM-DD-slug/`. In Markdown, use paths **without** the baseurl — Chirpy adds `/articles` automatically:

```markdown
![alt](/assets/img/posts/YYYY-MM-DD-slug/image.jpg)
```

### Categories and tags

Use meaningful topic categories (e.g., `AI`, `DevOps`). Do not use categories like "翻譯" — every article in this project is a translation by definition.

### After completion

Commit with message format `Add article: <English title>` and push immediately. Do not start the dev server unless explicitly asked.

After pushing, poll CI status every 1 minute (`gh run list --repo h3l1o5/articles --limit 1`) until the run completes. Report the result to the user.
