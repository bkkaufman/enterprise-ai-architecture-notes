# Approval Decision Table

The authority gate answers *may this actor do this at all* (note 02). This table
covers what happens **after** authorization succeeds: does a human need to
approve *this specific instance* before the execution gate runs it?

Read top to bottom. **The first matching row wins.** If nothing matches, the
default (last row) applies.

| # | Reversibility | Value / breadth | Data class | Agent confidence | Novel? | → Route |
|---|---------------|-----------------|------------|------------------|--------|---------|
| 1 | irreversible | any | any | any | any | **Human approval (required)** |
| 2 | any | any | regulated / sensitive | any | any | **Human approval (required)** |
| 3 | any | over threshold (e.g. > $50k, or > N records) | any | any | any | **Human approval (required)** |
| 4 | any | any | any | any | yes (out of calibrated distribution) | **Human approval (required)** |
| 5 | any | any | any | below policy band (e.g. < 0.75) | any | **Human approval (required)** |
| 6 | reversible | within threshold | internal / public | at/above band | no | **Auto-approve (in control plane)** |
| — | *default* | | | | | **Human approval (required)** |

Notes on reading the table:

- **Default is human, not auto.** The safe default when no auto-approve row
  matches is to route to a person. Autonomy is the exception you *earn* by
  matching row 6, not the baseline.
- **Rows are conjunctive within, disjunctive across.** Row 6 requires *all* of
  its cells to hold. Rows 1–5 each fire on their *single* condition — any one is
  enough to force human review.
- **Thresholds are policy, not code constants.** The `$50k` and `0.75` here mirror
  [`authority-policy.yaml`](authority-policy.yaml). Changing them is a reviewable
  policy change.

## Four-eyes overlay

Independent of the table above, certain action classes carry a **four-eyes**
requirement: the person who approves must be different from the person (or the
principal) who initiated. This applies even when a row would otherwise auto-approve.

| Action class | Four-eyes? | Rationale |
|--------------|-----------|-----------|
| `vendor.approve_elevated` | yes | elevated credit / non-standard terms |
| `payment.release` (illustrative) | yes | direct financial disbursement |
| `vendor.create:standard-band` | no | routine, within standard band |

## Approval lifecycle rules

- **Context required.** An approval request must present the proposal, its
  rationale, and its control-plane classification. "Approve action X?" with no
  context is not a valid approval prompt.
- **Recorded either way.** Approve and deny both write a
  [decision record](../../patterns/decision-record.md) with approver identity,
  timestamp, and comment.
- **No silent re-roll.** A denial returns to the agent as a constraint. The agent
  may propose a *different* action; it may not resubmit the same one hoping for a
  different reviewer.
- **Approvals expire.** Past `approval_ttl_minutes`, the approval is stale and the
  execution gate must re-evaluate — the world may have changed since sign-off.
