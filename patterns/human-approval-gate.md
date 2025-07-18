# Pattern: Human Approval Gate

**Intent.** Route an *authorized* proposal to a real person for sign-off when
policy demands it, and turn their decision into a recorded, load-bearing part of
the trace.

## Context

The [Authority Gate](authority-gate.md) has already said "this actor may do
this." The approval decision table (see note 02) has flagged this *specific
instance* as requiring human review — because it's irreversible, over a
threshold, touches sensitive data, is low-confidence, or is novel.

## Problem

Human-in-the-loop is easy to implement as theater: a notification, a button, no
context, no record. That satisfies a checkbox and provides no actual control. A
real approval gate must make the human's judgment *informed* and *accountable*.

## Solution

A gate that:

1. **Presents full context.** The approver sees the proposal, its rationale, the
   control-plane classification (reversibility, value band, data class), and the
   reason it was routed to them. Never a bare "approve action X?".
2. **Captures an authenticated decision.** Approve or deny, tied to a real
   identity — not a role label.
3. **Enforces four-eyes where required.** For flagged action classes, the
   approver must differ from the initiator/principal.
4. **Records both outcomes.** Approval and denial each write a
   [Decision Record](decision-record.md) with identity, timestamp, and comment.
5. **Feeds denials back as constraints.** A denial returns to the agent as a
   constraint to respect, not an error to retry verbatim.
6. **Expires approvals.** Past the TTL, the approval is stale; the
   [Execution Gate](execution-gate.md) must re-evaluate against current state.

## Structure

```
authorized + routed ──► [ present proposal + rationale + classification ]
                                        │
                              approver (authenticated, four-eyes-checked)
                                        │
                        ┌───────────────┴───────────────┐
                     approve                           deny
                        │                                │
              record(approved, TTL)            record(denied, comment)
                        │                                │
                 → Execution Gate            constraint fed back to agent
                (re-check if TTL stale)          (no silent re-roll)
```

## Invariants

- **Context or it doesn't count.** An approval made without seeing the proposal
  and its classification is not a valid approval.
- **Real identity, always.** No "operator." No shared accounts standing in for a
  person.
- **Denial ≠ retry.** The same proposal is not resubmitted hoping for a softer
  reviewer; it comes back as a constraint.
- **Approvals are perishable.** They authorize execution against the state they
  were shown, not against an arbitrarily-later state.

## Related

- Note 02 — [Authority and Approval](../notes/02-authority-and-approval/)
- [Decision table](../notes/02-authority-and-approval/decision-table.md) — what routes here
- [Execution Gate](execution-gate.md)
