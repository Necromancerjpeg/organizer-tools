# Token Usage Audit: Account-Layer Findings

**Originally audited:** 30 July 2026, from a Cowork cloud session
**Reviewed and re-verified:** 30 July 2026, from a Claude Code remote session
**Scope covered:** connectors, plugins, account skills, scheduled tasks
**Scope not covered:** local Claude Code configuration (see "What this session could not reach")

This file is written to be handed to Claude Code alongside `token-audit-deployment-prompt.md`. It completes the layers that a local Code session cannot see well, so that session can skip them and concentrate on the local filesystem.

The second pass re-ran every account-layer enumeration from a different surface. Most of the original findings held. Three numbers were wrong, one finding could not be reproduced, and the review turned up a structural point the first pass missed — the cost being audited is not a single fixed block, it is composed differently on every surface. Changes from the first pass are marked **[revised]**, **[new]**, or **[unconfirmed]**.

---

## Phase 0 status: blocked, and now verified blocked

The three orientation files the audit depends on remain unreachable. No desktop bridge is connected to either session, so `C:\ClaudeHub\context-engineering-dist\deployment-checklist.md`, `local-audit-proposals.md`, and the memory knowledge-base file `context_engineering_implementation.md` could not be read.

**[new]** The second pass went further than the first and searched Google Drive directly for all three filenames, plus the deployment prompt itself, and then for context-engineering content by full text. None of them are in Drive. The block is therefore not merely "no bridge was connected" — those files exist only on the local machine, and no cloud surface can reach them. Only a local Claude Code session will do.

Nothing below has been checked against those files. Some of it is certainly already tracked there. Treat the findings as raw observations that still need to be deduplicated against the deployment checklist before anything is acted on.

---

## What was verified

### 1. Fifteen connectors installed; fourteen loading in session — confirmed

Re-enumerated and unchanged. Every connector below reports `connected: true` and `enabledInChat: true`, meaning its tool catalogue is presented to the model on every session:

Booking.com, Canva, Expedia, Gmail, Google Calendar, Google Drive, Jotform, Kiwi.com, lastminute.com, Magisterium AI, Scholar Gateway, Spotify, Tripadvisor, Zoom.

Microsoft 365 is the exception. It still reports `installState: "unknown"` and `enabledInChat: false`, so it is installed but neither confirmed working nor currently loading. Nine months of sitting in that state is itself the finding: it is not being missed.

**[revised] The tool count was too high.** The first pass estimated "roughly 124 individual tool entries." The actual figure, counted by enumerating every connector's catalogue rather than estimating, is **116**. The distribution is as reported — very uneven:

| Connector | Tools |
|---|---:|
| Canva | 31 |
| Gmail | 16 |
| Jotform | 11 |
| Magisterium AI | 10 |
| Google Calendar | 9 |
| Zoom | 9 |
| Google Drive | 8 |
| lastminute.com | 8 |
| Booking.com | 3 |
| Spotify | 3 |
| Tripadvisor | 3 |
| Expedia | 2 |
| Kiwi.com | 2 |
| Scholar Gateway | 1 |
| **Total** | **116** |

The per-connector figures the first pass quoted were all correct; the total was not. The error does not change any conclusion — Canva is still more than a quarter of the surface on its own, and the shape of the problem is unchanged.

### 2. The travel cluster is the clearest redundancy — confirmed, count exact

Five separate connectors cover overlapping travel functions: Booking.com, Expedia, Kiwi.com, lastminute.com, and Tripadvisor. Between them they expose **eighteen** tools — the first pass's figure, now confirmed by enumeration rather than estimate — and the functional overlap is close to total. Kiwi.com, Expedia, and lastminute.com all search flights. Booking.com, Expedia, lastminute.com, and Tripadvisor all search hotels.

Expedia deserves particular attention. Its server instructions, read directly this pass, state verbatim: *"This is a gateway server that proxies requests to multiple MCP clients."* That is precisely the aggregator pattern the audit prompt warns about — a single innocuous-looking entry that can carry a much larger catalogue behind it. Worth noting that Expedia currently presents only two tools, so the aggregation is not costing anything today; the risk is that the catalogue behind the gateway can grow without any corresponding change on your side.

