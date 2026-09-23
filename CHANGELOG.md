# Changelog

All notable changes to Codeforces Inline and its companion browser extension.

## [0.2.1] — Marketplace-ready README

- `README.md` rewritten for a Marketplace reader: it previously told an installer to "select this repo's `browser/` folder", which is impossible for anyone who only has the Marketplace page. Install now walks through downloading the companion zip from a GitHub release and loading it unpacked, with the Chrome Web Store listing (pending review) marked as the route once it's live.
- Dropped the "search Codeforces Inline" install step — redundant on the page that already has an Install button.
- `Codeforces: Setup walkthrough` had the same repo-relative instruction; fixed the same way, with a real link to the release.
- Moved **Development**, **Repo layout**, and **Where the scrapers will break** out of `README.md` into a new `CONTRIBUTING.md`, so the published README stays user-facing.
- Companion zip rebuilt against the current `publisher` and attached to the GitHub release for this version.

## [0.2.0] — packaging

### Extension
- Sidebar now browses **Contests, Gym, and a new Problemset (by rating)** entirely through Codeforces' public API — no setup, no companion, works the moment you install.
- Companion is now requested only when you do something that needs it (open a statement, add a group, submit), with a plain-language explanation instead of a raw error.
- A dismissible line at the top of the tree points at setup when the companion isn't connected.
- New **`Codeforces: Setup walkthrough`** command.
- New **`Codeforces: Change workspace folder`** command; the folder prompt no longer appears at activation — only the first time it's actually needed.
- Relay and companion now exchange a **protocol version** on every health check; a mismatched pair reports the exact problem instead of failing silently.
- **Deep link**: `vscode://<publisher>.<name>/openProblem?...` opens a problem, scaffolds the file, and focuses the editor. The URI is generated from `package.json`, never hardcoded (`scripts/gen-companion-config.js`), so publishing later is a config change.

### Companion (`browser/`)
- Added toolbar icons (16/48/128).
- Added an **"Open in VS Code"** button on Codeforces problem pages (`content.js`) that opens the deep link above, with a Marketplace-page fallback if nothing handles it.
- Added `browser/build.js` — builds a Chrome-Web-Store-ready zip (`npm run build:companion`).
- Added `browser/PRIVACY.md` — per-permission justification for the Web Store review form, including the new content script.
- Verbose logging moved behind an options-page toggle (off by default).

### Packaging
- Extension icon, `LICENSE`, `repository`/`bugs` links, keywords, removed the stray `private: true`.
- Switched license from MIT to **GPL-3.0-or-later** (`LICENSE`, `package.json`).
- Fixed `.vscodeignore` — it wasn't excluding generated solution folders or the `dist/` build output, so every previous `.vsix` shipped whatever was in your workspace alongside the extension. Neither was ever published; still, fixed.
- **Placeholders you must fill in before publishing** — see the note at the end of this file.

## [0.1.0] — initial build

First working end-to-end version, built and verified against live Codeforces:

- Browse contests, gyms, and group contests; read statements in a webview.
- Run samples locally with a diffed pass/fail per test, in a **Results panel** (per-test cards, first-diff-line highlight, custom test cases, in-panel compile errors).
- Submit and poll the verdict. Codeforces enforces a Cloudflare Turnstile CAPTCHA on the submit form and blocks this extension's own requests outright (TLS fingerprint) — so submitting and most reads go through a **companion Chrome extension** that fills the form and reads pages from a real logged-in tab; you solve the Turnstile and click Submit yourself.
- Local **attempt + run archive** (`.meta.json`, `attempts/`, `runs/` per problem, judge-agnostic layout) with a dedicated **Archive view** (offline, timeline of every run/submission, read-only source, diff against current file, search/filter) and a **`Codeforces: Stats`** command.
- A picked-once, globally-stored **workspace folder**, with non-destructive migration from an earlier ad hoc layout.

---

## Before publishing

`publisher` is set (`HimanshuShekhar`) and `repository`/`bugs` point at the
real GitHub repo. What's still open:

- Screenshots — marked `<!-- TODO -->` in `README.md`; add real ones from a live run (sidebar+statement+Results panel, and the Archive view).
- Chrome Web Store **long description** (entered in the developer dashboard at publish time, not a repo file) — add the "Unofficial. Not affiliated with, endorsed by, or sponsored by Codeforces." line there. `browser/manifest.json`'s `description` is capped by Chrome at 132 characters (rejected an upload at 369) and should say only what the extension does, nothing else.
- If the publisher ever changes again: `npm run gen:companion-config`, then `npm run build:companion` for a fresh companion zip (the deep link is derived from `package.json`, never hardcoded).
