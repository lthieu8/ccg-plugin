---
name: spec-check
description: >-
  Validate a finished BA specification and report what is MISSING, ambiguous or untestable,
  without rewriting it. Runs a quality rubric plus a systematic omission sweep for the
  requirement categories BAs habitually forget. Use when a BA has written a document and wants
  to know what they left out, before developers see it. Trigger: check my spec, validate spec,
  review the requirements, what did I miss, is this complete, gap analysis, ba-check, spec
  quality, is this ready for dev. Do NOT use to write a spec (use intake) or to review code
  (use code-review).
---

# Spec Check

The BA has written what they believe is a complete document. Your job is to find what is not
there. This is a critique, not an edit — you never rewrite the spec, you report on it.

Load `vault-conventions` first for paths, frontmatter and the acceptance-criteria format.

## Discipline

**Do not validate a document you drafted in this same session.** Self-review produces
self-confirmation: the gaps you missed while writing are exactly the gaps you will miss while
checking. Dispatch the review to a subagent with clean context (see step 2). If the user insists
on an inline check, say once that it is a weaker check, then do it.

**Report, never repair.** A finding the BA reads and fixes teaches them where their blind spots
are. A gap you silently fill teaches them nothing and puts your guess in the spec under their
name. Offer to draft fixes only after the report is delivered and only for findings the user
picks.

**Be specific or say nothing.** "The permissions section is underspecified" is noise. "§Business
Rules does not say whether a user who loses the SupplierReview claim keeps access to evaluations
they already submitted" is a finding. Quote the text, cite the section. Abstract criticism is
failure of nerve.

## Steps

### 1. Orient

Read the spec in full, plus `notes/glossary.md` and the source document in `00-inbox/` if it is
referenced. A requirement that looks missing is often defined in the glossary — check before you
claim it is absent.

Establish the stakes with the user if unclear: a small internal change and a customer-facing
billing change do not deserve the same bar. Calibrate severity to stakes, not to how easy a fix is.

### 2. Dispatch the reviewer

Spawn one subagent with this task:

> Read the quality rubric at `references/rubric.md` and the omission sweep at
> `references/gap-sweep.md`. Then read the spec at `<path>` and the glossary. For each rubric
> dimension form a judgment — strong / adequate / thin / broken — backed by quoted specifics.
> Then walk the gap sweep and list every category the spec does not address, distinguishing
> "deliberately out of scope" (it says so) from "silently absent". Write findings only where
> they add information. Return the report in the format the rubric specifies.

For a large or high-stakes spec, run a second subagent over the gap sweep alone, in parallel —
the sweep is mechanical and benefits from an independent pass. Merge the two before reporting.

### 3. Report

Write to `{vault}/reports/<feature>-check.md` and summarise in conversation. Lead with the
verdict and the count of blocking findings, then the findings by severity. Do not bury a critical
finding under six low ones.

Findings take the form:

```markdown
- **[critical]** Existing evaluations have no migration rule (§Business Rules)
  The spec defines the new 1-5 scoring scale but never says what happens to the ~2 years of
  evaluations already stored on the old 1-10 scale. Reports spanning the cutover will mix scales.
  *Fix:* state whether historic scores are converted, displayed as-is with a marker, or excluded.
```

Severity ranks impact on the spec's usefulness, not the effort to fix. A one-word ambiguity in an
acceptance criterion can be critical; a glossary inconsistency across ten sections can be low.

### 4. Close

Offer, in this order:

1. **Elicit** — run the `elicit` skill against a specific weak section. This is the right next
   step when the BA knows the gap exists but cannot yet answer it.
2. **Draft fixes** — for findings the user picks, propose replacement text they approve.
3. **Re-check** — re-run after edits. The report overwrites in place.

If every finding is low severity and no dimension is thin or broken, say the spec is ready and
remind the BA to commit and push the vault. Do not manufacture findings to look thorough — a clean spec that passes
is a real outcome, and inventing concerns to fill a report destroys the BA's trust in every
future report.
