# Synthetic Example: <Scenario Name>

> **All data in this example is synthetic.** No real vendor, customer, employee,
> credential, internal system, endpoint, or policy is described. See
> [`docs/publishing-boundary.md`](../docs/publishing-boundary.md).

## Scenario

<2–4 sentences. What business situation does this illustrate? Keep it generic —
"a mid-size company onboarding an office-supplies vendor," never a real company.>

## Actors

| Actor | Kind | Acts for | Notes |
|-------|------|----------|-------|
| `<agent-id>` | agent | `role:<principal>` | narrower grants than principal |
| `role:<principal>` | human role | — | |

## Walkthrough

1. **Trigger.** <What kicks this off.>
2. **Proposal.** <What the agent proposes, and why.> → see `<proposal>.json`
3. **Authority.** <Grant relied on; delegation check.>
4. **Approval routing.** <Auto-approve or human? Which decision-table row?>
5. **Execution.** <What effect lands on which synthetic system of record.>
6. **Record.** <What the decision record captures.> → see `<record>.json`

## Companion files

- [`<proposal>.json`](<proposal>.json) — the proposal
- [`<record>.json`](<record>.json) — the decision/execution record
- [`<policy>.yaml`](<policy>.yaml) — relevant authority policy (if any)

## What this example is meant to show

<The one or two teaching points. E.g. "how a denial feeds back as a constraint
and the re-proposal links the denial it supersedes.">

---
<!--
Synthetic-data rules (delete before publishing):
- [ ] Names invented; use "-synthetic" suffixes on IDs.
- [ ] No real hostnames, URLs (use example.invalid), IPs, account numbers, tax IDs.
- [ ] Thresholds/policies illustrative, not lifted from a real environment.
- [ ] Ran through docs/publishing-boundary.md.
-->
