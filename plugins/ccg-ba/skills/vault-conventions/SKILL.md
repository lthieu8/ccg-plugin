---
name: vault-conventions
description: >-
  Shared rules for locating and reading the CCG vault - folder layout, spec frontmatter, the
  status lifecycle, acceptance-criteria format, the question store and the precedence order
  between sources. Load before reading or writing anything in the vault. Use whenever a question
  touches a feature, spec, business rule, or "what did the BA say about X". Trigger: vault, spec,
  specification, requirement, acceptance criteria, AC-, business rule, glossary, question store,
  what does the doc say, is this specified.
---

# CCG Vault Conventions

The vault is the retrieval substrate the team asks questions against. It is **not** a wiki people
browse. A developer asks Claude; Claude answers from the vault or reports that the vault does not
cover it. Nobody is expected to go looking manually, and no answer is expected to come from
anywhere else.

That puts the whole burden on grounding. See the `answer` skill for the discipline — the summary
is: everything you assert comes from a file you can cite, and everything else is **NOT SPECIFIED**.

## Locating the vault

Resolve in this order and stop at the first hit:

1. `$CCG_VAULT` environment variable.
2. A `.ccg-vault` file in the project root whose single line is the vault path.
3. `~/Documents/Obsidian Vault/projects/ccg`
4. A sibling clone: `../ccg-vault`

If none resolve, stop and ask. Never create the structure implicitly — a missing vault means a
misconfigured machine, not a new project. Referred to below as `{vault}`.

Pull before answering if the vault is a git clone (`git -C {vault} pull --ff-only`). An answer
from a stale clone is worse than no answer, because it carries the same confidence.

## Layout

```
{vault}/
  00-inbox/            BA source documents, as delivered. Read-only.
  01-questions/        Q&A store, one file per feature. Answered and open questions.
  02-specs/            Validated specs. The primary source of truth.
  reports/             Gap-check output. Disposable.
  notes/glossary.md    Domain vocabulary.
  notes/decisions.md   Cross-cutting answers that outlived one feature.
  _index.md            Feature list with status.
```

## Precedence between sources

When two sources address the same question, the later one in this list wins, and you say which
you used:

1. Spec body (`02-specs/`)
2. Spec `## Decisions` section — later by construction
3. `01-questions/` entry marked answered, if dated after the spec's `updated:`
4. `notes/decisions.md` for cross-cutting rules

If two sources conflict and dates cannot settle it, **report both and answer neither**. Picking
one is an assumption wearing a citation.

## Spec frontmatter

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

`status` governs how an answer may be used:

- **draft** — BA still writing. Answers drawn from it are provisional and must say so.
- **in-review** — checked, findings open. Same caveat.
- **approved** — validated. **Only `approved` specs are implementable.**
- **implemented** — shipped, kept for traceability.

Never quote a draft without stating that it is a draft. A developer who builds on a draft answer
has been misled by the omission, not by the content.

## Required spec sections

```markdown
## Context            Why this exists.
## Scope              What is in.
## Non-Goals          What is deliberately out. Absent is suspicious.
## Business Rules     Numbered. Each testable.
## Acceptance Criteria
## Open Questions     Genuinely unanswered.
## Decisions          Append-only Q&A.
```

## Acceptance criteria format

Numbered, stable `AC-n` IDs, one testable statement each — a nameable input and an observable
outcome. These are the contract `/dev-review` checks code against.

```markdown
- AC-1: A supplier with no completed evaluations shows "Not evaluated", not a score of 0.
- AC-2: Only users holding the SupplierReview claim can submit an evaluation.
```

IDs are permanent. Withdraw with `~~AC-4 (withdrawn 2026-09-16)~~` rather than renumbering —
renumbering silently invalidates every PR description and review that cited the old number.

## The question store

`01-questions/<feature>.md` holds every question asked about that feature, answered or not. This
is what makes the second developer's question free.

```markdown
### Q: Do inactive suppliers appear in the evaluation dropdown?
- **Status:** answered
- **A:** No. Only suppliers with `IsActive = true`.
- **Answered by:** Hieu (BA), 2026-09-14
- **Relates to:** [[02-specs/supplier-evaluation#AC-2]]

### Q: What rounding applies to the aggregate score?
- **Status:** open
- **Asked by:** Nam (dev), 2026-09-16 — blocking
- **Needed for:** [[02-specs/supplier-evaluation#AC-7]]
```

Entries are **append-only**. When an answer changes, add a new entry that supersedes the old one
and say so. Overwriting destroys the record of why the earlier code was written the way it was.

An open entry is not a failure — it is the mechanism working. A question the BA has not answered
is exactly the thing that should be visible and queued, rather than guessed at.
