# Contributing

Developer setup, repo layout, and where the HTML scrapers are most likely to
break next. See `README.md` for the user-facing install/usage docs.

## Development

```bash
npm install
npm run compile
```

Open the folder in VS Code, press **F5** — a second VS Code window opens with the extension loaded. To install it into your normal VS Code instead:

```bash
npm i -g @vscode/vsce
vsce package                                        # codeforces-inline-<version>.vsix
code --install-extension codeforces-inline-<version>.vsix
npm run build:companion                              # dist/codeforces-inline-companion-<version>.zip
```

`npm run selftest` compiles, runs the checks in `relay.ts` / `http.ts` / `archive.ts` / `migrate.ts` / `scrape.ts` / `languages.ts` / `scorecard.ts` over real temp directories and a real HTTP server, then bundles with esbuild and loads the bundle under a mocked `vscode` module to confirm `activate()` runs and every command registers — no real VS Code needed for any of it.

## Repo layout

```
src/
  http.ts         cookie jar, 1-req/s queue, Cloudflare detect, companion-fetch fallback
  session.ts      login, csrf, ftaa/bfaa, browser-session import, SecretStorage
  api.ts          read-only API, apiSig signing, caching
  scrape.ts       groups, statements, samples, compiler list (+ disk cache)
  submit.ts       submission POST, verdict polling, latest-submission lookup
  relay.ts        token-guarded localhost queue the companion polls (+ selfTest)
  cache.ts        on-disk read cache
  verdict.ts      shared verdict-string helpers
  archive.ts      judge-agnostic on-disk layout, per-run/attempt records, rolled-up index
  migrate.ts      one-time move from the old layout (+ selfTest)
  files.ts        solution scaffolding, per-problem metadata (ProblemMeta over archive.ts)
  languages.ts    per-extension compile/run commands, compiler-name → extension guess (+ selfTest)
  runner.ts       compile, run samples, diff
  statement.ts    statement webview
  resultsView.ts  Results panel (samples, attempts, submit)
  scorecard.ts    attempted/solved/verdict/language rollup, any scope (+ selfTest)
  statsView.ts    renders scorecard.ts's numbers — whole archive or one Archive-view scope
  archiveView.ts  Archive view — offline browse of every run and submission
  walkthrough.ts  Setup walkthrough webview
  tree.ts         sidebar, local+API solve-state reconciliation
  extension.ts    commands, workspace picker, migration, wiring
browser/          companion Chrome extension (MV3) — see browser/README.md, browser/PRIVACY.md
```

## Where the scrapers will break

Statements, the compiler list, group markup and the submit-form fields are parsed from live HTML. When Codeforces changes markup, `Session.findCsrf`, `parseLanguages`, `groupContests`, `problemDetail` or `submit.ts`'s field names break first. [cf-tool](https://github.com/xalanq/cf-tool) does the same in Go; its issue tracker is a good early warning.

Other failure modes:

- **`codeforces.com` tab closed or challenged.** Reads fail with *"Start the companion extension in Chrome…"* / *"open codeforces.com in Chrome, clear the check"*. Open a tab, clear the check.
- **Companion service worker asleep.** Chrome suspends MV3 workers after ~30 s idle; a keepalive alarm wakes it within ~30 s and VS Code retries across that window. `Codeforces: Check companion` confirms.
- **Rate limits.** One request per ~1 s through a single queue; verdict polls are 4 s. Don't lower these during a live contest.
- **Identical submission.** Codeforces refuses a resubmit of unchanged source; the message is surfaced as-is and still recorded as an attempt.

## Releasing

```bash
npm version 0.2.1 --no-git-tag-version   # bump src of truth: package.json
npm run gen:companion-config              # regenerate browser/deeplink-config.js
npm run selftest                          # compile, self-tests, bundle, activation smoke test
npm run build:companion                   # dist/codeforces-inline-companion-<version>.zip
npx vsce package --no-dependencies --out dist/codeforces-inline.vsix
gh release create v<version> \
  dist/codeforces-inline-companion-<version>.zip \
  --title "v<version>" \
  --notes "See CHANGELOG.md"
vsce publish   # or upload dist/codeforces-inline.vsix by hand on the Marketplace
```
