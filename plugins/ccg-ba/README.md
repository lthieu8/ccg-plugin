# ccg-ba

A Claude Code plugin for the CCG BA → developer workflow.

The BA writes a document. Claude finds what is missing from it before developers do. The validated
spec lands in a shared Obsidian vault, and developers check their code against it — so questions
get answered once, in writing, instead of every time someone new picks up the ticket.

Adapted from [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) (MIT): the elicitation
method catalogue and the spec quality rubric are derived from upstream. Everything else — the
vault conventions, the omission sweep, the code-review side — is CCG-specific. The heavy BMAD
runtime (`uv`, `_bmad/` config, Python scripts) is deliberately not carried over; this plugin is
plain skills and commands with no external dependencies.

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
same way and gets updates with `/plugin marketplace update ccg-workflow`.

### 2. Get the vault

Developers clone it:

```bash
git clone https://github.com/lthieu8/ccg-vault.git
```

The BA already has it at `Documents/Obsidian Vault/projects/ccg` and opens the parent folder in
Obsidian. Everything is plain markdown — Obsidian is a convenience, not a requirement.

### 3. Tell the plugin where the vault is

Set `CCG_VAULT` to the vault path, or drop a `.ccg-vault` file containing that path in the project
root. Without it the plugin falls back to `~/Documents/Obsidian Vault/projects/ccg`, then to a
sibling `../ccg-vault` clone.

```powershell
[Environment]::SetEnvironmentVariable('CCG_VAULT', 'C:\Users\you\ccg-vault', 'User')
```

### 4. Check it works

```
/ba-check
```

It should list the specs in the vault. If it asks you where the vault is, step 3 did not take.

---

## Commands

### BA side

| Command | Does |
|---|---|
| `/ba-new [feature or inbox doc]` | Starts a spec from the template and interviews you through it, one decision at a time |
| `/ba-check [spec]` | Validates a finished spec and reports what is missing, ambiguous or untestable. **The main one.** |
| `/ba-elicit [section]` | Pressure-tests a weak section with structured reasoning methods |
| `/ba-approve [spec]` | Promotes a spec to `approved`, commits and pushes the vault |

### Developer side

| Command | Does |
|---|---|
| `/dev-review [spec]` | Reviews your changes against the spec's acceptance criteria, one verdict each |

Developers can also just ask — "what does the spec say about supplier deletion?" — and the
`vault-conventions` skill will have Claude search the vault, including the decisions log, before
answering.

---

## How to use it

### If the BA already has a finished document

This is the common case, and the whole point of the plugin.

1. Drop the document into `00-inbox/` in the vault.
2. Run `/ba-check` against it.
3. Work the findings. For anything you cannot answer alone, `/ba-elicit` on that section.
4. `/ba-approve` when the report is clean.

Roughly thirty minutes. The output is a spec a developer can build from without asking you
anything.

### If starting from scratch

`/ba-new` → `/ba-check` → `/ba-approve`. Same finish, one extra step at the front.

### What `/ba-check` actually looks for

Two passes, because they catch different things:

**A quality rubric** (five dimensions — done-ness clarity, scope honesty, decision-readiness,
consistency, substance over theater) judges what you *did* write. It catches untestable criteria,
missing non-goals, adjectives where numbers belong.

**An omission sweep** finds what was never written at all, walking sixteen categories requirements
habitually go missing from: existing data and migration, permissions, lifecycle states, empty and
boundary states, deletion, validation and error paths, concurrency, audit, reporting and export,
localisation, notifications, integration, scale, rollout, attachments, numbers and money.

The sweep is the one that catches "you never said what happens to the two years of evaluations
already in the system." A rubric cannot find that, because nothing in the document is wrong.

### Why the check runs in a subagent

You cannot reliably check your own draft: the gaps you missed while writing are the ones you miss
while checking, and an ambiguity reads unambiguously to whoever wrote it. `/ba-check` dispatches to
a subagent with clean context that reads the spec as a stranger would. `/dev-review` does the same
when the current session wrote the code.

---

## Conventions the plugin enforces

**Only `status: approved` specs are implementable.** If a developer asks about a draft, Claude says
what the status is instead of answering from it.

**Acceptance criteria have stable `AC-n` IDs and are never renumbered.** `/dev-review` reports per
ID and PR descriptions cite them, so renumbering makes every past reference wrong.

**The decisions log is append-only.** Every answer the BA gives gets written down once. This is the
part that actually reduces interruptions — and the part most likely to lapse. If devs keep asking
in Slack and nobody files the answer, the vault decays into a folder.

**Claude reports, it does not repair.** `/ba-check` never edits your spec and `/ba-elicit` never
applies a change you did not accept. A gap Claude fills silently is Claude's guess wearing your
name, and you find out when a developer builds it.

---

## Skills

Invoked automatically by the commands; you can also call them directly.

| Skill | Purpose |
|---|---|
| `vault-conventions` | Vault paths, frontmatter, status lifecycle, AC format. Loaded by all the others. |
| `spec-write` | Draft or complete a spec by interview |
| `spec-check` | The rubric and omission sweep |
| `elicit` | 71 structured reasoning methods |
| `code-review` | Conformance of a diff to a spec |

---

## Limits worth knowing

**The check is not codebase-aware.** It reads the spec, not the code, so it cannot catch "this
contradicts how `SupplierService` already handles soft deletes." On the BA machine that is by
design — the BA has no clone. On a dev machine, give it a repo and it will do better.

**`/dev-review` checks conformance, not correctness.** Code can satisfy every acceptance criterion
and still be wrong. Tests and a normal code review still apply.

**A clean report is not proof of a complete spec.** It means nothing in sixteen known-risky
categories is silently absent. Domain knowledge nobody wrote down stays missing.
