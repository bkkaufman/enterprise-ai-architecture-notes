# Synthetic Example: Vendor Onboarding

> **All data in this example is synthetic.** No real vendor, customer, employee,
> credential, or internal system is described. Identifiers carry a `-synthetic`
> suffix and hosts use `example.invalid`.

This is the end-to-end scenario the three notes draw their fragments from. It ties
[Proposal vs. Execution](../../notes/01-proposal-vs-execution/),
[Authority and Approval](../../notes/02-authority-and-approval/), and the
[Agent Audit Trace](../../notes/03-agent-audit-trace/) into one story.

## Scenario

A mid-size company lets a `vendor-onboarding-assistant` agent help process
incoming vendor requests. The agent acts on behalf of a purchasing manager. It
can *propose* vendor records; it can never *create* one directly. A deterministic
control plane decides whether each proposal executes, and records every decision.

## Actors

| Actor | Kind | Acts for | Notes |
|-------|------|----------|-------|
| `vendor-onboarding-assistant` | agent | `role:purchasing-manager` | grants are a strict subset of the principal's |
| `role:purchasing-manager` | human role | — | can onboard vendors at standard terms |
| `role:finance-controller` | human role | — | approves elevated credit / non-standard terms |

## Walkthrough

### Path A — the routine case (auto-approve)

1. **Trigger.** An intake bundle passes completeness checks.
2. **Proposal.** The agent proposes `create_vendor_record` at NET30, $25k limit —
   within the standard band.
   → [`.../01/example-proposal.json`](../../notes/01-proposal-vs-execution/example-proposal.json)
3. **Authority.** The agent holds `vendor.create:standard-band`; the request is
   within the principal's authority (delegation invariant holds).
4. **Approval routing.** Reversible, confidence ≥ 0.75, limit ≤ $50k, non-regulated
   → **auto-approve** (decision-table row 6).
5. **Execution.** The execution gate writes an intent record, inserts the vendor,
   writes the outcome record.
   → [`.../01/example-execution-record.json`](../../notes/01-proposal-vs-execution/example-execution-record.json)

### Path B — the escalated case (denied, then re-proposed)

1. **Proposal.** An earlier proposal requested a **$75k** limit — above the
   standard band.
2. **Approval routing.** Over the $50k threshold → **human approval required**
   (decision-table row 3).
3. **Denial.** The finance controller denies it with the comment *"reduce limit to
   standard band."* The denial is recorded with the approver's real identity.
4. **Constraint feedback.** The denial returns to the agent as a constraint — not a
   retry-the-same-thing error.
5. **Re-proposal.** The agent proposes again at **$25k**, *linking the denial it
   supersedes*. This now auto-approves and executes.
   → [`.../03/decision-record.json`](../../notes/03-agent-audit-trace/decision-record.json)
6. **Audit.** An investigator later asks "why does `vnd_00184-synthetic` exist?"
   and walks the chain backward: executed record → superseded denial → original
   deferral. Nothing is hidden.
   → [`.../03/audit-flow.mmd`](../../notes/03-agent-audit-trace/audit-flow.mmd)

## Relevant policy

The authority grants, bands, and approval routing used above are the synthetic
[`authority-policy.yaml`](../../notes/02-authority-and-approval/authority-policy.yaml)
and [`decision-table.md`](../../notes/02-authority-and-approval/decision-table.md).

## What this example shows

- An agent that **proposes but never executes** — every side effect goes through
  the control plane.
- The **delegation invariant** capping the agent below its principal.
- A **denial fed back as a constraint**, with the re-proposal linking what it
  supersedes — so a retry can't launder a rejected action past the human gate.
- An **audit trace you can walk backward** from a system-of-record entity to the
  original intent.

---

[← 03 — Agent Audit Trace](../../notes/03-agent-audit-trace/) · [Home](../../README.md)
