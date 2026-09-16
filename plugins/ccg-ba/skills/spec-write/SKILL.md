---
name: spec-write
description: >-
  Start a new spec in the vault from the CCG template, or complete an existing draft section by
  section. Interviews the BA one decision at a time rather than generating a document to proofread.
  Use when a BA is starting a feature or filling gaps a check reported. Trigger: new spec, start a
  feature, write the spec, draft requirements, ba-new, fill in the spec, complete this section.
  Do NOT use to validate a finished doc (use spec-check).
---

# Spec Write

Build the spec **with** the BA, one section at a time. Do not generate a finished document for
them to proofread — a BA reviewing generated prose skims and approves; a BA answering a direct
question thinks.

Load `vault-conventions` first for paths, frontmatter and the AC format.

## Starting a new spec

1. Ask for the feature name and, if there is a source document in `00-inbox/`, read it first.
2. Create `{vault}/02-specs/<kebab-feature-name>.md` from `assets/spec-template.md` with
   `status: draft`.
3. Work the sections in template order. Do not jump ahead — later sections depend on decisions
   made in earlier ones.

## How to interview

Ask about **one decision at a time**, and prefer a closed question with a recommended default
over an open one:

> Deleted suppliers in the evaluation report:
> **(a)** excluded entirely *(recommended — matches how the supplier list already behaves)*
> **(b)** included, marked as deleted
> **(c)** included only if they have historic evaluations

A BA answers fifteen of those in twenty minutes. Three open questions cost them a day and produce
vaguer answers.

Where a default exists in the current system's behaviour, say so and cite it — "recommended
because X already works this way" is the single most useful thing you can offer, and it stops the
team accidentally specifying two different behaviours for the same concept.

When the BA does not know, do not guess and move on. Record it under Open Questions with who needs
to answer it, or offer to run `elicit` on that section. An honest open question is worth more than
a confident invention, because only one of them gets caught before code is written.

## Writing rules

- Business rules are numbered and testable. If you cannot name an input and an outcome, it is
  not a rule yet — keep asking.
- Acceptance criteria use stable `AC-n` IDs. Never renumber.
- Do not write Non-Goals for the BA. Ask what is deliberately out, then record their answer.
  An invented non-goal is a scope decision you had no authority to make.
- Never fabricate a number. "Within 3 seconds" must come from the BA or be marked
  `[ASSUMPTION: 3s — not confirmed]`.
- Leave the Decisions log empty at creation. It fills up during development.

## Closing

When the template is filled, do not declare it done. Say what is still thin, then recommend
`/ba-check` — which runs with clean context and will find things this session could not, because
this session watched them being written.
