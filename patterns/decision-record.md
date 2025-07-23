# Pattern: Decision Record

**Intent.** Produce one durable, append-only, tamper-evident record per proposal
that reaches the control plane — executed, rejected, or deferred — so that any
decision can be reconstructed long after the run ends.

## Context

Every other gate in this repo *emits* decision records: the
[Proposal Gate](proposal-gate.md) on rejection, the
[Authority Gate](authority-gate.md) on its verdict, the
[Human Approval Gate](human-approval-gate.md) on approve/deny, the
[Execution Gate](execution-gate.md) on intent and outcome. This pattern defines
the record they all write.

## Problem

Logs are for debugging and are routinely dropped, sampled, or edited. An
enterprise needs a record that survives incidents and audits, that no one can
quietly rewrite, and that answers "why did this happen?" from the record alone.

## Solution

A record type with a fixed shape and hard guarantees. Minimum fields:

| Field | Purpose |
|-------|---------|
| `record_id`, `sequence` | identity and ordering |
| `prev_record_hash`, `self_hash` | hash chain → tamper-evidence |
| `created_at` | when the decision was recorded |
| `decision` | `executed` / `rejected` / `deferred` / `approved` / `denied` |
| `phase` | `intent` or `outcome` (for execution bracketing) |
| `proposal` | the originating proposal, embedded verbatim |
| `decided_by` | control plane component **or** authenticated human identity |
| `authority_basis` | grant relied on, principal, delegation check, approval route |
| `outcome` | concrete system-of-record effect (for executed records) |
| `links` | `run_id`, `trace_id`, superseded/prior records |
| `redaction` | which values were masked (values only — never the record) |

See the synthetic
[`decision-record.json`](../notes/03-agent-audit-trace/decision-record.json) for a
complete instance.

## Invariants

- **Append-only.** Never edited or deleted. A correction is a *new* record
  referencing the old one.
- **Tamper-evident.** Hash-chained so after-the-fact edits are detectable.
- **Self-contained.** Embeds or hard-references the proposal, decision, decider,
  and outcome — readable without three other systems online.
- **Attributable.** Human decisions carry a real authenticated identity; agent
  decisions carry the agent id and the principal.
- **Rejections count.** "We chose not to act" produces a record too.
- **Redaction ≠ deletion.** Sensitive *values* may be masked; the record that
  something happened, and its shape, is always retained.
- **Retained to policy.** Lifetime set by compliance, not disk pressure.

## Related

- Note 03 — [Agent Audit Trace](../notes/03-agent-audit-trace/)
- [Failure scenarios](../notes/03-agent-audit-trace/failure-scenarios.md) — what breaks when a field is missing
