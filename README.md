# Inline for Codeforces

*Unofficial. Not affiliated with, endorsed by, or sponsored by Codeforces.*

Browse contests, gyms, and group contests, read statements, run samples, submit, and keep a searchable local history of every attempt — without leaving VS Code.

<!-- TODO (before publishing): screenshot — sidebar + statement + Results panel side by side -->

## Before you install — read this

**Browsing works with nothing else installed. Opening a problem, viewing a group, and submitting all need a second, free browser extension** — that's not a rare case, it's most of what this extension does. Why: Codeforces blocks this extension's own network requests outright, and its submit form has a CAPTCHA only a human in a browser can solve. There's no way around either from inside VS Code. Full detail in **Architecture**, below.

If that's a dealbreaker, this extension isn't for you yet. If it's fine, the companion takes about two minutes to set up — run **`Codeforces: Setup walkthrough`** after installing, or follow **Install** below.

## Requirements

- VS Code 1.85+
- Google Chrome (or a Chromium browser that can load an unpacked MV3 extension) — for the companion, needed for anything beyond browsing
- A Codeforces account, if you plan to submit

## Install

You've already got this extension — that's how you're reading this. The one extra step is the companion browser extension.

### 1. Get the companion

**Chrome Web Store** — *submitted, pending review; not live yet.* <!-- TODO: replace this line with the Web Store listing link once approved --> Once it's live, this becomes a one-click install and the manual steps below won't be needed.

