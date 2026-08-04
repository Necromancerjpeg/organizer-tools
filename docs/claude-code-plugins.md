# Claude Code plugins to add

Source: Dibakar Ghosh, ["I tested over 100 Claude Code plugins—here are the 5 that actually
matter"](https://www.howtogeek.com/the-best-claude-code-plugins/), How-To Geek, Aug 3 2026.

The five below are the article's picks, each with a note on how it applies to this repo — a
single-file, local-first React PWA (`index.html`, 573 lines; React 18 + Babel standalone from
CDN; all app code inline in a `<script type="text/babel">` block at `index.html:50`; IndexedDB
persistence; `sw.js` service worker) used for community organizing relationship maps.

## Installing

- **Desktop app:** Customize (left sidebar) → Plugins → Browse.
- **Terminal:** `/plugin`, then the Discover tab.

The official Anthropic marketplace (`claude-plugins-official`) is preconfigured, so official
plugins install straight from `/plugin` with no setup. Third-party marketplaces are added with
`/plugin marketplace add <github-repo | git-url | local-path>`.

**Security:** plugins from the built-in marketplaces are vetted. Anything from elsewhere is
not — review its skill files, MCP server code, and docs before installing. Of the five below,
only Superpowers comes from a third-party marketplace. That matters more than usual here,
because this app holds real organizing contact data.

---

## 1. Superpowers — engineering discipline

- **Author:** Jesse Vincent (`obra`) — community plugin, third-party marketplace
- **Install:**
  ```
  /plugin marketplace add obra/superpowers-marketplace
  /plugin install superpowers@superpowers-marketplace
  ```
- **What it is:** A dozen-plus skills encoding a full development methodology — TDD, systematic
  debugging, brainstorming, planning. Adds `/brainstorm` (a Socratic requirements session before
  any code exists), `/write-plan`, and `/execute-plan`. Skills auto-activate when relevant.
- **Why it matters:** It enforces *process*. The TDD skill requires a test to fail before any
  implementation is written; the debugging skill requires a root-cause investigation before
  Claude may touch a fix, which stops symptom-patching.
- **The article's caveat:** it does not substitute for model tier — Superpowers-on-Sonnet still
  won't match Opus, and Opus won't match Fable. Same model with vs. without is the real
  comparison, and there it wins.
- **Fit here:** The article calls this "the only non-negotiable install," and it is the
  strongest pick for this repo specifically. 573 lines in one file with no test suite is exactly
  where a quick fix silently breaks adjacent behaviour.

## 2. Productivity — persistent task + memory context

- **Author:** Anthropic (official)
- **Install:** `/plugin` → Discover → `productivity`
- **What it is:** Three components — a `TASKS.md` task system Claude reads and updates; a
  two-tier memory system (a local `CLAUDE.md` hot cache for frequently-used people, projects,
  and terms, plus a long-term memory folder with richer files); and an HTML dashboard.
  `/start` initializes all three. `/update` scans connected apps (Slack, Asana, Notion, Gmail)
  and consolidates open work into `TASKS.md`.
- **Why it matters:** Removes the re-explain-the-project tax on every new session.
- **Fit here:** Useful, with one real constraint. The memory system is explicitly designed to
  remember *people*, and this app's whole subject matter is community members and volunteers.
  Keep contact data in the app's IndexedDB store, not in `CLAUDE.md` or the long-term memory
  folder — those are plain files that get committed or synced by accident. Record project
  conventions there, not constituents.

## 3. Language server (LSP) plugins — real semantic understanding

- **Author:** Anthropic (official). A category, not one plugin: `typescript-lsp` (TypeScript
  **and JavaScript**), `pyright-lsp`, `rust-analyzer-lsp`, `clangd-lsp` (C/C++), and more —
  including Go, Java, Kotlin, Lua, PHP, Ruby, and Swift.
- **Install:** `/plugin` → Discover → pick the one for your language. **You must also install
  the language server binary** — the plugin connects Claude to an existing LSP rather than
  bundling one. Claude Code can list your installed LSP plugins and set up the matching servers
  if you ask it to.
- **What it is:** The same LSP servers that power IDE intelligence — real-time diagnostics,
  go-to-definition, find-all-references.
- **Why it matters:** Without one, Claude largely sees the codebase as *text*: it misses type
  information and often has to run the code to discover errors. With one, it catches errors
  while writing, finds every reference before a rename, and greps less. Fewer edit-and-retry
  cycles also means fewer tokens.
- **Fit here:** **Low value in the current layout, for a structural reason rather than a
  language one.** `typescript-lsp` does cover plain JavaScript — but this repo's only real `.js`
  file is the 35-line `sw.js`. Every component, hook, and handler lives inline in the
  `text/babel` block in `index.html`, where a language server cannot see it. Installing it now
  buys almost nothing. It becomes worthwhile the moment app code is extracted into real `.js`
  modules — which is arguably the stronger reason to do that extraction.

## 4. Frontend Design — non-generic UI output

- **Author:** Anthropic (official), a single skill
- **Install:** `/plugin` → Discover → `frontend-design`
- **What it is:** Forces Claude to establish an actual design direction before generating
  interface code — what the page is for, who it's for, and a specific aesthetic to commit to
  (brutalist, maximalist, retro-futuristic, etc.). It then makes deliberate typography, color,
  motion, and layout choices: opinionated font pairings, scroll-triggered animations, asymmetric
  compositions. Works with React, Vue, Svelte, and plain HTML/CSS, and auto-activates whenever
  you ask for a frontend interface.
- **Why it matters:** Default AI UI output converges on the same generic layouts, purple
  gradients, and system fonts regardless of the product.
- **Fit here — and the scoping rule that goes with it:** This particular app is NDP political
  comms, so it correctly uses the NDP identity: `#F58220` orange, `#58595B` grey, Roboto +
  Source Serif 4, mobile-first 880px container. When working on *this* file, tell Frontend
  Design to work within those tokens rather than proposing a fresh aesthetic.

  **That constraint is specific to NDP comms surfaces — it is not a house style for the repo.**
  Other tools added under `organizer-tools` should not inherit the orange-and-grey NDP palette
  by default. For anything that isn't NDP political communications, let Frontend Design do what
  it's actually for and establish a direction of its own.

## 5. Context7 — live, version-correct library docs

- **Author:** Upstash
- **Install:** `/plugin` → Discover → `context7`. Ships a single MCP server; no skills or
  agents to invoke.
- **What it is:** Pulls current, version-specific documentation directly from source
  repositories and injects it into context on demand.
- **Why it matters:** An LLM's knowledge of a library API is frozen at its training cutoff, so
  it will confidently emit deprecated patterns or hallucinate methods that no longer exist.
- **Honest limitation (the article's own):** it is not purely preventative. In practice Claude
  sometimes calls a deprecated API *first*, then reaches for Context7 and corrects itself. Not
  ideal, but preferable to permanently loading the context window with docs it may never need.
- **Fit here:** Directly relevant. React 18 is pinned via unpkg, and the app leans on Service
  Worker and IndexedDB APIs — all areas where answers tend to blend versions.

---

## Recommended set for this repo

| Plugin | Verdict |
| --- | --- |
| Superpowers | **Install** — highest value; no test suite, all logic in one file |
| Context7 | **Install** — React 18 / Service Worker / IndexedDB version accuracy |
| Frontend Design | **Install** — scope NDP tokens to this NDP app only, not to future tools |
| Productivity | **Optional** — keep member and volunteer data out of the memory files |
| LSP (`typescript-lsp`) | **Defer** — app code is inline in HTML; revisit after extracting modules |

## The underlying point

Nothing here is strictly necessary — everything these plugins do could be recreated with
careful prompting and manual setup. They are curated bundles of prompts, instructions, and
tooling that make that setup dramatically easier.
