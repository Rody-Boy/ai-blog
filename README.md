# AI Blog / Content Gallery

A lightweight, static AI content gallery that renders post cards from `posts/posts.json` and links to individual HTML post pages in `posts/`.

The project is intentionally simple:
- no framework build step
- no backend server logic in the repo
- pure HTML/CSS/JavaScript frontend

---

## Table of Contents

- [Overview](#overview)
- [How It Works](#how-it-works)
- [Features](#features)
- [Project Structure](#project-structure)
- [Post Data Format (`posts/posts.json`)](#post-data-format-postspostsjson)
- [Run Locally](#run-locally)
- [Content Workflow (n8n / automation)](#content-workflow-n8n--automation)
- [Customization](#customization)
- [Troubleshooting](#troubleshooting)
- [Deployment](#deployment)

---

## Overview

The homepage (`index.html`) fetches `posts/posts.json`, normalizes entries, and renders cards with:
- title
- excerpt
- image
- date
- platform badge
- quick actions (save/favorite, copy, share, preview)

Each card links to an existing static HTML post file in `posts/`.

---

## How It Works

1. Browser loads `index.html`.
2. Client fetches `posts/posts.json` (cache-busted request).
3. JSON entries are normalized client-side.
4. UI state applies search/sort/filter/favorites/pagination.
5. Rendered cards link to static `posts/post-*.html` pages.

No backend APIs are required for viewing content.

---

## Features

### UI / UX
- Dark/light theme toggle (persisted in `localStorage`)
- Sticky status bar with shown/matched counts and last updated time
- Responsive card grid
- Keyboard-friendly focus states

### Discovery
- Search by title/excerpt
- Platform chip filters
- Favorites-only filter
- Sort options: newest, oldest, title, random
- "Load more" pagination

### Interactions
- Save/unsave favorites (persisted locally)
- Copy link
- Native share fallback
- Preview modal
- Empty-state recovery button (clear filters)
- URL state sync for query/platform/sort

### Data robustness
- Supports multiple payload shapes in `posts.json`:
  - array
  - `{ posts: [...] }`
  - single object
- Fallbacks for missing title/excerpt/image/url/date/platform

---

## Project Structure

```text
.
├── index.html                # Main gallery page
├── README.md                 # This file
└── posts/
    ├── posts.json            # Post index used by homepage
    ├── post-<id>.html        # Individual post pages
    └── .gitkeep
```

---

## Post Data Format (`posts/posts.json`)

`posts/posts.json` should be a JSON array (recommended), where each item resembles:

```json
{
  "id": 1771712579846,
  "title": "Post title",
  "excerpt": "Short preview text",
  "image": "https://...",
  "image_url": "https://...",
  "date": "2026-02-21",
  "slug": "post-1771712579846",
  "url": "posts/post-1771712579846.html",
  "platform": "AI Post"
}
```

### Required vs optional

Strictly speaking, the UI can recover from missing fields due to normalization, but for best results provide all fields above.

---

## Run Locally

Because the page fetches JSON, open via a local server (not `file://`).

### Option A (Python)

```bash
python3 -m http.server 8000
```

Then open:

```text
http://127.0.0.1:8000
```

---

## Content Workflow (n8n / automation)

Recommended workflow behavior:

1. Generate new post HTML as `posts/post-<timestamp>.html`.
2. Read existing `posts/posts.json`.
3. Parse existing JSON safely.
4. Append/prepend new entry (do not overwrite with single object).
5. Write updated JSON back as plain text content.

### Important

If automation accidentally replaces `posts.json` with malformed data or one entry only, the gallery can degrade in quality. Ensure your workflow merges existing entries before writing.

---

## Customization

### Theme colors
Edit CSS variables in `:root` and `body[data-theme='light']` near the top of `index.html`.

### Initial page size
Change `pageSize` in script state:

```js
pageSize: 12
```

### Auto-refresh interval
Current refresh is every 5 minutes:

```js
setInterval(loadPosts, 5 * 60 * 1000)
```

### Platform labels
Platform chips are generated from `platform` values in `posts.json`.

---

## Troubleshooting

### No posts visible
- Verify `posts/posts.json` is valid JSON.
- Verify each `url` points to an existing HTML file.
- Open browser console for fetch/parse errors.

### Images not loading
- Check image URLs.
- The UI shows a fallback placeholder if image load fails.

### Filters look wrong after sharing URL
- Clear URL params and refresh.
- Use the in-UI "Clear filters" button if no posts match.

### CORS/file errors locally
- Make sure you are using a local server (`python3 -m http.server`).

---

## Deployment

This repo is static and can be deployed on any static host:
- GitHub Pages
- Netlify
- Vercel (static)
- Cloudflare Pages

As long as `index.html` and the `posts/` directory are served together, the gallery will work.

---

If you want, this README can be extended with:
- exact n8n node template JSON
- contribution guidelines
- changelog/release section
- screenshot examples
