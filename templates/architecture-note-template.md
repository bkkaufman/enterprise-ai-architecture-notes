# NN — <Note Title>

> <One-sentence thesis. The single claim this note defends. Make it quotable.>

<Two or three sentences of orientation: what earlier notes established, and where
this one picks up. Link back with relative paths.>

## The problem

<Describe the failure mode this note addresses. Prefer a concrete, synthetic
"naive approach" and show why it breaks. What goes unattributable, unauthorized,
or irreversible if you don't do this?>

## The <core idea>

<State the pattern or separation. A small diagram helps — reference a companion
`.mmd` file in this directory rather than inlining a huge ASCII block.>

See [`<diagram>.mmd`](<diagram>.mmd).

## What <the key object> is

<Define the central data object or mechanism. Enumerate its fields/properties in
a list or table. Point at the synthetic example file.>

See [`<example>.json`](<example>.json).

## Why this is the right approach

<Explain why the seam lands where it does — how it makes downstream controls
(authority, approval, audit) expressible as clean functions over the key object.>

## Anti-patterns

- **<Anti-pattern name>.** <Why it's tempting and why it defeats the point.>
- **<Anti-pattern name>.** <...>
- **<Anti-pattern name>.** <...>

## Failure modes this prevents

<Optional. If the note pairs with a `failure-scenarios.md`, summarize the theme
and link it. Otherwise a short bulleted list of what breaks without this.>

## Related patterns

- [<Pattern>](../../patterns/<pattern>.md)
- [<Pattern>](../../patterns/<pattern>.md)

---
<!--
Authoring checklist (delete before publishing):
- [ ] Thesis is one quotable sentence.
- [ ] Every example file is synthetic — no real names, systems, or credentials.
- [ ] Diagrams live in .mmd files, referenced not inlined.
- [ ] Cross-links use relative paths and resolve.
- [ ] Ran through docs/review-checklist.md and docs/publishing-boundary.md.
-->
