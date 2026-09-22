# The Computational Claim Audit — published checklist

**Version 1.1, 21 September 2026.** John Goodman, OceanSparx Pty Ltd, Sydney.

Worked cases for every check are in `CASES.md`, which this
document indexes rather than duplicates.

A standard for deciding whether an in-silico result is load-bearing, applied before money is
committed to it. Every failure mode below is named, and every one is illustrated with a case
from this practice's own work — including results we published and then withdrew. Nothing here
is anonymised and nothing here is hypothetical.

The checklist is public on purpose. It is not the product. The product is a documented opinion
from someone who has run it against his own results and published what it found.

---

## How it is used

Fixed scope, fixed fee, fixed turnaround, **flat rate paid regardless of the finding**. A fee
contingent on the answer destroys the only thing being sold.

Input is the in-silico package as it sits in the data room: docking or free-energy results, an
ML model and its benchmark, a virtual screen, a claimed enrichment, with the code and the
identifiers that produced them.

Output is a verdict against each of the seven checks below, in order — **PASS**, **FAIL**,
or **CANNOT BE DETERMINED FROM THE PACKAGE** — with the specific failure named, or an explicit
*we tried to break this and could not*.

The third verdict is not a hedge. A package that does not carry the identifiers needed to check
target identity has failed to make its claim auditable, and that is a finding in itself.

**The order is the method.** Checks 1–6 cost minutes to hours. Check 7 costs the budget. Every
expensive failure in the record below was reachable by a cheap check that was skipped.

---

## 1. Identity — is the target the target?

**Failure mode.** The claim names a protein; the data were assembled against a different
identifier, a catch-all bucket, or the wrong chain of the right crystal. Nothing errors. The
numbers are well-formed. Every downstream metric is computed correctly on the wrong thing.

**Our own cases.**

- `CHEMBL612545` was carried through an entire benchmark arm labelled PD-L1. It is a ChEMBL
  `UNCHECKED` catch-all: **no organism, zero target components, 2.3M activities across
  unrelated assays.** Real PD-L1 is `CHEMBL3580522`. Of twelve sampled "actives", one had any
  real PD-L1 activity — the rest were HCN1 channel blockers and IL-6 release inhibitors. The
  arm was voided, and with it a Zenodo deposit that had been drafted but not minted.
- **PDB 3THW chain A is MSH2, not MSH3.** A binding hypothesis, a provisional patent and a
  published preprint rested on docking to it. Retracted; the correction was sent to the
  foundation that had committed bench time.
- `CHEMBL4005` is **PI3Kα**, not aldose reductase.
- A KPC-**3** inhibitor claim was docked against **3RXX, which is KPC-2**.

**How to check — minutes.** Open the target record and confirm organism, target type and
component count. Confirm the PDB chain's UniProt mapping rather than the entry's title. Confirm
the assay identifiers behind the actives resolve to the named protein. Sample a dozen actives
and check one by hand.

**Cost of missing it.** Total. Eighteen hours of docking on `CHEMBL612545`, plus every
conclusion drawn from it. There is no partial credit on this check.

---

## 2. Applicability — can the protocol reproduce a known answer on this system?

**Failure mode.** The method is valid in general and inapplicable here. Reported as a general
capability, so nobody tests it on the specific receptor, series or assay in front of them.

**Our own cases.**

- Redocking gate on PD-L1: **5J89 fails both pockets, at 4.13 Å and 4.02 Å RMSD; 5J8O passes at
  1.93 Å top pose, 1.36 Å best.** A screen run against the first is worthless, and looks identical
  to one run against the second.
- The gate is trustworthy only because it discriminates in both directions: **3PTB passes at
  0.43 Å across four seeds while 1STP fails at 6.0 Å.** The first version of that gate rejected
  trypsin/benzamidine two times in three — a gate where everything fails cannot be told apart
  from a gate that works.
