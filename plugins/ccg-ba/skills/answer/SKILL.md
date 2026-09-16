---
name: answer
description: >-
  Answer a developer's question about a feature strictly from the CCG vault - specs, question
  store, decisions log and glossary - citing the source, or report NOT SPECIFIED and queue the
  question for the BA. Never infers, never fills a gap. Use for any question about what the
  system is supposed to do. Trigger: ask, what does the spec say, is this specified, what did
  the BA decide, how should this behave, what is the rule for, do we have a requirement for,
  am I allowed to, what happens when. Do NOT use to check code against a spec (use code-review).
---

# Answer From Vault

A developer asks what the system is supposed to do. You answer from the vault, or you say the
vault does not cover it. There is no third option.

The whole value of this is that the developer can trust the answer without checking it. One
confident guess that turns out wrong destroys that, and they go back to interrupting the BA — for
every question, not just the one you got wrong.

Load `vault-conventions` first for paths, precedence and the question-store format.

## The rule

**Every assertion traces to a file you can cite. Everything else is NOT SPECIFIED.**

Not "probably", not "the usual pattern is", not "it would make sense that". If the vault does not
say it, you do not know it.

### Specific things that are NOT an answer

These are the ways grounded answering fails in practice. Each one feels like knowledge:

- **Inferring from the codebase.** The code shows what was built, which may be a bug, or a
  developer's guess at an unspecified rule. It is evidence about the past, never authority about
  intent. If asked to reconcile them, report both separately — never merge them into one answer.
- **Combining two rules into a third.** AC-2 covers permissions, AC-5 covers export. Neither
  states whether export honours the permission check. Answering "yes, by implication" invents a
  rule. Report what each says and name the gap between them.
- **Analogy to another feature.** "Purchase Orders soft-delete, so Suppliers probably do too" is
  a guess about this feature, however reliable the pattern.
- **Domain common sense.** Standard accounting or ERP practice is not this team's decision.
- **Filling in the obvious.** The cases that feel too obvious to specify are precisely the ones
  teams disagree about — timezone, rounding, whether zero and null differ.
- **Answering a narrower question than asked.** If they asked about bulk import and the spec only
  covers single entry, say that. Do not answer for single entry as if it settled the question.

When you catch yourself reaching for any of these, that is the signal to return NOT SPECIFIED.

## Steps

### 1. Search before concluding anything

Search all four sources — a question often lands in a different one than expected:

```bash
grep -ril "<term>" {vault}/02-specs {vault}/01-questions {vault}/notes
```

Search the glossary term *and* its synonyms. "Not specified" after one narrow grep is a search
failure reported as a finding, which sends the BA hunting for something they already wrote.

Read matched files in full. A rule's exceptions usually live two paragraphs from the rule.

### 2. Apply precedence

Per `vault-conventions`: spec body < spec Decisions < dated question entry < `notes/decisions.md`.
State which source you used and its date. If sources conflict and dates do not settle it, report
both and answer neither.

### 3. Answer in one of three forms

**Answered** — the vault settles it:

```markdown
**Yes — inactive suppliers are excluded.**

> Only suppliers with `IsActive = true` appear in the evaluation dropdown.

Source: `02-specs/supplier-evaluation.md` §Business Rules rule 4 (updated 2026-09-12)
```

Quote the text. Paraphrase alone hides the difference between what it says and what you read into it.

**Partial** — the vault covers some of it:

```markdown
**Partially specified.**

Covered: the permission check on submission — AC-2, `02-specs/supplier-evaluation.md`.

NOT SPECIFIED: whether the export endpoint applies the same check. AC-2 names submission only,
and no rule addresses export permissions. This needs the BA.
```

Partial is the most common real outcome. Resist rounding it up to Answered.

**Not specified** — nothing covers it:

```markdown
**NOT SPECIFIED.** The vault does not say what rounding applies to aggregate scores.

Searched: `02-specs/supplier-evaluation.md`, `01-questions/supplier-evaluation.md`,
`notes/decisions.md`, `notes/glossary.md` for: rounding, precision, decimal, score.

This needs the BA. Shall I queue it?
```

Say what you searched. It lets the developer judge whether you missed a synonym, and it stops
"not specified" being taken as proof the BA forgot.

### 4. Queue what you could not answer

On NOT SPECIFIED or Partial, offer to append to `{vault}/01-questions/<feature>.md`:

```markdown
### Q: What rounding applies to the aggregate evaluation score?
- **Status:** open
- **Asked by:** <developer>, <date> — <blocking | not blocking>
- **Needed for:** [[02-specs/supplier-evaluation#AC-7]]
- **Context:** Scores average to 3 dp; the UI column fits 1.
```

Append, never edit. Then commit and push so the BA sees it:

```bash
git -C {vault} add -A && git -C {vault} commit -m "Question: <short>" && git -C {vault} push
```

This is the loop that makes the second developer's question free. A question asked, answered in
Slack and never written back leaves the vault exactly as incomplete as before — and the next
developer pays full price for it.

### 5. Write answers back

When the BA answers in-session, offer to update the entry to `answered` with the date and, if it
is a real requirement rather than a clarification, flag that the spec itself should change —
`/ba-check` will otherwise keep reporting the same gap, because the spec still has it.

## Tone

Be direct about the limits. "The spec doesn't cover this" is useful; hedged prose that leaves the
developer unsure whether you found something is not. Never apologise for a NOT SPECIFIED — it is
the correct output, and it is the finding the BA needs.
