# Pattern: Execution Gate

**Intent.** Be the *single* place where a proposal becomes a side effect on a
system of record — and guarantee that every effect is preceded and followed by a
durable record.

## Context

A proposal has passed the [Proposal Gate](proposal-gate.md), been authorized by
the [Authority Gate](authority-gate.md), and either auto-approved or approved via
the [Human Approval Gate](human-approval-gate.md). Only now may it run.

## Problem

If side effects can originate from more than one place — a fast path here, a
direct tool call there — the audit story has holes and the gates can be bypassed.
And if the effect happens without a bracketing record, a crash leaves an
unaccountable gap (see failure scenario S2 in note 03).

## Solution

A gate that is the *sole* execution authority:

1. **Re-validates preconditions.** Confirms authority still holds and any human
   approval is within its TTL. State may have moved since approval.
2. **Independently classifies reversibility.** Never trusts the agent's claim;
   re-derives it and refuses to run an action classified irreversible without the
   approval that class requires.
3. **Writes the intent record first.** `phase: "intent"` — persisted *before* the
   side effect (write-before-effect).
4. **Performs the side effect** against the system of record.
5. **Writes the outcome record.** `phase: "outcome"` — success or failure, with
   the concrete effect and correlation IDs.
6. **Returns the result** to the agent as data — including on rejection or
   failure.

## Structure

```
approved proposal ──► [ re-check authority + approval TTL ]
                              │ stale/invalid → reject (recorded)
                              ▼
                    [ re-classify reversibility ]
                              │ irreversible w/o required approval → reject
                              ▼
                    [ WRITE intent record ]  ◄── before any effect
                              ▼
                    [ side effect on System of Record ]
                              ▼
                    [ WRITE outcome record ]  ◄── after effect (success or failure)
                              ▼
                         result → agent
```

## Invariants

- **Single choke point.** All side effects go through here. No side path, no
  "trusted" caller that skips it.
- **Write-before-effect, write-after-effect.** Every effect is bracketed. A crash
  leaves an intent record with no outcome — detectable, never silent.
- **Reversibility is the control plane's call.** The agent's claim is advisory
  only.
- **Fail closed on stale approval.** If the approving state has changed past TTL,
  re-evaluate rather than execute on an old sign-off.

## Related

- Note 01 — [Proposal vs. Execution](../notes/01-proposal-vs-execution/)
- Note 03 — [Agent Audit Trace](../notes/03-agent-audit-trace/)
- [Decision Record](decision-record.md)
