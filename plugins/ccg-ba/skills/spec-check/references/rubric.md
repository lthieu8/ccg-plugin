# Spec Quality Rubric

Adapted from the BMAD-METHOD PRD quality rubric (MIT), trimmed to five dimensions and
recalibrated for internal ERP feature specs rather than consumer product PRDs.

Form a judgment per dimension — **strong / adequate / thin / broken** — backed by quoted
specifics. Write findings only where they add information: a `strong` dimension may need none, a
`broken` one needs concrete, fixable ones. Calibrate to the stakes; not every dimension deserves
equal scrutiny on every spec.

---

## 1. Done-ness clarity

Would a developer reading this know what "done" looks like for every rule?

Look for:
- Every business rule carrying at least one testable consequence.
- Acceptance criteria with a nameable input and an observable outcome.
- Bounds instead of adjectives on anything non-functional. "Fast", "user-friendly", "handles X
  gracefully", "reasonable" — flag every occurrence.
- Numbers wherever a number is implied: limits, timeouts, page sizes, retention, precision.

Be unforgiving here. This is the dimension `/dev-review` leans on entirely — an untestable AC
means the whole downstream check is theatre.

## 2. Scope honesty

Are the omissions explicit, or is the reader meant to infer them?

Look for:
- A Non-Goals section doing real work. Its absence on anything non-trivial is itself a finding.
- Assumptions stated as assumptions, not smuggled in as fact.
- Open Questions that are genuinely open — not rhetorical questions answered in the next sentence.
- De-scoping done openly rather than silently.

The failure mode: a spec that reads complete because everything uncertain was quietly dropped.

## 3. Decision-readiness

Can someone act on this, or does it defer every hard call?

Look for:
- Decisions stated as decisions, not buried as "considerations".
- Trade-offs naming what was given up, not only what was chosen.
- Conflicts with existing behaviour surfaced rather than ignored.

Red flag: every choice "balances" everything and no option was ever rejected.

## 4. Consistency and usability

Can a developer, or the next spec, source from this cleanly?

Look for:
- Every domain noun used identically throughout, and present in the glossary.
- AC and rule IDs unique, contiguous, and referenced consistently.
- Each section intelligible when read alone — cross-references by term, not "see above".
- No contradiction between two sections. Where one exists, quote both.

## 5. Substance over theater

Is the content earned, or is it furniture?

- **Boilerplate NFRs** — "must be secure / scalable / reliable" with no product-specific threshold.
- **Restated context** — background paragraphs that repeat the ticket title at length.
- **Rules that aren't rules** — statements with no decidable outcome.
- **Copied structure** — sections present because the template had them, empty of content.

Flag what reads like furniture, even when it is well-written furniture.

---

## Output format

```markdown
# Spec Check — {feature}

**Verdict:** [Ready | Minor gaps | Significant gaps | Not ready]
**Blocking findings:** {n critical/high}

## Overall
[2-3 sentences. What holds up, what is at risk. Earned by the judgments below.]

## Dimensions
- Done-ness clarity — {verdict}
- Scope honesty — {verdict}
- Decision-readiness — {verdict}
- Consistency and usability — {verdict}
- Substance over theater — {verdict}

## Findings

### Critical (n)
- **[critical]** {Title} (§ {section})
  {What is wrong or missing, with quoted text.}
  *Fix:* {concrete suggestion}

### High (n) / Medium (n) / Low (n)
...

## Omission sweep
{Categories from gap-sweep.md that are silently absent. Deliberate exclusions listed separately.}

## Not findings
{Anything checked and found sound that the BA might expect to be flagged. Brief.}
```

Grade: **Ready** = all dimensions strong/adequate, no high or critical · **Minor gaps** = one thin
dimension, no critical · **Significant gaps** = multiple thin, or any high · **Not ready** = any
broken dimension or any critical finding.
