---
description: Turn a document in the inbox into a finished spec with acceptance criteria
argument-hint: "[doc name in 00-inbox/; omit to pick, or to start from nothing]"
---

Process a new BA document into a spec.

Source: $ARGUMENTS

Use the `intake` skill. Load `vault-conventions` first.

If no source was given, list what is in `{vault}/00-inbox/` and ask which one — or offer to start
from nothing if the inbox is empty.

Analyse the document in a subagent with clean context, using the `spec-check` skill's rubric and
omission sweep. Then ask the BA **every** unclear question in one numbered batch — closed
questions with a recommended default, grouped by area, each marked blocking or not, capped at
twenty. Do not ask one at a time.

Then write the spec to `02-specs/` with numbered, testable `AC-n` criteria, put anything still
unanswered in Open Questions rather than guessing, and commit and push the vault.