- Days of decoy-control and redocking work went into **cathepsin S (3N4C)**, whose native ligand
  is **covalent to the catalytic Cys25**. Cathepsins are cysteine proteases and most of their
  inhibitors are covalent, so a non-covalent scoring function cannot represent the binding mode
  at all; the redocking gate correctly returns NOT_APPLICABLE rather than a pass or a fail. The
  same defect sat in the Mpro arm, where **7K40 was rejected because its co-crystal ligand is
  covalent to Cys145** and its box sat 6.3 Å off the ligand centroid. Checking the covalency of
  the native ligand takes seconds.

**How to check — hours.** Redock the co-crystal ligand into its own receptor, at the screening
protocol and not at a more generous one, across several seeds. Demand a known negative as well
as a known positive.

**Cost of missing it.** The entire screen, with no signal that anything went wrong.

---

## 3. Provenance — leakage, decoys, splits, and the confound that explains the result

**Failure mode.** The performance is real and it is not the performance claimed. A property the
study did not control for carries the signal.

**Our own cases.**

- **Docking against seven free descriptors**, on repaired receptors with paired-bootstrap
  intervals over the same compounds. Descriptors are molecular weight, clogP, donors, acceptors,
  rotatable bonds, TPSA and formal charge — microseconds of compute, no structure, no box.

  | target | Vina alone | descriptors | desc + Vina | **docking adds** | 95% CI |
  |---|---|---|---|---|---|
  | Mpro (751, 257 act) | 0.4530 | 0.7653 | 0.7630 | **−0.0023** | [−0.0044, −0.0002] **sig.** |
  | Factor Xa (854, 388 act) | 0.6775 | 0.7129 | 0.7279 | **+0.0150** | [−0.0033, +0.0339] **n.s.** |

  Factor Xa is the target where docking is genuinely above chance, and **it still adds nothing
  demonstrable** over descriptors that are free. On Mpro it significantly *subtracts*. A package
  reporting the 0.6775 without the 0.7129 beside it is reporting the wrong number.
- **We published the confounded version of that table ourselves.** An earlier revision gave the
  Factor Xa margin as **+0.011 with no confidence interval**, on the donor-defective receptor.
  With the receptor repaired and a paired bootstrap, it is +0.0150 **crossing zero**. A point
  estimate with no interval is not a result.
- **PD-L1, 320 compounds, 78% active:** the seven descriptors alone reach **0.9145**. The panel is
  very nearly separable from ligand properties, so nothing docked against it bears on binding at
  all. Confounded beyond use, and closed.
- **Collateral-sensitivity SCC in clinical surveillance data.** Reproduced through the
  manuscript's own table construction at crude OR **1.808** (imipenem→tetracycline) and **1.819**
  (meropenem→tetracycline), against the published 1.81 and 1.82 — so the contrast being tested is
  demonstrably the paper's. Stratified by MLST sequence type and pooled by Mantel–Haenszel, the
  association is **0.932** (95% CI 0.63–1.37) and **0.952** (0.65–1.40), CMH **p = 0.725** and
  **0.804** — not marginal, absent, with intervals excluding the unadjusted estimates. An
  independent hand-rolled pooling over a slightly different stratum set gives 0.910 and 0.952.
  ST307 and ST258 pull opposite ways (1.26 against 0.67), and excluding both drops the pooled OR
  to 0.466. The association is confounding by clone. The manuscript was withdrawn from peer
  review by us, after submission. Case 3 in `CASES.md`.
- **Quantum kernel QSAR:** fidelity kernels concentrate toward an identity Gram matrix, so the
  reported advantage disappeared against a **dimension-matched classical baseline — 0.850
  against 0.953.** The classical comparator has to match the dimension, or it is not a control.
- Decoy construction is itself suspect. Our own property-matched debias gate does **not** support
  per-target admissibility at the active-set sizes routinely used: 3 of 7 targets qualify at
  n ≈ 30 against 0 of 6 at full active count, and real targets are **not separable from random
  compound libraries (p = 0.711)**, with 5 of 7 inside the null range. Compute-driven active
  caps are near-universal in this literature and seldom reported.

