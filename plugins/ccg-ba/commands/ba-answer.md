---
description: Work through the open questions developers have queued for you
argument-hint: "[feature; omit for all open questions]"
---

Show and resolve open developer questions.

Scope: $ARGUMENTS

Load `vault-conventions`. Then:

1. Pull the vault first (`git -C {vault} pull --ff-only`) so you see questions queued since the
   last session.
2. Find every entry with `**Status:** open` in `{vault}/01-questions/`. Group by feature, and put
   entries marked blocking first — someone is waiting on those.
3. For each, show the question, who asked, what it blocks, and the relevant spec section so the
   BA has the context without opening anything.
4. Take the BA's answer. Where a closed question would help, offer options with a recommended
   default rather than an open prompt — it is faster to answer and produces a sharper decision.
5. Update the entry in place to `**Status:** answered` with the answer, the BA's name and today's
   date. Append; never rewrite the question.
6. **If the answer is a real requirement rather than a clarification, say so and recommend
   updating the spec itself.** An answer that lives only in the question store leaves the spec
   still wrong, and `/ba-check` will keep reporting the same gap.
7. Commit and push the vault.

If nothing is open, say so plainly. Do not invent questions to fill the session.