Against this, memory shows one active travel commitment (a San Luis Obispo trip) and one prospective one (the UFS symposium in South Africa). Two trips do not require five booking connectors permanently loaded.

lastminute.com is the one to keep if you keep only one: at eight tools it is the largest of the five, but it is also the only one covering flights, hotels, and combined packages, which between them subsume everything the other four do.

### 3. Six plugins contribute the bulk of the skill surface — confirmed, with an important qualification

The enabled plugins are `pdf-viewer`, `design`, `legal`, `data`, `cowork-plugin-management`, and `productivity`. Re-enumerated this pass; all six still enabled, none added or removed.

**[unconfirmed]** The count of roughly 38 skill descriptions contributed by these plugins could not be re-verified, for a reason that turns out to matter more than the number does — see finding 8. The figure is carried forward from the first pass and should be treated as unverified rather than wrong.

Judged against the work visible in memory, the fit varies sharply. The `productivity` plugin is unambiguously yours: its skills name Canadore, Cambrian, JTS, Thorneloe, and Compass directly. The `pdf-viewer` plugin has plausible use in thesis examination. The remaining three are harder to justify. The `legal` plugin is built for in-house corporate counsel and covers NDA triage, contract redlining, vendor agreement checks, and e-signature routing; MOU work touches this only at the edges. The `data` plugin assumes a SQL data warehouse. The `design` plugin assumes product and interface design, which is a different activity from the Duncanson-Hales document design system.

### 4. Three overlapping systems for tracking work — partially confirmed

The `project-manager` skill maintains a project registry with standup and weekly-review cadences. This is confirmed present and enabled at account level; its description does claim the project registry as its own persistent file.

The other two — `productivity:task-management` maintaining a separate `TASKS.md`, and `productivity:shared-context` maintaining a third file for cross-project threads — are plugin skills and could not be re-verified from this surface, again for the reason in finding 8. Carried forward as first-pass observations.

The substance of the finding is unaffected. Three systems each describing themselves as the place work is tracked, all competing to trigger on the same phrasing, is a cost in tokens and a larger cost in ambiguity about which file is authoritative. Retiring `TASKS.md` is, from the deployment notes, already under consideration; the wider point is that the three-way overlap is itself the problem.

### 5. Plugin-management duplicate — **[unconfirmed]**, may already be resolved

The first pass reported that `cowork-plugin-management` supplies two skills, `cowork-plugin-customizer` and `create-cowork-plugin`, and that a standalone account skill named `cowork-plugin` covers both functions in a single description.

**There is no skill named `cowork-plugin` in the account skill list this pass.** The full list of thirteen is in finding 7 below and does not contain it. Either it was removed between the two passes, or the first pass misread a plugin-scoped skill as a standalone account skill.

Do not act on this one. Check it from a Cowork session before removing anything, since the surface that reported the duplicate is the surface that can confirm it.

### 6. The Caribbean course skill is still enabled — confirmed

`caribbean-sacred-theory-course-production` and the generic `course-production` skill are both active at account level, with substantially overlapping trigger language. Both re-verified enabled this pass.

The overlap is real and readable in the descriptions themselves. `course-production` claims "any course the instructor teaches"; the Caribbean skill claims one specific seminar. Both list module guides, teacher/session guides, reading selections, LMS descriptions, and NotebookLM slide prompts as their outputs. The Caribbean skill's description is also the single longest in the account list at roughly 830 characters, so it is the most expensive individual description you are carrying.

This matches the migration thread already recorded in the deployment notes, so it is confirmation rather than a new finding.

### 7. **[new]** Only seven of the thirteen account skills are actually yours to prune

The account skill list contains thirteen entries, matching the first pass's count. But they fall into two groups that the first pass treated as one, and only one group is actionable:

**User-authored (7)** — these carry generated IDs and are skills you uploaded:
`academic-translation`, `coauthoring-writing-and-research-integrity`, `automated-grading`, `model-orchestrator`, `course-production`, `project-manager`, `caribbean-sacred-theory-course-production`

