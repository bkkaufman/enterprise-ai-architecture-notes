# Publishing Boundary

This repo is public. These notes generalize hard-won lessons about running AI
agents in enterprise systems **without** disclosing anything about any specific
real environment. This document is the boundary that keeps that true. Every file
must stay on the safe side of it.

## The core rule

> Publish **patterns and principles**. Never publish **particulars** of a real
> system, organization, person, or dataset.

If a reader could use something in this repo to learn a fact about a real
company's internals, a real person, or real data, it does not belong here.

## Never publish

- **Real identifiers.** Company names, employee or customer names, usernames,
  email addresses, vendor names, account numbers, tax IDs, invoice numbers.
- **Real infrastructure.** Hostnames, internal URLs, IP addresses, service names,
  database names, queue names, bucket names, environment names.
- **Credentials or secrets.** API keys, tokens, passwords, private keys,
  connection strings — even expired or "fake-looking" ones.
- **Real policy specifics.** Actual approval thresholds, credit limits, SLA
  numbers, headcounts, or org-chart structure lifted from a real environment.
- **Real incident details.** Dates, ticket numbers, or specifics that could
  identify an actual event or the people involved.
- **Proprietary logic.** Business rules, pricing formulas, or model prompts that
  are a specific organization's IP.

## Always do instead

- **Invent everything.** Synthetic names ("Northwind Synthetic Supplies"),
  invented roles, made-up categories.
- **Suffix synthetic IDs** with `-synthetic` so they can never be mistaken for
  real values (`vnd_00184-synthetic`, `run_4d21b8c0-synthetic`).
- **Use reserved placeholders.** `example.invalid` for hostnames/URLs,
  documentation IP ranges, obviously-fake tax IDs.
- **Round and relabel thresholds.** Use illustrative numbers ($50k, 0.75) and say
  they're illustrative. Never lift real thresholds.
- **Describe shapes, not values.** "A banking verification letter is present," not
  the letter. "Tax ID provided," not the tax ID.

## The generalization test

Before adding or editing a file, ask:

1. **Could this fact identify a real org, person, system, or event?** If yes, cut
   or synthesize it.
2. **Does the point survive full synthesis?** If the lesson only lands with real
   specifics, the lesson isn't general enough to publish — rework it until it is.
3. **Would I be comfortable with the subject reading this?** Any real subject.
4. **Is every ID, name, host, and number obviously fake?** If not, make it so.

If any answer is uncomfortable, the content stays out until it's fixed.

## Enforcement

- Every synthetic example carries a header pointing back to this document.
- The [review checklist](review-checklist.md) includes a synthetic-data pass that
  must be run before anything is committed.
- When in doubt, leave it out. The value of these notes is the patterns; no
  particular is worth the exposure.
