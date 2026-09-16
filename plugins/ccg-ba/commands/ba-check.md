---
description: Validate a finished spec and report what is missing, ambiguous or untestable
argument-hint: "[spec name or path; omit to pick from 02-specs/]"
---

Run a specification gap check.

Target: $ARGUMENTS

Use the `spec-check` skill. Load `vault-conventions` first.

If no target was given, list the specs in `02-specs/` with their status and ask which one.

Dispatch the review to a subagent with clean context, per the skill's discipline section — do not
review inline a spec that was drafted in this session.

Report findings. Do not edit the spec.
