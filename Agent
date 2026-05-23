# AGENTS.md — BookFinder

Guidance for AI coding agents working on this codebase.

---

## Project Overview

BookFinder is a client-side web app with **no build step**. It is plain HTML + CSS + JavaScript — no bundler, no framework, no npm. Everything runs directly in the browser.

- `index.html` — the entire application (markup + all JS logic in a `<script type="module">`)
- `style.css` — all styles, imported via `<link>` in the HTML
- `README.md` — Firebase setup and GitHub Pages deployment guide

---

## Architecture

### Frontend
- Vanilla JS ES modules (`<script type="module">`)
- No framework, no build tool, no package.json
- Fonts loaded from Google Fonts CDN
- Firebase loaded from `gstatic.com` CDN (v10, ES module imports)

### Database
- **Firebase Firestore** (real-time, persistent)
- Collection path: `users/default-user/reading-list`
- Each document ID is the OpenLibrary book key with `/` replaced by `_`
- Document shape:
  ```js
  {
    key:     "/works/OL12345W",   // OpenLibrary key
    title:   "string",
    author:  "string",
    year:    "string | number",
    cover:   "string",            // OpenLibrary cover ID, or ""
    status:  "want" | "read",
    savedAt: 1234567890           // Unix ms timestamp
  }
  ```

### External API
- **OpenLibrary** `https://openlibrary.org/search.json` — free, no API key
- **OpenLibrary Covers** `https://covers.openlibrary.org/b/id/{cover_i}-M.jpg`

---

## Key Conventions

- **No localStorage / sessionStorage** — all persistence is via Firestore
- **Real-time sync** — `onSnapshot` keeps `savedBooks` state and the UI in sync at all times; avoid one-off `getDoc` calls
- **Firestore doc IDs** — always use `encodeBookKey(key)` (replaces `/` with `_`) when reading or writing docs
- **HTML escaping** — user-visible strings from the API are always passed through `escHtml()` before being injected into innerHTML
- **No authentication** — the app uses a hardcoded `USER_ID = "default-user"`; if adding auth, swap this for the Firebase Auth UID
- **CSS variables** — all colours are defined as CSS custom properties in `:root` inside `style.css`; never hardcode hex values in new rules
- **Animations** — new cards/list items use the `fadeUp` keyframe + `animation-delay` for stagger; follow this pattern for any new list rendering

---

## Adding Features — Guidelines

### Adding a new field to a saved book
1. Include the field in the `setDoc` call inside `onSaveClick`
2. Read it back in `renderReadingList` where the list item is built
3. No migration needed — Firestore documents are schemaless; old documents simply won't have the field (handle with `|| defaultValue`)

### Adding a new status (beyond "want" / "read")
1. Add a `.status-btn` entry in `renderReadingList`
2. Add a CSS rule for `.status-btn.active[data-status="newstatus"]` in `style.css`
3. Update the `.list-filters` buttons and the `activeFilter` logic

### Adding Firebase Authentication
1. Import `getAuth`, `signInAnonymously` (or another provider) from the Firebase Auth CDN module
2. Replace the `USER_ID` constant with `auth.currentUser.uid`
3. Wrap the `onSnapshot` call so it only starts after auth resolves
4. Update Firestore security rules to scope reads/writes per UID

---

## What NOT to Do

- Do not introduce a bundler (Vite, Webpack, etc.) without updating the README and removing CDN imports
- Do not add `type="module"` to additional `<script>` tags; keep all JS in the single existing module script
- Do not use `innerHTML` with unsanitised API data — always call `escHtml()` first
- Do not call `renderReadingList()` manually after Firestore writes — the `onSnapshot` listener triggers it automatically
- Do not use relative Firestore paths; always build refs from `listColRef` or `doc(listColRef, id)`

---

## Local Development

No server required for most changes — open `index.html` directly in a browser.

If you need a local server (e.g. to avoid CORS issues with certain browser policies):

```bash
npx serve .
# or
python3 -m http.server 8080
```

Firebase config must be filled in (`FIREBASE_CONFIG` object in `index.html`) for Firestore features to work locally.

---

## Deployment

GitHub Pages — push to `main`, Pages serves from the root. No build step. See `README.md` for full instructions.
