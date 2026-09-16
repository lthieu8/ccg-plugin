# ccg-ba

A Claude Code plugin for the CCG BA → developer workflow.

The BA writes a document. Claude finds what is missing from it before developers do. The validated
spec lands in the shared vault — and from then on developers **ask Claude** instead of asking the
BA. Claude answers from the vault, citing it, or says the vault does not cover it and queues the
question. Nobody browses the vault by hand.

Adapted from [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) (MIT): the elicitation
method catalogue and the spec quality rubric derive from upstream. The vault conventions, the
omission sweep, the grounded answering and the code review are CCG-specific. The heavy BMAD runtime
(`uv`, `_bmad/` config, Python scripts) is deliberately not carried over — this plugin is plain
skills and commands with no external dependencies, so it installs on a BA machine with no dev
toolchain.

---

## Quickstart

### 1. Install the plugin

From an interactive `claude` terminal session:

```
/plugin marketplace add C:\Users\hieu.luu\Downloads\Project\ccg-workflow
```

```
/plugin install ccg-ba@ccg-workflow
```

Once this repo is on a git host, replace the local path with `owner/repo` so the team installs the
same way and updates with `/plugin marketplace update ccg-workflow`.

### 2. Get the vault

Developers clone it:

```bash
git clone https://github.com/lthieu8/ccg-vault.git
```

The BA already has it at `Documents/Obsidian Vault/projects/ccg`. Everything is plain markdown —
Obsidian is the BA's editor, not a requirement for anyone else.

### 3. Tell the plugin where the vault is

Set `CCG_VAULT`, or put a `.ccg-vault` file containing the path in your project root. Without it
the plugin falls back to `~/Documents/Obsidian Vault/projects/ccg`, then a sibling `../ccg-vault`.

```powershell
[Environment]::SetEnvironmentVariable('CCG_VAULT', 'C:\Users\you\ccg-vault', 'User')
```

### 4. Put `CLAUDE.md` in your source repo

This is what makes Claude use the vault for every question, not just when someone remembers to
type `/ask`. Copy the template into the root of the repo you work in:

```bash
cp <ccg-workflow>/plugins/ccg-ba/templates/CLAUDE.md <your-repo>/CLAUDE.md
```

If the repo already has a `CLAUDE.md`, paste the template's sections into it rather than
overwriting — you will lose the repo's own conventions otherwise.

It instructs Claude to answer questions about intended behaviour **only** from the vault, and to
say plainly that the vault does not cover something rather than filling the gap with a guess. It
also stops Claude treating the current code as the requirement, which is the failure mode that
quietly turns an old bug into a specification.

Commit it — every developer on the repo should get the same rules.

### 5. Check it works

```
/ask what features are specified so far?
```

If it asks where the vault is, step 3 did not take.

---

## Commands

### Developer side

| Command | Does |
|---|---|
| `/ask <question>` | Answers from the vault with a citation, or returns **NOT SPECIFIED** and queues the question. The one you will use most. |
| `/dev-review [spec]` | Checks your changes against the spec's acceptance criteria, one verdict each |

### BA side

| Command | Does |
|---|---|
| `/ba-new [feature or inbox doc]` | Starts a spec from the template and interviews you through it |
| `/ba-check [spec]` | Validates a finished spec and reports what is missing, ambiguous or untestable |
| `/ba-elicit [section]` | Pressure-tests a weak section with structured reasoning methods |
| `/ba-answer [feature]` | Works through the questions developers have queued for you |
| `/ba-approve [spec]` | Promotes a spec to `approved`, commits and pushes the vault |

---

## How it works

### Developers ask, they do not browse

```
/ask does the evaluation export honour the SupplierReview permission?
```

Claude searches the specs, the question store, the decisions log and the glossary, then answers in
one of three forms:

- **Answered** — with the text quoted and the file, section and date cited.
- **Partially specified** — what is covered, and precisely what is not. This is the most common
  real outcome.
- **NOT SPECIFIED** — with the list of what was searched, and an offer to queue the question.

A queued question is appended to `01-questions/<feature>.md` and pushed. The BA clears the queue
with `/ba-answer`, and from then on that question is free for everyone else.

### The no-assumption rule

