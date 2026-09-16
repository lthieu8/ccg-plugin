---
description: Promote a spec to approved so developers can build against it
argument-hint: "[spec name or path]"
---

Approve a specification.

Target: $ARGUMENTS

Load `vault-conventions`. Then, before changing anything:

1. Read the spec and its most recent report in `{vault}/reports/`.
2. Refuse to approve if any of these hold, and say which:
   - no check has been run, or the report predates the spec's last edit
   - the report has open critical or high findings
   - Open Questions is non-empty
   - any acceptance criterion has no nameable input and observable outcome
3. If it passes, set `status: approved`, update `updated:` to today, and add the feature to
   `_index.md`.
4. Commit and push the vault so the team sees it:
   `git -C {vault} add -A && git -C {vault} commit -m "Approve spec: <feature>" && git -C {vault} push`

Approval is the gate the whole workflow rests on. If it is granted while findings are open, the
status field stops meaning anything and developers go back to asking the BA directly.
