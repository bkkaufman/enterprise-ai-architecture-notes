# Enterprise AI Architecture Notes

This repository consolidates architecture work developed across 2025 and 2026
into public reference notes and synthetic examples.

Working notes on architecture patterns for AI agents that operate inside
enterprise systems — where the interesting problem is not *what the model can
do*, but *what the system is allowed to let it do*.

The through-line across every note here is a single separation:

> **An agent may propose. The system decides whether to execute.**

Everything else — authority models, approval gates, audit traces — is machinery
built to enforce and observe that boundary.

## Why this repo exists

Most "AI agent" writing is about capability: better tools, better reasoning,
longer autonomy. In an enterprise, capability is table stakes. The hard part is
containment and accountability:

- A model can generate a plausible action. That does not mean the action is
  *authorized*, *reversible*, or *attributable*.
- Enterprises answer to auditors, regulators, and their own change-management
  processes. "The AI did it" is not an acceptable entry in an incident review.
- The safe default is that an agent produces a **proposal** — a structured,
  inspectable intent — and a separate, non-model control plane evaluates that
  proposal against policy before anything touches a system of record.

These notes describe patterns for building that control plane.

## Structure

| Path | What's in it |
|------|--------------|
| [`notes/`](notes/) | Longer-form architecture notes, each self-contained with diagrams and synthetic examples |
| [`patterns/`](patterns/) | Reusable gate/record patterns referenced across the notes |
| [`templates/`](templates/) | Blank templates for writing new notes and examples |
| [`docs/`](docs/) | Meta: scope, what is safe to publish, review checklist |
| [`examples/`](examples/) | End-to-end synthetic scenarios |

### Notes

1. **[Proposal vs. Execution](notes/01-proposal-vs-execution/)** — the core
   separation. An agent emits a proposal; a distinct execution stage decides
   whether and how to carry it out, and records what happened.
2. **[Authority and Approval](notes/02-authority-and-approval/)** — how the
   system decides *whether a given actor is allowed* to execute a given
   proposal, and when a human must approve.
3. **[Agent Audit Trace](notes/03-agent-audit-trace/)** — how to make every
   decision reconstructable after the fact, and what the trace must capture to
   survive an incident review.

## Patterns

The notes lean on five recurring building blocks:

- [Proposal Gate](patterns/proposal-gate.md) — normalize and validate agent output into a proposal
- [Authority Gate](patterns/authority-gate.md) — check the actor's authority against policy
- [Human Approval Gate](patterns/human-approval-gate.md) — route to a person when required
- [Execution Gate](patterns/execution-gate.md) — the only place side effects happen
- [Decision Record](patterns/decision-record.md) — the durable, append-only account of what was decided

## A note on the examples

Every JSON, YAML, and scenario in this repo is **synthetic**. No real vendor,
customer, employee, credential, or internal system is described. See
[`docs/publishing-boundary.md`](docs/publishing-boundary.md) for the rules these
notes follow so they stay safe to share publicly.

## Status

Living notes. Opinionated, not authoritative. Corrections and counterexamples
welcome.