Every assertion traces to a file Claude can cite. Everything else is NOT SPECIFIED. The skill
explicitly forbids the ways grounded answering usually fails:

- inferring the rule from what the code currently does
- combining two rules into a third that neither one states
- reasoning by analogy from a similar feature
- applying ERP or accounting convention as if it were this team's decision
- filling in whatever seems too obvious to specify — timezone, rounding, null-versus-zero, which
  are exactly the things teams disagree about
- answering a narrower question than the one asked

This matters more than it looks. The value of the vault is that a developer can act on an answer
without verifying it. One confident wrong answer ends that, and they go back to interrupting the
BA for everything — not just the question that was wrong.

### If the BA already has a finished document

1. Drop it into `00-inbox/`.
2. `/ba-check` against it.
3. Work the findings; `/ba-elicit` on anything the BA cannot answer alone.
4. `/ba-approve` when the report is clean.

About thirty minutes. Starting from scratch instead: `/ba-new` first, same finish.

### What `/ba-check` looks for

Two passes, catching different things.

**A quality rubric** — five dimensions (done-ness clarity, scope honesty, decision-readiness,
consistency, substance over theater) — judges what was written. It catches untestable criteria,
missing non-goals, adjectives where numbers belong.

**An omission sweep** finds what was never written, walking sixteen categories requirements go
missing from: existing data and migration, permissions, lifecycle states, empty and boundary
states, deletion, validation and error paths, concurrency, audit, reporting and export,
localisation, notifications, integration, scale, rollout, attachments, numbers and money.

The sweep is what catches "you never said what happens to the two years of evaluations already in
the system". A rubric structurally cannot — nothing in the document is wrong.

### Why checks run in a subagent

You cannot reliably check your own draft: the gaps you missed writing are the ones you miss
checking, and an ambiguity reads unambiguously to whoever wrote it. `/ba-check` dispatches to a
subagent with clean context. `/dev-review` does the same when the session wrote the code.

---

## Conventions the plugin enforces

**Only `status: approved` specs are implementable.** An answer drawn from a draft is always
labelled provisional.

**Acceptance criteria have stable `AC-n` IDs, never renumbered.** `/dev-review` reports per ID and
PRs cite them; renumbering silently invalidates every past reference.

**The question store and decisions log are append-only.** A superseded answer gets a new entry
saying so. Overwriting destroys the record of why the earlier code was written that way.

**Claude reports, it does not repair.** `/ba-check` never edits a spec; `/ba-elicit` never applies
a change the BA did not accept. A gap filled silently is Claude's guess wearing the BA's name.

---

## Skills

Invoked by the commands; callable directly.

| Skill | Purpose |
|---|---|
| `vault-conventions` | Paths, frontmatter, status lifecycle, AC format, source precedence. Loaded by all the others. |
| `answer` | Grounded question answering with citations |
| `spec-write` | Draft or complete a spec by interview |
| `spec-check` | The rubric and omission sweep |
| `elicit` | 71 structured reasoning methods |
| `code-review` | Conformance of a diff to a spec |

## Templates

`templates/CLAUDE.md` — drop into a source repo so Claude there answers behaviour questions from
the vault and never from assumption. See quickstart step 4. The rules in it stand on their own:
a repo with this file but without the plugin still gets grounded answers, it just searches the
vault directly instead of through `/ask`.

`skills/spec-write/assets/spec-template.md` — the spec skeleton `/ba-new` starts from.

---

## Limits worth knowing

**`/ask` is only as complete as the vault.** NOT SPECIFIED means the vault does not cover it, not
that the answer does not exist. Undocumented domain knowledge stays undocumented until someone
asks and the BA answers.

**`/ba-check` is not codebase-aware.** It reads the spec, not the code, so it cannot catch
"this contradicts how `SupplierService` already handles soft deletes". On the BA machine that is
by design. Give it a repo on a dev machine and it does better.

**`/dev-review` checks conformance, not correctness.** Code can satisfy every acceptance criterion
and still be wrong. Tests and ordinary code review still apply.

**The loop only closes if queued questions get answered.** If `01-questions/` fills with open
entries nobody clears, developers learn that `/ask` returns NOT SPECIFIED and go back to Slack.
`/ba-answer` is the habit that keeps this alive.
