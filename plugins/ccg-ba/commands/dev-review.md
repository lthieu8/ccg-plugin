---
description: Review your changes against the spec in the vault, per acceptance criterion
argument-hint: "[spec name; omit to match by branch or ticket]"
---

Review the current change against its specification.

Target spec: $ARGUMENTS

Use the `code-review` skill. Load `vault-conventions` first.

If no spec was named, match the current branch name or ticket id against `jira:` in the
`02-specs/` frontmatter, and confirm the match with the user before reviewing.

Establish the diff against the branch point, not just the working tree, and say which you
reviewed. Dispatch to a subagent with clean context if this session wrote the code.

Report a verdict per acceptance criterion with file:line evidence, plus anything implemented that
no criterion asked for. Check conformance to the spec only — not code quality.
