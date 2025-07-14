# Pattern: Proposal Gate

**Intent.** Turn raw agent output into a validated, normalized **proposal** —
the only currency the control plane accepts. Nothing downstream ever sees free
model output; it sees a proposal or it sees nothing.

## Context

An agent produces text and/or tool-call-shaped output. That output is
untrustworthy in three ways: it may be malformed, it may claim things it can't
substantiate (e.g. reversibility), and it may reference actions or scopes outside
the enumerated set. The proposal gate is the first checkpoint after the model.

## Problem

If unvalidated model output flows directly into authority checks or execution,
every downstream gate has to defend against garbage input. Worse, the model can
smuggle in fields (an inflated confidence, a false "reversible" claim) that later
gates might trust.

## Solution

A deterministic gate that:

1. **Parses** the output into a proposal object, rejecting anything that doesn't
   fit the schema.
2. **Constrains `action`** to the enumerated action set. Unknown actions are
   rejected outright, not "best-effort mapped."
3. **Fully resolves parameters.** No deferred lookups, no "the executor will
   figure out the account." A proposal is complete or it's rejected.
4. **Strips authority.** The gate treats agent-asserted fields (`reversibility`,
   `confidence`) as *claims*, tagging them as unverified. Downstream gates
   re-derive these independently.
5. **Records the rejection** if it rejects — a malformed proposal is still a
   decision (see [Decision Record](decision-record.md)).

## Structure

```
agent output ──► [ parse ] ──► [ enum-check action ] ──► [ resolve params ]
                                                              │
                              reject (recorded) ◄── invalid ──┤
                                                              ▼
                                                    normalized Proposal
                                                    (agent claims tagged unverified)
```

## Invariants

- **No side effects.** The proposal gate never touches a system of record.
- **Agent claims are never load-bearing.** `reversibility` and `confidence` are
  inputs to *routing*, re-verified by the control plane; they are never accepted
  as ground truth.
- **Fail closed.** Ambiguity is rejection, not a guess.

## Related

- Note 01 — [Proposal vs. Execution](../notes/01-proposal-vs-execution/)
- [Execution Gate](execution-gate.md) — where a validated proposal eventually runs
- [Authority Gate](authority-gate.md) — the next checkpoint
