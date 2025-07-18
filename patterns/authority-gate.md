# Pattern: Authority Gate

**Intent.** Decide whether the actor behind a proposal is *permitted* to perform
the requested action at the requested scope — before any approval routing or
execution is considered.

## Context

A validated proposal has arrived from the [Proposal Gate](proposal-gate.md). It
names an action and parameters. The actor is an agent acting on behalf of a
principal. The question now is strictly one of permission.

## Problem

Capability is not permission. An agent that *can* form a `create_vendor_record`
proposal is not thereby *allowed* to create vendors — and certainly not at any
scope. Without an explicit authority check, the ability to propose becomes the
ability to act.

## Solution

A deterministic gate that evaluates `authorized(actor, proposal)` against
declarative policy:

1. **Resolve grants** for the actor and its principal.
2. **Enforce the delegation invariant** — the agent's authority must be a
   *subset* of the principal's. If the proposal exceeds what the principal could
   do, reject regardless of the agent's own grants.
3. **Match action + scope** — the actor must hold a grant for this action at (or
   above) the requested scope band.
4. **Evaluate conditions** — predicates over the proposal (thresholds, data
   class, time windows) must hold.
5. **Emit a verdict** — `authorized` or `rejected(reason)`, always recorded.

## Structure

```
Proposal ──► [ resolve grants ] ──► [ delegation subset? ] ──► [ action@scope? ] ──► [ conditions? ]
                                          │ no                    │ no                  │ no
                                          ▼                       ▼                     ▼
                                     reject(exceeds)         reject(unauth)       reject(condition)
                                                                                        │ all yes
                                                                                        ▼
                                                                                   AUTHORIZED
                                                                              (→ approval routing)
```

## Invariants

- **Ordered before approval.** Authority is checked first. An unauthorized
  proposal never reaches approval routing — you cannot approve your way past a
  missing grant.
- **Delegation is a hard ceiling.** No agent grant can exceed the principal's
  authority. This bounds the worst case.
- **Declarative, reviewable policy.** The grants and conditions live in a
  human-readable policy (see the synthetic
  [`authority-policy.yaml`](../notes/02-authority-and-approval/authority-policy.yaml)),
  not scattered in code.
- **Fail closed.** No matching grant means reject, never "allow by default."

## Related

- Note 02 — [Authority and Approval](../notes/02-authority-and-approval/)
- [Human Approval Gate](human-approval-gate.md) — runs only *after* authorization
- [Execution Gate](execution-gate.md)
