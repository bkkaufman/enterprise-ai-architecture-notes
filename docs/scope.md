# Scope

What these notes are, what they are not, and who they're for.

## What this repo is

Architecture notes on **controlling and accounting for AI agents that act inside
enterprise systems**. The focus is the control plane around the model — the seam
between *proposing* an action and *executing* it, and the authority, approval, and
audit machinery attached to that seam.

The organizing claim, repeated everywhere: **an agent may propose; the system
decides whether to execute.**

## In scope

- The proposal/execution separation and why it's the load bearing seam.
- Authority models and the delegation invariant (agent ⊆ principal).
- Human approval routing: when a person must sign off, and what makes an approval
  real rather than theater.
- Audit traces: append-only, tamper-evident, reconstructable decision records.
- Reusable gate/record patterns and synthetic, end-to-end examples.

## Out of scope

- **Model internals / prompting technique.** How to get a good proposal *out* of a
  model is a different craft. These notes assume you have a proposal and care what
  the system does with it.
- **Vendor/tool selection.** No product recommendations, no framework comparisons.
- **A reference implementation.** These are architecture notes, not a library. The
  JSON/YAML are illustrative schemas, not shipped code.
- **Real deployments.** Nothing here describes any specific company's systems,
  policies, or data. Everything is synthetic.
- **General MLOps / model training / eval pipelines.** Adjacent, not covered.

## Audience

- Engineers designing agent systems that touch systems of record.
- Architects and reviewers who have to sign off on "can we let the agent do this?"
- Risk / compliance readers who need the *authority* and *approval* models to be
  legible without reading code.

## Stance

Opinionated but not authoritative. The patterns reflect a particular bias — **fail
closed, record everything, keep the model out of the execution path** — that suits
regulated, audited environments. In a low-stakes internal tool, some of this is
overkill; the notes say so where relevant.

## How to read

Start with the [top-level README](../README.md), then the three notes in order —
each builds on the last. The [patterns](../patterns/) are referenced throughout
and can be read on demand.