**Manual install (works today):**
1. Open the [latest release](https://github.com/Luna7282/inline-for-codeforces/releases/latest) on GitHub and download `codeforces-inline-companion-*.zip` from **Assets**.
2. Unzip it anywhere.
3. In Chrome, go to `chrome://extensions`, turn on **Developer mode** (top right), click **Load unpacked**, and select the folder you just unzipped.

### 2. Connect it to VS Code

1. In VS Code, run **`Codeforces: Relay info`** — copy the port and token it shows.
2. In Chrome, open the companion's card on `chrome://extensions` → **Details** → **Extension options** → paste the port and token → **Save**.
3. Stay signed in to `codeforces.com` in that Chrome profile, with a tab open.

`Codeforces: Setup walkthrough` covers this in the app, with the reasoning. `Codeforces: Check companion` tells you if it's working.

## Quick start

1. Open the **Codeforces** icon in the Activity Bar. The tree populates immediately — **Contests**, **Gym**, **Problemset**, **Groups** — no setup, no login.
2. (Optional, no login needed) Set `codeforces.handle` in Settings to see your solved/attempted marks.
3. Click a problem. First time, you're asked for a **workspace folder** — where your solutions and history live; it's global, not per-project.
4. Write your solution, then `Ctrl+Alt+R` to run the samples in the **Results** panel, or `Ctrl+Alt+Enter` to submit.
5. For groups: `Codeforces: Add group`, paste the group code or its URL.

<!-- TODO (before publishing): screenshot — Results panel with a mix of pass/fail cards -->

## Features

- **Sidebar**: Contests, Gym, Problemset (browse by rating), and your groups, with solve/attempt state and API-backed solved marks.
- **Statement webview** beside your solution file, with samples, limits, and images.
- **Results panel** (below the tree): one card per test — PASS/FAIL badge, runtime, input/expected/actual with the first differing line highlighted. Passing cards collapse; failing ones stay open. Add your own test cases. Compile errors show verbatim, in the panel, not a toast. Submit and watch the verdict from the same panel.
- **Submission compiler** shown in the status bar next to the open problem — click to change it. Picking a different language scaffolds that language's file in the same problem folder (never touching one that already exists); run and submit always act on whichever file is focused, not a global setting, so C++ and Python solutions for the same problem coexist and compile independently.
- **Local archive of everything you've done** — every run and every submission, including the ones Codeforces refused outright — searchable offline in a dedicated **Archive view** (its own Activity Bar icon): browse by judge → contest/group/gym → problem, a timeline per problem (consecutive identical outcomes collapse into one row), click any entry to reopen that exact source read-only and diff it against your current file.
- **`Codeforces: Stats`** — attempted / solved / solve rate / attempts-per-solve, a verdict breakdown, and a language breakdown, from that local history. Click a group, gym, or contest in the Archive view for the same scorecard scoped to just that one.

<!-- TODO (before publishing): screenshot — Archive view timeline -->

## Commands

| Command | What it does |
| --- | --- |
| `Codeforces: Log in` | Handle + password. Only needed if you plan to skip the companion entirely (rare — see Architecture). |
| `Codeforces: Setup walkthrough` | Explains the companion, Turnstile, and the tab requirement. |
| `Codeforces: Check companion` | Reports whether the companion is polling, asleep, misconfigured, or absent. |
| `Codeforces: Relay info` | Shows the port + token to paste into the companion's options. |
| `Codeforces: Add group` / `Remove group` | Track a group's contests in the sidebar. |
| `Codeforces: Choose submission language` | Reads the real compiler list off a live submit page. |
| `Codeforces: Run sample tests` (`Ctrl+Alt+R`) | Compile + run every sample and custom test. |
| `Codeforces: Submit current file` (`Ctrl+Alt+Enter`) | Submit and watch the verdict. |
| `Codeforces: Stats` | Local attempt/run summary across every problem. |
| `Codeforces: Refresh` | Re-fetch the sidebar and the archive index. |
| `Codeforces: Change workspace folder` | Move your solutions/history to a new folder. |
| `Codeforces: Import session from browser` | Fallback login path for when Cloudflare's check happens to be off. |

`Ctrl+Alt+R` / `Ctrl+Alt+Enter` only fire with an editor focused, and deliberately avoid `Ctrl+Alt+B` / `Ctrl+Alt+S`, which the `cph` extension claims. Rebind them under `codeforces.runTests` / `codeforces.submit` in Keyboard Shortcuts if you like.

## Settings

| Setting | What it does |
| --- | --- |
| `codeforces.handle` | Handle used for solved/attempted marks. Set automatically at login. |
| `codeforces.workspaceRoot` | Root folder for solutions + history. Prompted for on first use; change via `Codeforces: Change workspace folder`. |
| `codeforces.extension` | File extension for a problem's *first* solution file. A problem can hold more than one language at once — see `codeforces.languages`. |
| `codeforces.templatePath` | File copied into every new solution. |
| `codeforces.languages` | Compile/run commands per file extension, plus the Codeforces compiler name last picked for it (set automatically by the language picker). Placeholders: `${file}` `${bin}` `${dir}` `${name}`. Leave `compileCommand` unset for an interpreted language. Run/submit act on the active file's own extension, not one global setting. |
| `codeforces.timeoutMs` | Per-sample time limit. |
| `codeforces.groups` | Group codes shown in the tree. |
| `codeforces.contestLimit` | How many contests to list. |
| `codeforces.relayPort` | Localhost port the companion polls (default 27121, bound to 127.0.0.1). |
| `codeforces.directSubmit` | POST straight from the extension instead of via the companion. Only works when Cloudflare's Turnstile isn't enforced. |
| `codeforces.debug` | Verbose relay/command tracing to the Codeforces output channel. Off by default. |
| `codeforces.apiKey` / `apiSecret` | Optional, from `/settings/api`. Only needed for data private to you. Requests are signed and die if your clock is more than 5 minutes off server time. |

## Architecture — why the companion extension is required

With the companion unloaded, browsing and solve marks still work (read-only API). What needs it — opening a statement, listing a group's contests and problems, submitting, and polling a verdict — is forced, not a preference:

1. **Node can't reach Codeforces' HTML at all.** Codeforces runs Cloudflare in a mode that rejects Node's TLS handshake fingerprint *before* the request reaches Codeforces — every HTML route returns a `403 "Just a moment…"`. A valid session cookie does not help; neither does importing `cf_clearance` + the browser User-Agent (tried, still 403 — the block is on the TLS fingerprint, not the cookies). Only `codeforces.com/api/*` is exempt.
2. **A browser *service-worker* fetch isn't a page fetch either.** The companion's background fetch also gets a Cloudflare 403 — it sends `Sec-Fetch-Site: none` and no `Referer`, and Cloudflare scores it as non-browser. So the companion re-runs each read as a real page-context fetch (`chrome.scripting.executeScript`, MAIN world) inside a logged-in `codeforces.com` tab, which sends the right headers and the right TLS. **You must keep one `codeforces.com` tab open that has passed the "Just a moment" check.**
3. **The submit form has a Cloudflare Turnstile CAPTCHA.** There is no way to produce a valid Turnstile token headlessly. The companion opens the submit page and fills in the compiler, problem and source; **you solve the Turnstile and click Submit yourself.** VS Code then finds the new submission and polls the verdict (also via the companion).
4. **Group contests are invisible to the API.** `contest.list` has no group parameter with or without a signed key, so group contest/problem lists are scraped from `/group/<code>/…` — through the companion, per point 1.
5. **Codeforces has no write API.** `/apiHelp` has promised submit methods "soon" for years. Don't wait for it.

The extension runs a token-guarded localhost relay on `127.0.0.1` (`codeforces.relayPort`, default 27121); the companion polls it, and the two sides check a **protocol version** on every health check so an out-of-sync pair fails with a clear message instead of a silent hang. `Codeforces: Relay info` shows the port and token to paste into the companion's options once; `Codeforces: Check companion` reports whether it's connected. Reads are cached hard on disk (statements 30 days, group lists 15 minutes) since each one is a poll round-trip. If Cloudflare's under-attack mode is ever off, `codeforces.directSubmit` posts straight from the extension instead.

See `browser/README.md` for the companion in detail and `browser/PRIVACY.md` for what it does and doesn't send anywhere.

## Local archive layout

Everything lives under your workspace folder, keyed by `{ judge, scope, contestRef, index }` so another judge (LeetCode, AtCoder, …) could slot in beside `codeforces/` without a migration:

```
<root>/
  codeforces/
    group-<code>-<contestId>/  gym-<contestId>/  contest-<contestId>/
      A/
        A.cpp                       one file per language you've tried
        A.py                        — .meta.json below is shared by all of them
        .meta.json                  ref, problem, samples, user tests
        runs/<timestamp>.json       one per local run, tagged with language
        attempts/<timestamp>.json   one per submission, tagged with language
  .archive-index.json               rolled-up index for fast reads
```

`.meta.json` + `attempts/` are the source of truth for the tree's solve state and `Codeforces: Stats` — they hold tries Codeforces refused outright, which `user.status` never shows. Pre-existing `group-*/gym-*/<digits>/` folders and `.cf/*.json` files (an earlier, pre-1.0 layout) are migrated into this layout the first time you point the extension at a folder that has them: copy, verify, then remove the originals (build artifacts like `A.exe` are left where they are).

## Privacy

The extension talks to `codeforces.com` and (for the companion pairing) `127.0.0.1` — nothing else, no analytics, no telemetry. Your session lives in VS Code's `SecretStorage`. Full detail, permission-by-permission, in `browser/PRIVACY.md`.

## License

GPL-3.0-or-later — see `LICENSE`. In short, for anyone forking this: if you distribute a modified version, it must also be open source under the GPL.

## Contributing

Building from source, the repo layout, the self-test suite, and where the HTML scrapers are most likely to break next are all in **[CONTRIBUTING.md](CONTRIBUTING.md)**.