**How to check — hours.** Run the single-descriptor baseline (molecular weight, cLogP, assay
count) before reading the model's number. Stratify by the compositional variable that would
produce the same signal — lineage, chemical series, assay, site, year — **before** running the
model system. Confirm the temporal split is against the model's actual cutoff, not the paper's
submission date.

**Cost of missing it.** A real number attached to the wrong cause. This is the failure that
survives to publication and to the term sheet, because everything about it is internally
consistent.

---

## 4. Power and magnitude — could the study have detected it, and could the mechanism produce it?

**Failure mode.** Two shapes, both of which read as a clean experiment. A null that the design
manufactured, or a control that could not have passed whatever the data said. And separately, an
agreement between a model and a measurement that the model could not have produced at any
plausible parameter value — where the number doing the work is one the study assumes rather than
constrains.

**Our own cases.**

- A claim that transcription-factor partitioning into mHTT condensates was "quantitatively
  consistent with" a −44.7% expression deficit measured in HD striatum. Sampling the model's own
  parameter space, 400,000 draws, showed the prediction is governed by the **mHTT nuclear volume
  fraction (ρ = +0.910)** and only weakly by the partition coefficients that were the paper's
  actual contribution (+0.359). Reaching −44.7% requires **16–31% of nuclear volume**; a nucleolus
  occupies well under 1%, and plausible values give **0.4–3.8%** — one to two orders of magnitude
  short. Withdrawn, and the preprint reissued reporting the limit.
- A pre-registered **EF@1% = 0.00** was reported for weeks as a null result about docking. With
  the active set capped at 30 on 793 compounds, the top 1% is **7.93 compounds, in which 0.30
  actives are expected by chance** — chance itself scores 0.00. The cap was ours. The
  informative endpoint was EF@5%, where the number was not zero.
- A positive control ran at **n = 4 pairs against a two-sided sign-flip test, whose minimum
  achievable p is 2/2ⁿ = 0.125.** Observed p was 0.12475 — the control had fired *maximally*,
  4 of 4 in the correct direction — and still could not reach 0.05. Unpassable by construction:
  the mirror of a control that cannot fail. **n ≥ 6 is the minimum for a sign-flip test to reach
  p < 0.05**; re-run at n = 8, where the minimum reachable p is 0.0078, it fires at **p = 0.0081**.
  The effect was real throughout and the instrument could not say so. Four defects of this family
  landed in a single experiment, and **every one was caught by a number failing to make sense
  rather than by review** — which is the uncomfortable part and the reason to check the arithmetic
  of a test before running it.

**How to check — minutes, with a calculator.** Compute the smallest p, or the largest
enrichment, the design can return at this n, before anything runs. If it cannot clear the
stated threshold, the study cannot pass its own test. Ask what result would look identical
whether the effect is present or absent. Then, for any claimed quantitative agreement, ask which
parameter the result is most sensitive to and whether that parameter was **measured or assumed** —
an agreement is evidence only if the model could have disagreed.

**Cost of missing it.** A null quoted as evidence of absence, or a control quoted as evidence
of validity, when neither could have come out any other way — or a mechanism presented as
explaining a measurement it is orders of magnitude too weak to produce.

Cases 4 and 5 in `CASES.md`.

---

## 5. Threshold integrity — is the yardstick independent of what it measures?

**Failure mode.** A decision threshold derived from the data it judges, or a noise floor
measured on a different axis from the one the intervention moves. Because the threshold governs
every downstream call, this is never a fixable paragraph — it is the spine.

**Our own case.** A manuscript comparing six protocol interventions was **desk-rejected without
external review** on exactly this. The venue is not named — the corrected manuscript is under
active resubmission, and which journal caught it adds nothing. Two "resolution limits" were used as the decision rule for
all six:

- **0.020** was the disagreement between two *implementations* of a scoring function — that is
  implementation variance, not the run-to-run reproducibility of an unchanged protocol.