**Anthropic-bundled (6)** — these carry plain string IDs and ship with the product:
`morning`, `skill-creator`, `xlsx`, `pptx`, `pdf`, `docx`

The four document skills (`xlsx`, `pptx`, `pdf`, `docx`) are among the longest descriptions in the list — `xlsx` alone is roughly 900 characters — but they are not plugin clutter and removing them would cost real capability given how much of your work ends in Word and PDF. The practical consequence is that the account-skill layer offers less to cut than a bare count of thirteen suggests. The pruning opportunity is concentrated in the plugin layer, which strengthens rather than weakens the original ranking.

### 8. **[new]** The cost is surface-dependent, and this is the finding that reframes the rest

The first pass flagged, under "inferred rather than verified," that it did not know whether connector schemas load in full or by name outside the session it ran in. Running the same enumeration from Claude Code answers a larger version of that question, and the answer is more consequential than expected.

The three layers compose differently depending on where you are working:

- **Connector schemas are deferred here.** Only tool *names* load into context; the full parameter schemas are fetched on demand. The 116 tools cost roughly a name apiece rather than a full JSON schema apiece, which is a very large difference — likely an order of magnitude.
- **Plugin skills do not load here at all.** None of the six plugins' skills appear in this session. This is why findings 3, 4, and 5 could not be re-verified: the plugin skill layer is a Cowork and claude.ai construct that a Claude Code session does not inherit.
- **Claude Code substitutes its own.** In their place this session loads fourteen built-in skills (`init`, `review`, `security-review`, `simplify`, `dataviz`, `artifact-design`, `artifact-capabilities`, `update-config`, `keybindings-help`, `fewer-permission-prompts`, `loop`, `claude-api`, `run`, `session-start-hook`), giving twenty-seven skill descriptions total against the roughly fifty-one estimated for Cowork.

Two things follow. First, disabling the `legal`, `data`, and `design` plugins buys you nothing in Claude Code, because they are not loading there — the saving is real but confined to Cowork and claude.ai. Second, and more usefully, the audit's central question is not "how much does my configuration cost" but "how much does it cost *on the surface where I do the most work*." That question cannot be answered from either session in isolation, and the deployment checklist should record which surface each proposed change actually affects.

### 9. **[new]** `model-orchestrator` is a mitigation that is itself a cost, and it names the wrong model

`model-orchestrator` is a token-rationing skill — it exists to delegate work to cheaper model tiers and keep the main context lean. It is doing useful work and should stay. Two observations:

Its description is one of the longest in the account list at roughly 700 characters, and it instructs itself to trigger "BY DEFAULT at the start of any substantive or multi-step task ... even when the user never mentions cost, tokens, models, or delegation." A broad default trigger on a long description is the most expensive shape a skill can have. It is probably still net positive, but it is not free, and it is worth confirming it is actually firing and actually delegating rather than just sitting in the prompt.

Separately, the description is written around rationing **Fable 5** specifically. Sessions running on other tiers — this one runs on Opus — will read instructions calibrated to a model they are not using. Worth a pass to generalise the wording.

### 10. No scheduled tasks are running — confirmed at two levels

This is the good news, and it is now verified more thoroughly than the first pass managed. The first pass checked the scheduled-task list and session cron jobs. This pass checked both of those **and** the account-level Routines list, which is a separate mechanism the first pass did not query. All three are empty.

Nothing is firing on a timer, at session level or account level. Nothing is quietly consuming tokens while you are away. The `morning` skill is present but has not been set up as a recurring task.

---

## Ranked by likely impact

Ordered by how often the cost is paid rather than by how large any single instance is. Revised from the first pass to account for findings 7 and 8.

**Highest — the skill-description block, on Cowork and claude.ai.** Roughly fifty-one descriptions load into the system prompt on every message of every session there. The first pass estimated four to six thousand tokens. Measuring the thirteen account descriptions directly gives about 7,100 characters, or roughly 1,800 tokens; extrapolating the thirty-eight plugin descriptions at a similar average puts the full block nearer **7,000 tokens** than 6,000. Still an estimate, but the first pass's range was probably low. Three plugins account for a large share and appear to have little connection to your work. **This cost is not paid in Claude Code** (finding 8).

