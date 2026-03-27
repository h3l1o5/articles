# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Jekyll blog using the Chirpy theme, published at `https://h3l1o5.github.io/articles`. All articles are translated from external sources (X/Twitter posts, websites, or local files) into Traditional Chinese.

## Commands

- `./tools/run.sh` — local dev server with live reload
- `./tools/test.sh` — production build + HTML validation (html-proofer)

## Article Workflow

Articles are never written from scratch. The workflow is: source article → translate to Traditional Chinese → create `_posts/YYYY-MM-DD-slug.md`.

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

### Images

Store in `/assets/img/posts/YYYY-MM-DD-slug/`. When referencing images inside Markdown, prefix with `/articles` (the site's baseurl):

```markdown
![alt](/articles/assets/img/posts/YYYY-MM-DD-slug/image.jpg)
```
