# Failure Scenarios

Each scenario below is **synthetic** and illustrative. The pattern is always the
same: the model did something ordinary, but a gap in the audit trace turned a
recoverable situation into an unaccountable one. For each, we name the missing
field and the fix.

---

## S1 — The action no one can explain

**What happened.** A vendor record exists with non-standard NET60 terms. Finance
asks why. The system has a log line: `vendor updated: terms=NET60`. That's it.

**The gap.** The *proposal* and its *rationale* weren't retained — only the
effect. There's no record of what the agent was trying to accomplish or what
inputs it saw.

**Consequence.** No one can tell whether this was a legitimate negotiated term, a
hallucinated value, or a prompt injection in the intake document. The record
can't be defended or refuted.

**Fix.** The decision record embeds the proposal verbatim (action, params,
rationale, confidence). "What was proposed?" must be answerable from the record
alone.

---

## S2 — The silent gap at the crash

**What happened.** The execution service crashed mid-write. A payment instrument
was updated in the ERP, but no outcome record was written. The trace shows a
proposal, then nothing.

**The gap.** No **write-before-effect** record. The intent to execute was never
persisted before the side effect ran, so the crash left the trace saying "we
were about to do nothing" while the system of record changed.

**Consequence.** During recovery, no one knows whether the write completed. The
gap looks identical to "the agent never tried," which is the most dangerous
ambiguity in an incident.

**Fix.** Write the `phase: "intent"` record *before* the side effect and the
`phase: "outcome"` record *after*. A crash then leaves an intent record with no
matching outcome — a loud, detectable signal, not silence.

---

## S3 — "An operator approved it"

**What happened.** A high-value proposal was approved. The record says
`approved_by: "operator"`.

**The gap.** No **authenticated identity** on the human decision. "Operator" is a
role label, not a person.

**Consequence.** In a four-eyes context, you cannot prove the approver was
distinct from the initiator. In an incident, you cannot ask the actual human what
they saw. Accountability evaporates at the one point where a human was in the
loop specifically to provide it.

**Fix.** Human decisions carry a real, authenticated identity, plus what the
approver was shown (the proposal + rationale + classification) and any comment.

---

## S4 — The rewritten trail

**What happened.** After an incident, a well-meaning engineer "cleaned up" a
misleading record by editing it in place to reflect what *should* have happened.

**The gap.** Records were **mutable**. There is no tamper-evidence, so the edit is
invisible.

**Consequence.** The trace can no longer be trusted for *any* record, because any
of them could have been edited. A single in-place edit poisons the evidentiary
value of the whole trail.

**Fix.** Append-only, hash-chained records. Corrections are *new* records that
reference the original. The original stays, wrong but preserved; the chain makes
tampering detectable.

---

## S5 — The retry that slipped through

**What happened.** A proposal was denied by a human approver. Moments later, a
nearly identical proposal executed via auto-approve.

**The gap.** The denial wasn't **linked forward** as a constraint, and the trace
didn't correlate the two proposals under one run. Each looked independent.

**Consequence.** The second proposal effectively laundered the first past the
human gate. The trace, read record-by-record, shows two normal decisions and
hides the fact that one overrode the other.

**Fix.** Correlate proposals by `run_id`; a denial feeds back as an explicit
constraint and the re-proposal *links* the denial it supersedes. The graph shows
the override plainly (see [`audit-flow.mmd`](audit-flow.mmd)).

---

## S6 — Redaction as erasure

**What happened.** To satisfy a data-handling request, sensitive fields were
deleted from historical records — and the surrounding records went with them.

**The gap.** **Redaction was implemented as deletion.** Removing the value also
removed the evidence that a decision occurred.

**Consequence.** A regulator asks "did this action happen?" and the honest answer
is "we can no longer tell." Compliance with one obligation destroyed the ability
to meet another.

**Fix.** Redact *values*, retain *records*. The fact that an action happened, its
shape, its actors, and its timing survive; only the sensitive payload is masked.
Redaction is recorded as its own event.

---

## The common thread

None of these required an exotic model failure. Each was a routine decision made
unaccountable by a single missing property of the trace: retained rationale,
write-before-effect, authenticated identity, immutability, correlation, or
redaction-not-erasure. The audit trace earns its keep precisely at these
unglamorous points.
