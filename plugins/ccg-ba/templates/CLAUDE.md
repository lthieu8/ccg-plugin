# Working in this repository

<!--
  CCG vault rules. Copy this file to the root of a source repository as CLAUDE.md,
  or paste this section into an existing CLAUDE.md.
  Set CCG_VAULT, or edit the path below to point at your clone of ccg-vault.
-->

## The specification vault is the source of truth for behaviour

Requirements for this codebase live in the CCG vault, not in the code and not in your training
data. Resolve it in this order: `$CCG_VAULT`, a `.ccg-vault` file in this repo, then
`~/Documents/Obsidian Vault/projects/ccg`, then `../ccg-vault`. If none resolve, say so and stop —
do not answer from the code instead.

```
{vault}/02-specs/       approved specs — what the system must do
{vault}/01-questions/   questions already asked, answered and open
{vault}/notes/decisions.md   cross-cutting decisions
{vault}/notes/glossary.md    domain vocabulary
```

## Never answer a behaviour question from assumption

When asked what the system is *supposed* to do — a business rule, a validation, a permission, an
edge case, what happens to existing data — search the vault and answer only from what it says.

**If the vault does not cover it, say the vault does not cover it.** That is a correct and useful
answer. A plausible guess is not: the developer cannot tell the two apart, and will act on it.

Do not derive a requirement from any of these. Each one feels like knowledge and is not:

- **What the code currently does.** That shows what was built — possibly a bug, possibly an
  earlier developer's guess at an unspecified rule. It is evidence about the past, never authority
  about intent. Asked to reconcile code and spec, report both separately; never merge them.
- **Two rules combined into a third.** If one rule covers permissions and another covers export,
  and neither says whether export checks permissions, that is a gap — not an implication.
- **Another feature's behaviour.** However consistent the pattern, it is a guess about this one.
- **ERP, accounting or domain convention.** Standard practice is not this team's decision.
- **Whatever seems too obvious to specify.** Timezone, rounding, null-versus-zero, what a delete
  does to history — these are the things teams actually disagree about.

## How to answer

Search `02-specs/`, `01-questions/`, `notes/decisions.md` and `notes/glossary.md` — all four, with
synonyms of the key terms — before concluding anything. Read matched files in full; a rule's
exceptions usually sit two paragraphs from the rule.

Then answer in exactly one of three forms:

**Answered** — quote the text and cite the file, section, status and date.

**Partially specified** — state what is covered and precisely what is not. This is the most common
real outcome; do not round it up.

**NOT SPECIFIED** — say so, list what you searched, and offer to queue the question for the BA in
`{vault}/01-questions/<feature>.md`. Saying what you searched lets the developer judge whether you
missed a synonym.

Where two sources conflict, later wins: spec body < spec `## Decisions` < a dated question entry <
`notes/decisions.md`. If dates cannot settle it, report both and answer neither.

## Only approved specs are implementable

A spec's frontmatter carries `status`. Only `approved` is safe to build against. If a spec is
`draft` or `in-review`, say what the status is instead of answering from it — and if you do quote
it, label the answer provisional.

## Before implementing

Read the spec's acceptance criteria first. They have stable `AC-n` ids; cite them in commits and
pull requests. If a criterion is ambiguous, ask rather than choosing a reading — a guess here
becomes an undocumented business rule in the codebase, which is exactly what the vault exists to
prevent.

If the work needs behaviour no acceptance criterion covers, stop and queue the question. Do not
fill the gap with your own judgment.

## Commands

The `ccg-ba` plugin provides these. Without it, the rules above still apply — you just search the
vault directly.

- `/ask <question>` — answer from the vault with a citation, or NOT SPECIFIED
- `/dev-review [spec]` — check the current diff against the spec's acceptance criteria
