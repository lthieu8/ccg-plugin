---
name: vault-conventions
description: >-
  Shared rules for locating and reading the CCG Obsidian vault - folder layout, spec frontmatter,
  the status lifecycle, acceptance-criteria format, and the decisions log. Load this before
  reading or writing anything in the vault. Use when asked about a feature, a spec, a business
  rule, or "what did the BA say about X", and whenever another ccg-ba skill needs vault paths.
  Trigger: vault, spec, specification, requirement, acceptance criteria, AC-, business rule,
  glossary, decisions log, what does the doc say, is this specified.
---

# CCG Vault Conventions

The vault is the single source of truth for what the team is building. Code answers *how*; the
vault answers *what* and *why*. When they disagree, neither automatically wins — the disagreement
itself is the finding worth reporting.

## Locating the vault

Resolve in this order and stop at the first hit:

1. `$CCG_VAULT` environment variable.
2. A `.ccg-vault` file in the current project root whose single line is the vault path.
3. `~/Documents/Obsidian Vault/projects/ccg`
4. A sibling clone: `../ccg-vault`

If none resolve, stop and ask the user for the path rather than guessing. Never create the vault
structure implicitly — a missing vault means a misconfigured machine, not a new project.

Refer to the resolved path as `{vault}` below.

## Layout

```
{vault}/
  00-inbox/            BA's source documents, as delivered. Read-only. Never edited by anyone.
  02-specs/            Approved, validated specs. THE source of truth for developers.
  reports/             Validation reports from /ba-check. Disposable, regenerated on demand.
  notes/glossary.md    Domain vocabulary. One definition per term.
  notes/decisions.md   Cross-cutting Q&A that outlived a single feature.
  _index.md            Feature list with current status.
```

There is deliberately no `01-questions/` folder — clarification happens in-session through
elicitation, and its conclusions land in the spec itself.

## Spec frontmatter

Every file in `02-specs/` opens with:

```yaml
---
feature: Supplier Evaluation Report
status: draft | in-review | approved | implemented
source: "[[00-inbox/supplier-eval-brief.md]]"
owner: <BA name>
updated: 2026-09-16
jira: CCG-1234
---
```

`status` drives the whole workflow:

- **draft** — BA is still writing. Developers do not read it.
- **in-review** — `/ba-check` has run and findings are open.
- **approved** — validated, gaps closed, safe to build against. **Only `approved` specs are implementable.**
- **implemented** — shipped; kept for traceability.

If a developer asks about a spec that is not `approved`, say so plainly and name the current
status instead of answering from a draft. A draft answer that turns out to be wrong costs more
than the wait.

## Required spec sections

```markdown
## Context            Why this exists. One paragraph.
## Scope              What is in. Bullet list.
## Non-Goals          What is deliberately out. Bullet list. Absent = suspicious.
## Business Rules     Numbered. Each one testable.
## Acceptance Criteria
## Open Questions     Genuinely unanswered. Empty is fine; missing is not.
## Decisions          Append-only Q&A log.
```

## Acceptance criteria format

Numbered, stable IDs, one testable statement each. These are the contract `/dev-review` checks
code against, so vagueness here is what makes the whole loop useless.

```markdown
- AC-1: A supplier with no completed evaluations shows "Not evaluated", not a score of 0.
- AC-2: Only users holding the SupplierReview claim can submit an evaluation.
- AC-3: Soft-deleted suppliers are excluded from the evaluation report.
```

An AC is well-formed when you can name the input and the observable outcome. "The report should
be fast" is not an AC; "the report returns within 3s for 5000 suppliers" is. IDs are permanent —
when an AC is withdrawn, mark it `~~AC-4 (withdrawn 2026-09-16)~~` rather than renumbering, or
every PR description that cites an AC number silently starts lying.

## The decisions log

This is what stops developers interrupting the BA with the same question twice. Every answered
question gets appended, never edited:

```markdown
## Decisions
- **Q:** Do inactive suppliers appear in the dropdown? **A:** No. — BA, 2026-09-14
- **Q:** Score rounding, up or nearest? **A:** Nearest, 2 dp. — BA, 2026-09-15
```

**Before telling a developer to ask the BA anything, search `02-specs/` and `notes/` first.**
Most questions have already been answered once. When you do answer from the log, cite the entry
so the developer can judge how stale it is.

## Reading the vault

Specs are plain markdown, so ordinary file tools work — no Obsidian required. Use `grep -ri`
across `{vault}/02-specs` and `{vault}/notes` for a term before concluding something is
unspecified. Wikilinks (`[[glossary#Evaluation Cycle]]`) resolve to files under `{vault}`.

Say "the spec does not cover this" only after searching the glossary and decisions log as well.
Unspecified and not-yet-found are very different findings, and only one of them is the BA's problem.
