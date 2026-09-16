---
name: elicit
description: >-
  Pressure-test a draft, a section, or a decision using a catalogue of 71 structured reasoning
  methods - pre-mortem, inversion, Socratic questioning, stakeholder round table, assumption
  audit and others. Use when the BA knows something is thin but cannot articulate what, or when
  spec-check flagged a section as weak. Trigger: elicit, dig deeper, pressure test, stress test,
  challenge this, what am I missing here, pre-mortem, red team, socratic, first principles,
  play devil's advocate, ba-elicit.
---

# Elicit

A refinement checkpoint. The BA has something written that they suspect is thin. Instead of
asking them to "think harder", you apply a named reasoning method that attacks the work from a
specific angle, and show them what it surfaced.

Adapted from BMAD-METHOD's advanced elicitation skill (MIT). The method catalogue is the upstream
one; the orchestration is simplified — no scripts, no external runtime.

## The target

Whatever the user points at: a spec section, a business rule, an open question, a decision.
Default to the most recent output in the conversation. Fix the target before serving the menu and
do not let it drift mid-session — a method applied to a moving target produces mush.

## Serving the menu

The catalogue lives at `assets/methods.csv` (relative to this skill), with columns
`num,category,method_name,description,output_pattern`. Do not read it whole into context. Read
the categories, then only the rows you need:

```bash
cut -d, -f2 assets/methods.csv | tail -n +2 | sort | uniq -c
grep '^[0-9]*,risk,' assets/methods.csv
```

Categories: `advanced`, `collaboration`, `competitive`, `core`, `creative`, `framing`, `learning`,
`philosophical`, `research`, `retrospective`, `risk`, `technical`.

Pick 2-4 categories that fit the target, list them, then hand-pick **five methods that attack
from different angles**. Five similar methods waste the user's turn.

Fit heuristics:
- Requirements that feel incomplete → `risk` (Pre-mortem, Assumption Audit), `framing`
  (Reframe the Question, Stakeholder Lens Rotation)
- A decision nobody is sure about → `core` (Inversion, Second-Order Thinking), `competitive`
- Competing stakeholder needs → `collaboration` (Stakeholder Round Table, Six Thinking Hats)
- A rule that might have holes → `risk` (Boundary & Edge Case Sweep, Failure Mode Analysis)
- Content that is flat or obvious → `creative`, `framing` (Abstraction Laddering)

Then HALT and offer:

- The five methods, by name with a one-line description.
- **Reshuffle** — five different ones, excluding everything already offered.
- **List all** — the full catalogue as a compact table.
- **Proceed** — stop eliciting.

Any other reply is direction: apply it and offer the menu again.

## Running a method

Use the method's `description` as its intent and `output_pattern` as a loose flow guide. Scale
depth to the target — a single business rule gets a light pass, a scope decision gets the full
treatment. Each method works on the current enhanced version, so passes compound.

Show what the method **revealed**, then what it **proposes**. Keep those separate: the revelation
is the value, the proposal is a suggestion the BA may reject while keeping the insight.

Then HALT:

- **Apply** — accept the proposed changes.
- **Reject** — drop the proposal entirely.
- Or give different direction.

**Never change the work unless the user accepts.** This skill exists to make the BA think, not to
write the spec for them. A proposal applied without their say-so is your judgment wearing their
name, and they will find out at the worst possible moment — when a developer builds it.

When a method casts personas (round tables, panels, debates), invent named viewpoints that fit
the CCG domain — a warehouse supervisor, a finance controller, an auditor — rather than generic
"Stakeholder A".

## Closing

On **Proceed**, hand the enhanced version back. If the caller was `spec-check` or `intake`,
resume where it paused. If anything shown was never explicitly accepted, confirm what carries
over before returning — silent carry-over is how unreviewed text ends up in the spec.
