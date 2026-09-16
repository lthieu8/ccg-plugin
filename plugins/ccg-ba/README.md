# ccg-ba

The BA writes a document. Claude finds what is missing before developers do. The result lands in a
shared vault, and from then on developers **ask Claude** instead of asking the BA — answered from
the vault with a citation, or reported as not specified. Never guessed.

Adapted from [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) (MIT) — see
[NOTICE.md](../../NOTICE.md).

---

## Setup

**1. Install the plugin** — from an interactive `claude` session:

```
/plugin marketplace add lthieu8/ccg-plugin
/plugin install ccg-ba@ccg-workflow
```

**2. Clone the vault**

```bash
git clone https://github.com/lthieu8/ccg-vault.git
```

**3. Point the plugin at it** — set `CCG_VAULT`, or drop a `.ccg-vault` file holding the path in
your repo root:

```powershell
[Environment]::SetEnvironmentVariable('CCG_VAULT', 'C:\Users\you\ccg-vault', 'User')
```

**4. Copy `CLAUDE.md` into your source repo** — this is what applies the rules to every question,
not just the ones prefixed with `/ask`. Paste the sections in if the repo already has one.

```bash
cp <plugin>/templates/CLAUDE.md <your-repo>/CLAUDE.md
```

**5. Obsidian — optional.** The vault is plain markdown in git, so nothing depends on it.
Recommended for the BA (wikilinks, backlinks, frontmatter); developers can skip it, since the
point is to ask rather than browse. If the BA uses it, add the **Obsidian Git** community plugin
set to auto-pull and auto-commit so they never touch the CLI.

---

## Commands

| Command | Who | Does |
|---|---|---|
| `/ask <question>` | dev | Answers from the vault with a citation, or **NOT SPECIFIED** and queues the question |
| `/dev-review [spec]` | dev | Checks your diff against the spec's acceptance criteria, one verdict each |
| `/ba-new [feature]` | BA | Starts a spec and interviews you through it, one decision at a time |
| `/ba-check [spec]` | BA | Reports what the spec is missing, ambiguous about, or cannot test |
| `/ba-elicit [section]` | BA | Pressure-tests a weak section with structured reasoning methods |
| `/ba-answer [feature]` | BA | Works through the questions developers have queued |

## Skills

| Skill | Does |
|---|---|
| `vault-conventions` | Vault paths, frontmatter, AC format, source precedence. Loaded by the others. |
| `answer` | Grounded answering — cite the vault or say NOT SPECIFIED, never infer |
| `spec-write` | Draft or complete a spec by interview |
| `spec-check` | Quality rubric (5 dimensions) plus an omission sweep over 16 categories requirements go missing from |
| `elicit` | 71 structured reasoning methods — pre-mortem, inversion, Socratic, assumption audit |
| `code-review` | Conformance of a diff to a spec, per `AC-n` |

---

## Example

```
/ask does the evaluation export honour the SupplierReview permission?
```

```
Partially specified.

Covered: the permission check on submission — AC-2, 02-specs/supplier-evaluation.md.

NOT SPECIFIED: whether the export endpoint applies the same check. AC-2 names submission
only, and no rule addresses export permissions.
Searched: 02-specs/, 01-questions/, notes/decisions.md, notes/glossary.md for:
export, permission, SupplierReview, claim.

This needs the BA. Shall I queue it?
```

Answering "yes, by implication" would have invented a requirement. That is the behaviour this
plugin exists to prevent — a developer cannot tell a confident guess from a real answer, and will
build on it either way.

---

## First document, end to end

The BA has finished a requirements document. About thirty minutes from here to a spec developers
can build from.

**1. Drop it in** — copy the document to `{vault}/00-inbox/supplier-eval-brief.md`.

**2. Check it**

```
/ba-check supplier-eval-brief
```

Claude reads it in a clean subagent and reports two passes. The **rubric** judges what was written
— untestable criteria, missing non-goals, adjectives where numbers belong. The **omission sweep**
finds what was never written: existing data and migration, permissions, lifecycle states,
deletion, concurrency, audit, localisation, rounding, rollout and more. The sweep is what catches
*"you never said what happens to the two years of evaluations already in the system"* — a rubric
structurally cannot, because nothing in the document is wrong.

**3. Work the findings.** Answer what you can directly. For anything you know is thin but cannot
articulate:

```
/ba-elicit the scoring section
```

**4. Write the spec** — `/ba-new` turns the corrected document into `02-specs/supplier-evaluation.md`
with numbered, testable `AC-n` criteria. Re-run `/ba-check` until it is clean.

**5. Publish** — commit and push the vault. Everything in it is source of truth; there is no
approval gate.

**6. Developers take over.** They `/ask` instead of messaging you. Whatever Claude cannot answer
is queued in `01-questions/`, and you clear it with `/ba-answer`. Before raising a PR they run
`/dev-review`, which reports covered / partial / missing / contradicted per `AC-n` with file:line
evidence.

---

## Limits

- **`/ask` is only as complete as the vault.** NOT SPECIFIED means the vault does not cover it,
  not that no answer exists.
- **`/ba-check` does not read code**, so it cannot catch a spec that contradicts existing
  behaviour. On the BA machine that is by design; give it a repo and it does better.
- **`/dev-review` checks conformance, not correctness.** Code can satisfy every criterion and
  still be wrong. Tests and ordinary review still apply.
- **The loop only closes if queued questions get answered.** If `01-questions/` fills with open
  entries nobody clears, developers learn that asking returns nothing and go back to Slack.
