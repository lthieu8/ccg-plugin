# Omission Sweep

The rubric judges the quality of what was written. This sweep finds what was never written at
all — the categories requirements go missing from most often in a brownfield ERP.

Walk every category. For each, record one of:

- **Covered** — the spec addresses it. Move on, do not report.
- **Excluded** — the spec explicitly says it is out of scope. Not a finding; list it so the BA
  can confirm the exclusion was deliberate.
- **Absent** — the spec neither addresses nor excludes it. **This is a finding.** Severity
  depends on whether the feature can ship without an answer.
- **N/A** — genuinely does not apply. Say why in one clause; a lazy N/A is how gaps survive.

Do not report Absent for a category the feature plainly cannot touch. A read-only report screen
has no concurrency story and saying so wastes the BA's attention.

---

## Existing data

- What happens to records that already exist when this ships? Backfill, default, or leave null?
- If a field or scale changes, how are historic values displayed — converted, marked, or excluded?
- Can old and new coexist in the same report or export, and is that intelligible to the user?
- Is there a cutover date, and what does the boundary look like to someone reading the data?

*This is the single most commonly missed category in brownfield work.*

## Permissions and visibility

- Which roles or claims can see this? Which can change it?
- What does a user without permission see — hidden, greyed, or an error?
- What happens to work already submitted by a user whose permission is later revoked?
- Are there record-level rules (own-records-only, branch, department) on top of role rules?

## States and lifecycle

- Every state the entity can be in, and every legal transition between them.
- Can the action be undone, cancelled, reopened, resubmitted, superseded?
- What is forbidden once a record reaches a terminal state?
- Is there an approval step, and what happens on rejection?

## Empty and boundary states

- First run, before any data exists — what does the screen show?
- Zero results, one result, and a very large number of results.
- Null versus zero: are they distinguishable, and should they be?
- Maximum lengths, maximum rows, maximum file size.

## Deletion

- Hard delete or soft delete? The spec must say which.
- Do deleted records disappear from reports, totals and averages retroactively?
- What happens to child records and to references from elsewhere?
- Can a deleted record be restored, and by whom?

## Validation and error paths

- Every rule that can reject input, and the exact message the user sees.
- Is validation client-side, server-side, or both?
- What happens on partial failure in a multi-step or bulk operation — all, nothing, or partial?
- Are error messages localised?

## Concurrency

- Two users editing the same record at once — last-write-wins, or a conflict shown?
- Anything that must not be double-submitted (approvals, postings, numbering).
- Long-running operations: what does a second user see while one is in flight?

## Audit and history

- Does this need a who-changed-what-when trail?
- Is the previous value retained, or only the fact of a change?
- Who can read the audit trail?
- Any retention or legal-hold requirement?

## Reporting, export and print

- Does this appear in any existing report, and does that report's logic need to change?
- Export formats, and whether exports respect the same permission rules as the screen.
- Print or PDF layout, if the entity is customer-facing.

## Localisation

- Does new user-facing text need translation, and into which languages?
- Number, date, and currency formatting per locale.
- Do sort orders or comparisons depend on locale?

## Notifications

- Does anything here need to notify anyone — email, in-app, or both?
- Who receives it, when, and can they opt out?
- What happens if delivery fails?

## Integration

- Does any other system consume this data, and does the change break it?
- Are there API contracts, webhooks, or scheduled jobs that read these fields?
- Anything downstream that assumes the current shape of the data?

## Scale and performance

- Realistic production volume for this entity, today and in two years.
- Any operation that grows with row count — list, report, export, aggregate.
- A stated response-time expectation at that volume, not in the abstract.

## Rollout

- Is this behind a toggle, or on for everyone at deploy?
- Can it be turned off after release without data loss?
- Does anyone need training or a migration window?
- Does it need to go live at a particular time, such as a period boundary?

## Attachments and files

- Allowed types, size limits, and where they are stored.
- Virus scanning or content restrictions.
- What happens to attachments when the parent record is deleted.

## Numbers and money

- Rounding rule, and precision, stated explicitly.
- Currency: which one, and whether conversion is involved.
- Timezone for every stored and displayed timestamp.
- Any sequence or document number, and whether gaps are acceptable.

---

## Reporting the sweep

Group by severity, not by category. An absent deletion rule on a financial record outranks an
absent notification rule on an internal note. For each Absent item give the probing question
verbatim — the BA can often answer it in one line, and a question is easier to act on than an
observation.