- **0.039** came from comparing two *search-effort conditions* — an intervention effect being
  used as a noise floor.
- And the exhaustiveness comparison was **simultaneously one of the six interventions and the
  threshold the interventions were judged against.** The intervention was evaluated against
  itself.

The editor was right and no rebuttal was attempted. The repair is cheap and needed none of the
six interventions re-run — only the yardstick replaced, by replicating an **identical** protocol
across independent search seeds.

**Then the repair reproduced the same defect one level up, and our own gate caught it.** The new
floor was measured at exhaustiveness 4 while the manuscript's headline comparisons run at 32 —
a limit again not measured on the thing it polices. Rather than argue that less search is the
more stochastic case, it was tested: matched on the same 339 compounds and 6 seeds, exh=4 sd
0.00812 against exh=32 sd 0.00626, ratio 0.77, paired Pitman–Morgan **p = 0.624**. Conservative
in direction, **not established** — so the resolution was to use the exh=32 floor rather than to
justify the substitution. Two consequences that only measurement could produce: the intervention
originally reported at **+0.0185** re-measures paired-within-seed to **+0.00417, 95% CI
[−0.00598, +0.01433]**, spanning zero; and floors turn out to be panel-size specific, so a bound
measured on 755 compounds is about **1.5× too small** to charge against a claim computed on 339.
Case 6 in `CASES.md`.

**How to check — minutes.** For each threshold in the package, name the dataset it was derived
from. If that dataset is, or overlaps, the data being judged, the result is circular. Then ask
whether the floor was measured on the same axis the intervention moves.

**Cost of missing it.** Every conclusion in the package, conditional on a number that means
something else.

---

## 6. Artefact integrity — did what shipped match what passed the gate?

**Failure mode.** The gate ran, the gate passed, and a different file was delivered. Gating an
artefact and then bundling one are two separate operations until someone compares them.

**Our own case.** On 23 August 2026 `make_bundle.py` resolved the receptor **by naming convention
(`receptors/{target_id}_receptor.pdbqt`) rather than from the path in the configuration**, and
assembled a bundle around the 5J89 structure that had just **failed** its gate, carrying the
**passing** structure's search box — coordinates that in the other crystal's frame sit in empty
solvent. It would have returned a perfectly well-formed AUROC with no exception, no warning and
nothing in any log. **Caught by an md5 comparison — `b83fcd22` against `4ca3a9d9` — minutes
before 455 ligands went to compute.** Every other signal looked healthy.

**How to check — minutes.** md5 the artefact that ships against the artefact that passed. A path
built by naming convention will eventually disagree with the path in the config; prefer the
config field and let a missing one fail loudly. A stale or missing configuration must read as
UNKNOWN, never as a pass or as its last value.

**Cost of missing it.** A validated pipeline that did not validate the thing it delivered.

Case 7 in `CASES.md`.

---

## 7. Compute — last, never first

Nothing in checks 1–6 requires the study to be re-run, and every one of them is cheaper than the
run they protect.

| the cheap check | what it cost to skip it |
|---|---|
| Target identity (minutes) | 18 hours of docking against a bucket that was not a target |
| Covalency of the co-crystal ligand (seconds) | Days of decoy-control and redocking work on an unrepresentable mechanism |
| Lineage stratification (hours) | Would have been months of bench time; instead reached a withdrawal before anyone spent |

Re-running the computation is the most expensive way to find a defect and the least likely to
find this kind. It belongs at the end of the audit or outside it.

---

## Two rules that cut across every check

**A. A check that could not run looks exactly like a check that ran and found nothing — and
defaults to the permissive reading.** This is the single most common failure in the record, with
more than a dozen logged instances: a gate manifest excluded by `.gitignore`, so the gate
deployed with no data; a genome identifier parsed as a float, so the confounding test returned
zero strata; a database rejecting the default user agent, so 34 of 34 sequence fetches read as
"sequences unavailable"; an RMSD returning `None` on every ligand, so the gate passed everything;
a `LIMIT` with no `OFFSET`, so the rows being audited sat below the query window and the answer
came back "0 rows affected".

