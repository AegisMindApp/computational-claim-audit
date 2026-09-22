# The Computational Claim Audit

A published standard for deciding whether an in-silico result is load-bearing, applied before
money is committed to it — and eleven worked cases showing each failure mode, every one of them
our own.

- **[`CHECKLIST.md`](CHECKLIST.md)** — seven checks in cost order: identity, applicability,
  provenance, power and magnitude, threshold integrity, artefact integrity, compute. Compute is
  last, always.
- **[`CASES.md`](CASES.md)** — the eleven cases, with the numbers and what each one cost.

## Why the checklist is public

It is not the product. The product is a documented opinion from someone who has run it against
his own results and published what it found — including the results that did not survive.

Seven of the eleven cases end in a withdrawal, a retraction or a desk rejection. That is a bad
record for a discovery company and the right one for an audit practice: nobody needs a hypothesis
generator whose hypotheses don't survive, and quite a few people need someone who can tell them,
cheaply and fast, that a computational claim won't survive — before they wire the money.

## Every case resolves to a public artefact

This is the part that matters, and it is the reason this repository exists rather than a slide.
Each case can be checked without asking us for anything.

| evidence | where |
|---|---|
| Blind retrospective benchmark, 29 amendments, including the ones that refuted our own explanations | [AegisMindApp/retrospective-benchmark](https://github.com/AegisMindApp/retrospective-benchmark) |
| Virtual-screening decomposition: docking's marginal value, the redocking gate, the receptor-repair work | [AegisMindApp/screening-decomposition](https://github.com/AegisMindApp/screening-decomposition) |
| Collateral-sensitivity association dissolved by lineage stratification | [AegisMindApp/cs-lineage-confounding](https://github.com/AegisMindApp/cs-lineage-confounding) · bioRxiv [10.64898/2026.08.07.743632](https://doi.org/10.64898/2026.08.07.743632) |
| Chemical short-range order on Co-Ni-V, both runs including the one that failed | [AegisMindApp/coniv-csro](https://github.com/AegisMindApp/coniv-csro) |
| The circular-threshold manuscript and its corrected pre-registrations | ChemRxiv [10.26434/chemrxiv.15008496/v2](https://doi.org/10.26434/chemrxiv.15008496/v2) |

One limitation is stated rather than left to be discovered: the retrospective benchmark's
pre-registration **freeze date** is attested, not independently verifiable, because the public
repository is an extract that cannot carry the original commit. Its `PROVENANCE.md` says so.

## The single most useful question in the checklist

If you read none of it, this one costs nothing and is almost never answered:

> **What does the seven-descriptor baseline score on the same compounds?**

Molecular weight, clogP, donors, acceptors, rotatable bonds, TPSA, formal charge. Microseconds of
compute, no structure, no docking box. On our own panels, docking adds **−0.0023**
[−0.0044, −0.0002] over it on one target and **+0.0150** [−0.0033, +0.0339] on another. Nothing
demonstrable on either; significantly negative on one.

A lead-selection rationale that reduces to docking rank has not shown what it appears to show,
and the baseline that proves it is free.

## Scope

Not a replication — it asks whether the claim is supportable by the package as presented, not
whether the finding is true. Not a freedom-to-operate or legal opinion. Not a statement about the
biology: a package can pass all seven checks and the compound still fail in a cell. Never
deal-contingent and never a rating, and findings on named parties go to the party concerned
rather than into publication.

---

John Goodman — OceanSparx Pty Ltd, Sydney. Maintained against new failures as they are found,
ours first.
