# Provenance and changes

`academic-line-edit` is a fork of [no-ai-slop](https://github.com/petergyang/no-ai-slop) by Peter Yang, used under the MIT licence reproduced in `LICENSE-upstream`. Forked from upstream commit `efc572f`.

The upstream skill is tuned for newsletter and blog prose. This fork adapts it to scholarly, instructional, and institutional writing, and makes it safe to run alongside `coauthoring-writing-and-research-integrity`, `academic-translation`, `course-production`, and `automated-grading`.

## What was dropped

The ChatGPT and Codex plugin packaging — `.codex-plugin/`, `scripts/build_plugin.py`, `agents/openai.yaml`, `.github/workflows/plugin.yml`, `assets/`, `PRIVACY.md`, `TERMS.md`. It exists to satisfy the OpenAI plugin directory and has no function in a Claude skills library, which loads a folder rather than a validated ZIP.

## What was changed

**Register profiles.** Upstream has one mode for all prose. This fork resolves a Scholarly, Instructional, or Public profile first, and defaults Scholarly work to detect-only so the safe failure is "flagged but unedited."

**Fabrication guard.** Upstream demonstrates its concreteness rule by rewriting "the tool significantly improves engineering productivity" into "the tool cut review time from 30 minutes to 8." The rule text forbids inventing claims, but the worked example teaches substitute-a-plausible-number, which is phantom specificity under Hard Rule 4 of the coauthoring skill. Vague claims are now flagged `[VAGUE]` and left intact. Hard Limits 1–3 make this non-negotiable, and `eval.md` checks it first.

**Seven register exemptions.** Conclusions, evidential hedging, signposting, load-bearing subordination, conditional passive voice, the draft's existing first-person convention, and deliberate terminological repetition are all protected. Upstream would cut or flatten each of them.

**Vocabulary.** Removed from the ban list: foster, facilitate, intricate, multifaceted, meticulous, paramount, realm. These are ordinary humanities and social-science words; upstream bans them outright. They now pass when carrying literal meaning and get cut when decorative. Added: unlock and landscape in their metaphorical senses.

**Attribution handling escalated.** Weasel attribution moves to the top of the pattern list and produces `[SOURCE NEEDED]` flags rather than deletions, matching the coauthoring skill's citation-integrity rule.

**Scope narrowed.** No restructuring, no editing inside quotations or translated passages, no touching glossary-controlled terms. Upstream permits reorganisation with an explanation.

**Output extended.** Every run returns an **Author work required** list alongside the edited draft, mirroring the coauthoring skill's verification output.

**Description tightened.** Upstream triggers on any request for clearer or more direct writing, which would fire mid-draft during a coauthoring session. This fork triggers on explicit invocation or an explicit request for a line edit or slop audit, and defers to the coauthoring skill's Hard Rules when invoked inside its workflow.

**Student work restricted to detect mode**, with slop findings barred from acting as an AI-authorship claim or a grade input.

## What was kept

The load-bearing part of the upstream skill: the anti-overcorrection rules. Deleting a fake-profound kicker rather than improving it, refusing stacked punchy fragments and robotic rhythm, collapsing synonym cycling, and pairing each "cut X" rule with a "keep X when it is the author's voice." Those rules stop an anti-slop pass from producing differently-flavoured slop, and they are the part worth forking.

Detect mode's epistemics are also kept intact: named patterns with quoted lines, never a score and never a verdict on authorship.

## Installing

The skill library at `~/.claude/skills` syncs from the claude.ai custom skill library, so this folder is the source, not the install target. Upload `academic-line-edit/` as a custom skill; `SKILL.md` and `eval.md` are the only files the runtime needs.