A verification's success state is *found nothing wrong*, which is also what a broken verification
produces. Positive results announce themselves; absent ones do not. So: **every gate needs a
positive control that must pass and a negative that must fail**, pinned to its own file so a run
cannot silently redefine it, and mutation-tested in **both** directions — break what it guards
and confirm it goes red, including the loosening direction.

**B. The first reading is usually more dramatic than the evidence, and the correction is usually
boring.** Three in one week here: "docking underperforms a property baseline" — withdrawn, the
panel had no true actives; "every receptor in the pipeline fails" — wrong, ligand auto-detection
was picking up modified residues; "decoy choice flips the sign of the conclusion" — the
controlled test gives +0.059 and most of the apparent gap was the receptor. Before accepting a
result that is more interesting than expected, find the variable that changed besides the one
being credited.

---

## What this checklist is not

- **Not a replication.** It asks whether the claim is supportable by the package as presented,
  not whether the finding is true.
- **Not a freedom-to-operate or legal opinion.** Patent and FTO questions go to an attorney.
- **Not a statement about the biology.** A package can pass all seven checks and the compound
  still fail in a cell.
- **Not deal-contingent, and never a rating.** The fee is flat, the finding is whatever it is,
  and findings on named listed companies go privately to the interested party rather than into
  publication.

---

## Change log

**v1.0 — 21 Sep 2026.** Seven checks. Two changes from the six-item working version of
23 Aug 2026, both forced by results that arrived after it:

1. **Check 5 (threshold integrity) is new**, added after a desk rejection identified a circular
   decision rule as the spine of a manuscript — a failure mode the six-item version had no slot
   for.
2. **Check 3's leading case has been replaced, and it cuts against our own earlier headline.**
   The working version led with "docking is below chance", on Mpro at AUROC 0.427. That is
   withdrawn twice over. Factor Xa is **above** chance, so the inversion does not generalise;
   and receptor repair moved every number in the comparison, so the panel the original claim was
   measured on no longer exists. What replaces it is stronger and less flattering to us:
   **docking adds nothing demonstrable over seven free descriptors on either clean target**, and
   we had ourselves published one of those margins as a point estimate with no interval.

**v1.1 — 21 Sep 2026.** Reconciled against the case studies, which already existed and which v1.0
had duplicated in a parallel directory. Three substantive changes, all from reading the primary
sources rather than a summary of them:

3. **Check 4 is now "power and magnitude"**, absorbing the sensitivity failure mode — an
   agreement the mechanism could not have produced at any plausible parameter value. It was
   present in the case studies and missing from the checklist, so the two did not correspond.
4. **Three figures corrected.** The redocking pockets fail at 4.13 and 4.02 Å, not "4.1"; the
   gate-failed receptor was **caught before compute**, not shipped; and the clonality result is
   0.932 (0.63–1.37) and 0.952 (0.65–1.40) at p = 0.725 and 0.804 — regenerated in place rather
   than quoted, after two documents disagreed and neither turned out to be wrong.
5. Every check now points at its worked case.

The checklist is maintained against new failures as they are found, ours first.

---

## Provenance of the cases

The claim this document makes is that the findings are checkable, so the status of each one is
stated rather than implied. Where a case is not yet public, that is a fact about our publication
backlog, not a qualification on the finding — but it is the reader's to weigh.

Every case cited above appears here, not a selection of them — a provenance table that covered
only the convenient ones would be the same defect this checklist sells against.

