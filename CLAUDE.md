# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A personal bucket list web app ("나의 버킷 리스트") built with vanilla JavaScript. Users can add, edit, delete, and complete life goals with real-time statistics and filtering. All data persists in browser LocalStorage — no backend or build tools required.

## Running the App

Open `index.html` directly in a browser, or serve with any static file server:
```bash
python -m http.server 8000
```
There is no build step, no package manager, and no test framework.

## Architecture

Two-layer architecture with global script loading (no modules):

- **`js/storage.js`** — `BucketStorage` object literal (loaded first, global). Encapsulates all LocalStorage CRUD operations: `load()`, `save()`, `addItem()`, `updateItem()`, `deleteItem()`, `toggleComplete()`, `getStats()`, `getFilteredList()`. Data stored under key `'bucketList'` as JSON array.

- **`js/app.js`** — `BucketListApp` class (loaded second). Handles all UI: DOM caching (`cacheElements`), event binding (`bindEvents`), and full re-render on every data change (`render`). Instantiated as global `app` on DOMContentLoaded. Inline `onclick` handlers in generated HTML call methods on the global `app` instance.

- **`index.html`** — Single page with Tailwind CSS via CDN (`cdn.tailwindcss.com`). Script load order matters: `storage.js` before `app.js`.

- **`css/styles.css`** — Custom animations (slideIn, fadeIn, scaleIn), filter button active states, mobile responsive overrides, and partial dark mode via `prefers-color-scheme`.

## Data Model

```javascript
{ id: "timestamp_ms", title: "string", completed: bool, createdAt: "ISO", completedAt: "ISO|null" }
```

## Key Conventions

- All code comments and UI text are in Korean
- XSS prevention via `escapeHtml()` method using DOM `textContent`/`innerHTML`
- Item IDs are `Date.now().toString()` — string timestamps
- New items are prepended (`unshift`) so newest appears first
- Rendering is full replacement (`innerHTML`) on every state change — no diffing
