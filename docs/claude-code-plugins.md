# Claude Code plugins to add

Source article: [How-To Geek — "I tested over 100 Claude Code plugins—here are the 5 that
actually matter"](https://www.howtogeek.com/the-best-claude-code-plugins/)

The five below are the article's picks, plus a note on how each one applies (or doesn't) to
this repo — a single-file, local-first React PWA (`index.html`, ~573 lines, React 18 + Babel
standalone from CDN, IndexedDB persistence, `sw.js` service worker) used for community
organizing relationship maps.

Install order below is the recommended order: workflow discipline first, then accuracy, then
polish.

---

## 1. Superpowers — engineering discipline

- **Author:** Jesse Vincent (`obra`) — community plugin
- **Install:**
  ```
  /plugin marketplace add obra/superpowers-marketplace
  /plugin install superpowers@superpowers-marketplace
  ```
- **What it is:** A dozen-plus skills that encode a full development methodology —
  red/green/refactor TDD, root-cause-before-fix debugging, brainstorming, and planning.
- **Why it matters:** It constrains *process*, not just output. The debugging skill blocks
  Claude from patching a symptom before it has explained the cause.
- **Fit here:** Strongest pick for this repo. A 573-line single file with no test suite is
  exactly where "just fix it" edits silently break adjacent behaviour. The planning skill
  also survives long sessions, which matters when every change lands in one file.

## 2. Productivity — persistent task + memory context

- **Author:** Anthropic (official)
- **Install:** `/plugin` → official marketplace → `productivity`
- **What it is:** A `TASKS.md`-backed task system Claude reads and updates, a two-tier memory
  store for recurring people/projects/terms, and an HTML dashboard.
- **Why it matters:** Removes the "re-explain the project every session" tax.
- **Fit here:** Useful, with a caveat — this app holds real organizing contacts. Keep
  volunteer/member names out of the memory store; record project conventions only.

## 3. Context7 — live, version-correct library docs

- **Author:** Upstash
- **Install:** available as a plugin via `/plugin`, or as an MCP server:
  ```
  claude mcp add --transport http context7 https://mcp.context7.com/mcp
  ```
- **What it is:** Pulls current, version-specific documentation from source repos into
  context on demand.
- **Why it matters:** Kills the most common failure mode — confidently calling an API that
  was deprecated after the training cutoff.
- **Fit here:** Directly relevant. The app pins React 18 via unpkg and leans on Service
  Worker and IndexedDB APIs, all of which Claude tends to answer for from a mix of versions.
  Context7 pins the answer to the version actually loaded.

## 4. A language-server (LSP) plugin — real type/lint feedback

- **Author:** Anthropic (official); packages include `typescript-lsp`, `pyright-lsp`,
  `gopls-lsp`, `rust-analyzer-lsp`
- **Install:** `/plugin` → official marketplace → pick the one for your language.
  **Note:** the plugin only connects to a language server; you must install the server
  binary separately.
- **What it is:** Surfaces type and lint errors inside the edit loop instead of at runtime.
- **Why it matters:** Cuts the edit-run-fail-retry cycle, which saves both wall-clock and
  tokens.
- **Fit here:** **Lowest value as-is.** This repo is untyped JSX compiled in the browser by
  Babel standalone — there is nothing for `typescript-lsp` to check. Worth installing only
  if the app is ever split into real modules with a build step. Until then, skip it.

## 5. Frontend Design — non-generic UI output

- **Author:** Anthropic (official)
- **Install:** `/plugin` → official marketplace → `frontend-design`
- **What it is:** Forces Claude to commit to a design direction and aesthetic before
  generating interface code, instead of defaulting to generic AI-looking layouts.
- **Why it matters:** Default UI output converges on the same centered-card look regardless
  of the product.
- **Fit here:** Relevant but needs a leash. This app already has a deliberate NDP visual
  identity — `#F58220` orange, `#58595B` grey, Roboto + Source Serif 4, a mobile-first
  880px container. Point the plugin at the existing tokens rather than letting it propose a
  fresh direction.

---

## Recommended set for this repo

| Plugin | Verdict |
| --- | --- |
| Superpowers | Install — highest value given no test suite |
| Context7 | Install — React 18 / SW / IndexedDB version accuracy |
| Frontend Design | Install — constrain it to the existing NDP palette |
| Productivity | Optional — keep contact data out of memory |
| LSP | Skip until there is a build step and typed source |

## Sourcing note

The article page is blocked by this environment's network policy (HTTP 403 at the proxy), so
the five entries above were reconstructed from search-result excerpts of the article itself
rather than a full read. The plugin identities are consistent across those excerpts; confirm
exact package names in `/plugin` before installing, since marketplace naming shifts.