| case | check | status |
|---|---|---|
| Collateral-sensitivity SCC confounded by clonal lineage | 3 | **Public** — bioRxiv `10.64898/2026.08.07.743632` v3, retitled after we withdrew it from peer review; code at [`AegisMindApp/cs-lineage-confounding`](https://github.com/AegisMindApp/cs-lineage-confounding) |
| Circular resolution floors; exhaustiveness as its own threshold | 5 | **Public** — ChemRxiv `10.26434/chemrxiv.15008496/v2`; code and pre-registrations at [`AegisMindApp/screening-decomposition`](https://github.com/AegisMindApp/screening-decomposition) |
| KPC-3 claim screened against KPC-2 (3RXX) | 1 | **Public** — stated in the paper's own correction notice, Zenodo `10.5281/zenodo.20363635`, and on solver.press |
| Quantum kernel needs a dimension-matched baseline (0.850 → 0.953) | 3 | **Public record** — the refutation is published on solver.press as a settled discovery; the analysis write-up is not yet deposited |
| Docking's marginal value over seven descriptors | 3 | **Public** — [`docking_value/MARGINAL_VALUE.md`](https://github.com/AegisMindApp/screening-decomposition/blob/main/analysis/docking_value/MARGINAL_VALUE.md) |
| Factor Xa margin published as +0.011 with no interval | 3 | **Public** — [`docking_value/MARGINAL_VALUE.md`](https://github.com/AegisMindApp/screening-decomposition/blob/main/analysis/docking_value/MARGINAL_VALUE.md) records both the old point estimate and the corrected interval |
| PD-L1 panel separable from ligand properties at 0.9145 | 3 | **Public** — [`docking_audit/RESULT.md`](https://github.com/AegisMindApp/screening-decomposition/blob/main/analysis/docking_audit/RESULT.md), and `retrospective-benchmark/results/PDL1_RESULT.md` |
| 3THW chain A is MSH2, not MSH3 | 1 | **Public** — the correction notice on Zenodo `10.5281/zenodo.20363635`, and on solver.press |
| `CHEMBL612545` is not a target | 1 | **Public** — [`retrospective-benchmark`](https://github.com/AegisMindApp/retrospective-benchmark) (Amendment 21) and, as a caveat on the panel, `screening-decomposition` |
| `CHEMBL4005` is PI3Kα, not aldose reductase | 1 | **Public** — [`retrospective-benchmark`](https://github.com/AegisMindApp/retrospective-benchmark) README |
| Redocking gate: 5J89 / 5J8O, with 3PTB / 1STP as the two-way control | 2 | **Public** — [`screening-decomposition/analysis/docking_gates/`](https://github.com/AegisMindApp/screening-decomposition/tree/main/analysis/docking_gates) |
| Covalent native ligand: cathepsin S 3N4C, Mpro 7K40 | 2 | **Public** — [`screening-decomposition/analysis/docking_gates/`](https://github.com/AegisMindApp/screening-decomposition/tree/main/analysis/docking_gates) (`gate_manifest.json` carries the NOT_APPLICABLE verdicts) |
| Debias gate does not support per-target admissibility | 3 | **Public** — `github.com/AegisMindApp/retrospective-benchmark`, Amendments 22–25 |
| EF@1% null manufactured by our own active cap | 4 | **Public** — same repository, Amendment 19 and the active-cap note |
| Control unpassable at n = 4 by construction | 4 | **Public** — [`AegisMindApp/coniv-csro`](https://github.com/AegisMindApp/coniv-csro) |
| Gate-failed receptor caught by md5 before compute | 6 | **Public** — [`retrospective-benchmark`](https://github.com/AegisMindApp/retrospective-benchmark), Amendment 27.1 and the corrected `kaggle/make_bundle.py` |

**All sixteen are independently verifiable today.** Every case in this checklist resolves to a
public artefact — a DOI, a repository, or a file inside one. That was the precondition the
strategy named, and it is met.

One residual limitation, stated because it is not closed by anything above: the retrospective
benchmark's pre-registration **freeze date** is attested rather than independently verifiable, for
the reason its own `PROVENANCE.md` gives — the public repository is an extract and cannot carry the
original commit. A practice whose credential is reporting
against its own interest cannot rest that credential on private artefacts. This table exists so
a prospective client can see exactly which claims they would be taking on trust — and so that
the list of what to publish is a list, not a sentiment. Closing it is the first item of work,
ahead of adding any further checks.
