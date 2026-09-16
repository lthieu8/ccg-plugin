---
name: code-review
description: >-
  Review code changes against the approved spec in the Obsidian vault and report per acceptance
  criterion whether it is covered, partial, missing or contradicted. The developer-side half of
  the workflow. Use before raising a PR, or when asked whether an implementation matches what the
  BA asked for. Trigger: dev-review, review against spec, does this match the spec, spec
  compliance, did I implement everything, check my changes against the requirements, acceptance
  criteria check. Do NOT use for general code quality or bug hunting - this checks conformance to
  the spec only.
---

# Code Review Against Spec

You are checking one thing: does this change do what the approved spec says? Not whether the code
is good — other reviewers cover that. Conformance only.

Load `vault-conventions` first to resolve the vault and read the spec.

## Discipline

**Clean context.** If you implemented this change in this session, you are the wrong reviewer:
you will check the code against your memory of the spec rather than the spec itself, and confirm
your own reading of an ambiguity. Dispatch to a subagent that reads the spec fresh and sees only
the diff.

**Approved specs only.** If `status` is not `approved`, stop and say so. Reviewing against a draft
gives a developer false confidence in requirements the BA has not signed off.

**Verdict per AC, with evidence.** Every claim cites a file and line. "AC-2 looks handled" is
worthless; "AC-2 covered — claim checked in `SupplierController.cs:88`" can be disputed and
therefore trusted.

## Steps

### 1. Establish the target

- Resolve the spec. If the user did not name one, match the branch or ticket against
  `02-specs/` frontmatter `jira:`, and confirm the match before proceeding.
- Establish the diff: `git diff <base>...HEAD` against the branch point, not the working tree
  alone. Uncommitted work counts — say explicitly which you reviewed.
- Read the full spec, including Decisions. **A decisions-log entry overrides the body of the
  spec** where they conflict — it is more recent by construction.

### 2. Check each acceptance criterion

For every `AC-n`, assign exactly one verdict:

- **Covered** — implemented, with file:line evidence.
- **Partial** — the happy path works but a stated condition is unhandled. Name the condition.
- **Missing** — no implementation found. Say where you looked; a missing AC is sometimes a
  search failure, and claiming otherwise sends the developer on a hunt for code that exists.
- **Contradicted** — the code does something the AC forbids, or the opposite of what it requires.
  This is the most valuable finding in the report. Quote both the AC and the code.
- **Not verifiable statically** — needs a running system or data. Say what would verify it rather
  than guessing.

Then check the Business Rules the same way. Rules without a matching AC are a spec weakness worth
reporting back.

### 3. Check the other direction

Behaviour in the diff that **no** acceptance criterion asked for. Each one is either:

- scaffolding or refactoring — fine, note briefly and move on;
- a developer filling a spec gap with their own judgment — **report it**. This is how undocumented
  business rules enter the codebase, and it is precisely the thing the vault exists to prevent.

### 4. Report

```markdown
## Spec conformance — {feature} ({spec path})

**Covered 6 · Partial 1 · Missing 1 · Contradicted 0 · Unverifiable 1**

### Missing
- **AC-4** — Soft-deleted suppliers excluded from the report.
  No filter on `IsDeleted` in `SupplierEvaluationService.cs:112-140`. Searched the query builder
  and the report projection.

### Partial
- **AC-2** — Permission check present in the controller
  (`SupplierController.cs:88`) but the export endpoint at line 141 has no equivalent check.

### Implemented but unspecified
- Evaluation scores are rounded to 2dp in `ScoreCalculator.cs:31`. The spec does not state a
  rounding rule. Confirm with the BA and add it to the Decisions log.
```

Lead with the counts. A developer wants to know in one line whether they are done.

### 5. When the spec is wrong

Sometimes the code is right and the spec is stale or mistaken. Do not silently defer to either
side, and do not edit the spec — the BA owns it.

Report it as a conflict and draft the decisions-log entry for the BA to approve:

```markdown
**Proposed Decisions entry for {spec path}:**
- **Q:** AC-3 says deleted suppliers are excluded, but the reconciliation report needs them for
  historic totals. Which wins? **A:** <pending BA> — raised by <dev>, 2026-09-16
```

Getting that answer written down once is the entire point of the vault. An answer given in Slack
and never filed will be asked again within the month.
