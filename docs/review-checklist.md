# Review Checklist

Run this before committing any new or edited note, pattern, template, or example.
It has two passes: a **safety** pass (is this safe to publish?) and a **quality**
pass (is this a good note?). Safety is non-negotiable; quality is the bar we aim
for.

## Pass 1 — Safety (must all pass)

Reference: [`publishing-boundary.md`](publishing-boundary.md).

- [ ] **No real identifiers** — no real company, person, customer, vendor,
      employee, or account names anywhere.
- [ ] **No real infrastructure** — no real hostnames, URLs, IPs, service/db/queue
      names. Hostnames use `example.invalid`.
- [ ] **No credentials or secrets** — not even expired or fake-looking ones.
- [ ] **Synthetic IDs are marked** — invented IDs carry a `-synthetic` suffix.
- [ ] **Thresholds are illustrative** — numbers are made up and labeled as such,
      not lifted from a real environment.
- [ ] **Shapes not values** — sensitive data is described by shape ("tax ID
      provided"), never by value.
- [ ] **Generalization test passed** — the lesson survives full synthesis; no
      point depends on real particulars.
- [ ] **Synthetic files carry the boundary header** pointing to
      `publishing-boundary.md`.

If any safety item fails, **do not commit.** Fix or remove.

## Pass 2 — Quality

- [ ] **Thesis is one quotable sentence** at the top of the note.
- [ ] **It builds on the prior notes** and links back to them with relative paths.
- [ ] **Diagrams live in `.mmd` files**, referenced not inlined; each `.mmd` has a
      comment header explaining what it shows.
- [ ] **Every example file is referenced** from prose — no orphan files.
- [ ] **Anti-patterns section exists** and names concrete traps, not platitudes.
- [ ] **Related-patterns links resolve** and point at the right files.
- [ ] **Consistent vocabulary** — proposal, execution, authority, approval, audit
      are used the way the other notes use them.
- [ ] **The through-line holds** — the content still ladders up to *"an agent may
      propose; the system decides whether to execute."*

## Pass 3 — Mechanical

- [ ] **JSON parses** — every `.json` is valid.
- [ ] **YAML parses** — every `.yaml` is valid.
- [ ] **Mermaid renders** — every `.mmd` renders without syntax errors.
- [ ] **No broken relative links** — all `../` paths resolve within the repo.
- [ ] **Spelling / formatting** — headings, tables, and code fences render
      cleanly in GitHub-flavored markdown.

## Sign-off

A change is ready to commit only when **all of Pass 1** and **all of Pass 3**
pass, and Pass 2 is satisfied or the gaps are consciously accepted.
