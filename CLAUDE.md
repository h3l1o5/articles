# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Jekyll blog using the Chirpy theme, published at `https://h3l1o5.github.io/articles`. All articles are translated from external sources (X/Twitter posts, websites, or local files) into Traditional Chinese.

## Commands

- `./tools/run.sh` — local dev server with live reload
- `./tools/test.sh` — production build + HTML validation (html-proofer)

## Article Workflow

Articles are never written from scratch. The workflow is: source article → translate to Traditional Chinese → create `_posts/YYYY-MM-DD-slug.md`.

When the source is a URL, use `browser-use` (with `--profile "Default" --headed`) to fetch content and images.

### Front matter convention

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
