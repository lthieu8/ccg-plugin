---
name: intake
description: >-
  Turn a document dropped in the vault inbox into a finished spec with acceptance criteria - read
  it, find every gap, ask the BA all the unclear questions in one batched round, then write the
  spec to 02-specs/. The BA's one command. Use when a document has been copied to 00-inbox/, or to
  start a spec from nothing. Trigger: intake, new doc in inbox, process this document, ba-intake,
  turn this into a spec, write the spec from this, start a feature.
  Do NOT use to re-validate a spec that already exists (use spec-check).
---

# Intake

One pass from a document in the inbox to a spec in `02-specs/`. The BA copies a file and runs one
command; everything between is your job.

Load `vault-conventions` first for paths, frontmatter and the acceptance-criteria format.

## Shape of the run

1. Read the document and its surroundings.
2. Analyse it for gaps — in a subagent, with clean context.
3. Ask the BA **every** unclear question, in one batch.
4. Write the spec with numbered acceptance criteria.
5. Report what is still open, then commit and push.

Steps 3 and 4 are where this succeeds or fails. Get all the questions out at once and the BA
spends twenty minutes; dribble them out one at a time and it takes a day, which is the bottleneck
this exists to remove.

## 1. Read

Read the source document in full. Then read, because they change what counts as a gap:

- `notes/glossary.md` — a term defined there is not an ambiguity.
- Existing specs in `02-specs/` that touch the same entities. **If this feature changes behaviour
  another spec already defines, that is the most important thing you will find.** Say so early;
  do not let it surface after the spec is written.
- `notes/decisions.md` for cross-cutting rules that already constrain this.

If no document was given, skip to step 3 and interview from scratch.

## 2. Analyse

Dispatch a subagent. Give it the `spec-check` skill's rubric and omission sweep, and this task:

> Read the rubric and the omission sweep from the `spec-check` skill, then the document at
> `<path>` and the glossary. Judge each rubric dimension, then walk the sweep and list every
> category the document does not address, separating deliberate exclusions from silent absence.
> Return findings with quoted specifics, ranked by whether the feature can ship without an answer.

Clean context matters here even though you did not write the document: once you have read it
closely enough to plan questions, its ambiguities start reading as obvious.

## 3. Ask everything, once

Convert the findings into a single numbered list. This is the BA's whole turn — make it
answerable.

- **Closed questions with a recommended default.** Where the current system or an existing spec
  already implies an answer, say so and cite it:

  > **7.** Deleted suppliers in the evaluation report:
  > **(a)** excluded entirely *(recommended — `02-specs/supplier-list.md` AC-3 excludes them from
  > the supplier list)*
  > **(b)** included, marked as deleted
  > **(c)** included only where they have historic evaluations

- **Group by area** — permissions, existing data, lifecycle, reporting. A BA answering eight
  permission questions in a row thinks better than one hopping between topics.
- **Mark each blocking or not.** Blocking means the spec cannot be written without it. The BA is
  allowed to skip the rest.
- **Cap at twenty.** If the sweep produced more, keep the blocking ones and the highest-impact
  others, and say what you set aside. A list nobody finishes is worse than a shorter one they do.
- **Never ask what the document already answers.** Every question of that kind costs you the BA's
  trust in the other nineteen.

Then stop and wait. One follow-up round is allowed, but only for questions that genuinely depend
on an answer just given — not for things you should have asked the first time.

## 4. Write the spec

Write `{vault}/02-specs/<kebab-feature-name>.md` from `assets/spec-template.md`.

- **Business rules numbered and testable.** If you cannot name an input and an outcome, it is not
  a rule yet — it belongs in Open Questions.
- **Acceptance criteria with stable `AC-n` ids**, one observable outcome each. These are what
  `/dev-review` checks code against, so vagueness here makes the whole downstream check useless.
- **Non-Goals come from the BA's answers**, never from you. An invented non-goal is a scope
  decision you had no authority to make.
- **Never fabricate a number.** A threshold the BA did not give is written
  `[ASSUMPTION: 3s — not confirmed]` or not at all.
- **Anything unanswered goes to Open Questions**, named, with who needs to answer it. Do not
  resolve it with a sensible guess — a guess here becomes a business rule nobody agreed to, and it
  will not be caught until a developer builds it.
- Leave `## Decisions` empty. It fills during development.

## 5. Close

Report, briefly:

- the spec path and how many acceptance criteria it has
- what is still in Open Questions and who needs to answer it
- any conflict found with an existing spec

Then commit and push the vault so the team has it:

```bash
git -C {vault} add -A && git -C {vault} commit -m "Spec: <feature>" && git -C {vault} push
```

Do not claim the spec is complete when Open Questions is non-empty. Say what is settled and what
is not — developers will hit the gap either way, and knowing about it now is the difference
between a question and a rewrite.

If the BA wants a deeper pass on a section they know is thin, point them at `/ba-elicit`.