**High — the connector tool surface.** Fourteen connectors present 116 tools each session. In both audited sessions the schemas were deferred and only names loaded, which softens the cost considerably. That deferral is now confirmed on two independent surfaces rather than one, which raises confidence that it is general behaviour rather than a quirk. It has still not been checked in claude.ai chat, and if full schemas load there the cost would be substantially larger. Canva alone, at 31 tools, would dominate.

**Moderate — the five-way travel overlap.** Eighteen tool entries, confirmed, plus the trigger ambiguity it creates when four connectors can all answer "find me a hotel."

**Moderate — the three competing work-tracking systems.** Costs tokens in descriptions and costs attention in deciding which file to trust. The attention cost is the larger one.

**Moderate — the two course-production skills.** Promoted from the first pass's implicit lower ranking, because finding 6 establishes that the Caribbean skill carries the longest single description in the account list. Resolving the migration closes the most expensive individual line item available to you at account level.

**Low but worth closing — Microsoft 365** sitting in an unknown state. Costs almost nothing since it is not loading, but it is unresolved and has been for some time.

**Unranked pending verification — the `cowork-plugin` duplication** (finding 5), which could not be reproduced.

**None — scheduled runs.** There is nothing here to fix, now confirmed at both session and account level.

---

## Inferred rather than verified

Fit judgements about the `legal`, `data`, and `design` plugins rest on what memory records about your work, not on usage logs. If any of the three is serving a purpose not visible in those notes, that judgement is wrong and should be discarded.

Token figures are estimates derived from counting descriptions and tool entries, not measurements from a billing view. The 116 connector tools and the thirteen account skills are exact counts; the token conversions from them are not.

The roughly 38 plugin skill descriptions are a first-pass figure that this pass could not re-verify, because plugin skills do not load in Claude Code.

Whether connector schemas load in full or by name differs by surface. Two surfaces now observed directly, both deferring. claude.ai chat still unobserved.

---

## What this session could not reach

Four of the six audit areas still require a **local** Claude Code session, because they concern files on your own machine. A remote Claude Code session — which is what this review ran in — is no closer to them than Cowork was: it works from a fresh clone of a repository in an ephemeral container, not from your filesystem.

- Startup hooks and auto-run instructions, including whether any run unconditionally against parent directories rather than the actual project.
- Sizes of `CLAUDE.md` and equivalent instruction files, which the right-sizing proposal already covers.
- Default model settings for interactive versus automated use.
- Divergence between local skills under `Documents\.claude\skills\` and the packaged copies uploaded to the profile. Finding 7's split between user-authored and bundled skills gives that comparison a cleaner starting point: only the seven user-authored skills need to be diffed against local copies.

Hand this file to that session along with the audit prompt so it can begin at Phase 1 item 3 rather than repeating what is above.

---

## Decisions pending, and where to make them

None of the changes suggested here can be applied from a Cowork or remote Claude Code session. Connector removal and plugin disabling are settings actions in the Claude app: connectors under **Settings → Connectors**, plugins and skills under **Settings → Capabilities**. Nothing has been changed by either pass; both were read-only.

The questions worth deciding first:

1. **Keep more than one travel connector?** If not, lastminute.com subsumes the functionality of the other four.
2. **Do `legal`, `data`, and `design` earn their place?** Note from finding 8 that disabling them only saves tokens on Cowork and claude.ai, not in Claude Code.
3. **Which of the three work-tracking systems is authoritative?** This is the one whose real cost is attention rather than tokens.
4. **Repair or remove Microsoft 365?**
5. **Finish the course-production migration**, which closes the largest single description in the account skill list.
6. **[new] Which surface should the audit optimise for?** Finding 8 makes this the question that orders the others. If most of your work happens in Cowork, the plugin layer is the priority. If it happens in Claude Code, the plugin layer is irrelevant and the local-configuration layer this session could not reach is where the entire remaining opportunity sits.

Changes made in settings take effect on the next new session, not on one already open. All of them are reversible by reinstalling the connector or re-enabling the plugin, and no local files are touched by any of them.
