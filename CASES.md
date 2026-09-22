# Eleven ways a computational claim fails, and what each one cost to catch

Every case below is our own work. None is anonymised, none is a hypothetical, and in every one the
wrong number was already on screen and looked fine. That is the point: none of these failures
announced itself. Each was found by a check that costs minutes, and each would have survived into
a publication, a data room or a wet lab if the check had not been run.

They map onto the audit checklist in order: **identity → applicability → provenance → power and
magnitude → threshold integrity → artefact integrity → compute.** Cases 4 and 5 are two failure
modes of the same check — one where the mechanism could not produce the magnitude claimed, one
where the study could not have detected the effect it looked for. Compute is last, always.

Cases 1–7 are told at length; 8–11 are shorter because the checks that caught them are cheaper.
**Seven of the eleven ended in a withdrawal or a rejection. Four did not** — they were caught
before anything was spent, which is what the checklist is for and what the long cases, taken
alone, would misrepresent.

*Updated 21 September 2026. Two cases carry verdicts that changed after this document was first
written, and both changed against our earlier position — see the prior immediately below, and
case 5.*

---

## The prior: what docking is actually worth

Before the individual failures, the finding that motivates auditing this class of claim at all.

**This section has been rewritten once, and the correction went against us.** The original read
"docking is below chance", on a SARS-CoV-2 Mpro panel docked into a receptor that was missing its
polar hydrogens. Repairing the receptor moved every number, and a second and third target then
disagreed with the first. The honest claim is narrower than the one we made, and more damaging to
the method.

**Three targets, repaired receptors, paired-bootstrap intervals over the same compounds.** The
descriptors are molecular weight, clogP, donors, acceptors, rotatable bonds, TPSA and formal
charge: microseconds of compute, no structure, no box, no receptor preparation.

| target | Vina | seven descriptors | desc + Vina | **docking adds** | 95% CI |
|---|---|---|---|---|---|
| Mpro (751, 257 act) | 0.4530 | 0.7653 | 0.7630 | **−0.0023** | [−0.0044, −0.0002] **sig.** |
| Factor Xa (854, 388 act) | 0.6775 | 0.7129 | 0.7279 | **+0.0150** | [−0.0033, +0.0339] **n.s.** |
| PD-L1 (320, 78% act) | 0.5356 | **0.9145** | — | +0.0296 | below the measurement floor |

**Docking is below chance on one target and above it on another, and on neither does it add
anything demonstrable over descriptors that are free.** On Mpro it significantly *subtracts*. The
Mpro inversion does not generalise, so "docking doesn't work" is withdrawn; what replaces it is
that docking is not carrying information the cheapest possible baseline lacks.

PD-L1 is in the table as a warning rather than a result: seven free descriptors reach 0.9145 on
it, so the panel is very nearly separable from ligand properties alone and nothing docked against
it bears on binding. A panel can be too easy to measure anything.

**What does clear the bar.** Boltz-2's binary affinity probability — sequence and SMILES, no
receptor preparation and no docking box — added to the descriptors gives **+0.0934
[+0.0616, +0.1253]** on Mpro and **+0.0751 [+0.051, +0.099]** on Factor Xa, against a
pre-registered +0.04 margin. Boltz-2 *alone* does not clear it (+0.0259 and +0.0186, both
intervals spanning zero). So the structure-based information that helps is not coming from
docking, and the combination result replicated across a viral protease and a coagulation factor
on independently assembled panels.

**What this means in a data room:** a docking score is a screening heuristic, not evidence of
binding. A company whose lead-selection rationale reduces to docking rank has not shown what it
thinks it has shown. Ask two things — what the redocking control returned, and what the
seven-descriptor baseline scores on the same compounds. The second question costs nothing and is
almost never answered.

---

## 1. Identity — the target that was not a target

**Claimed:** a retrospective benchmark on PD-L1, with the platform's model beating Vina
(AUROC 0.677 vs 0.533) — the one positive result in the study.

**The check:** query the target identifier against the ChEMBL API. Under a minute.

**Found:** `CHEMBL612545` has `pref_name: "Unchecked"`, `target_type: UNCHECKED`, no organism, and
**zero target components**. It is a catch-all bucket of roughly 2.3 million bioactivity records
with no validated target assignment. Sampled "actives" turned out to be HCN1 channel blockers and
IL-6 release inhibitors. Real human PD-L1 is `CHEMBL3580522`.

**Consequence:** there were no true actives to enrich, so the comparison said nothing about either
predictor's ability to rank binders. The claim was withdrawn.

The residue is more interesting than the retraction. Read as an unintended negative control, Vina's
0.533 is **correct behaviour** on a set with no signal — while the learned model found 0.677 of
apparent enrichment where no enrichment exists. That is evidence it reads property and scaffold
structure rather than binding.

**In a data room:** the identifier in the methods section is not the same as the protein in the
title. Check it. Eighteen hours of compute rested on this one, and the study's own limitations
section had already documented the problem — it went unread.

---

## 2. Applicability — the protocol that cannot reproduce a known answer

**Claimed:** hypotheses from a cathepsin S virtual screen (3N4C), and separately a PD-L1 screen
against 5J89.

**The check:** redock the co-crystallised ligand and ask whether the protocol puts it back where
the crystal says it is. Minutes per receptor.

**Found, CTSS/3N4C:** Vina placed EF3 — the structure's *own* crystallised inhibitor — 7.2 Å from
its crystal position, and scored that wrong pose better than 61 of 72 decoys. The box was correct
to 0.00 Å. The ligand was correct. Target identity passed, box sanity passed, counter-screen
passed. The assay was broken at a level none of those look at.

The subtlety that matters commercially: at exhaustiveness 128 the near-native poses **are**
generated, at 2.22 Å and 2.34 Å — ranked 6th and 9th of 9. Sampling finds the binding mode; the
scoring function rejects it. A naive "is a good pose in there somewhere?" check would have passed
this target. A prospective screen only ever consumes rank 1.

**Found, PD-L1:** both pockets of 5J89 fail (top pose 4.13 Å and 4.02 Å, best 2.49 Å and 2.12 Å —
sampling never visits the binding mode). 5J8O passes at 1.93 Å top, 1.36 Å best. Same target, same
protocol, different crystal — one is usable and one is not.

**The gate needs a positive control or it proves nothing.** Ours is calibrated on trypsin/
benzamidine (3PTB), which passes at 0.41–0.45 Å across four seeds, and 1STP, which fails at 6 Å
with the near-native pose ranked 6th. An earlier version of the gate demanded the near-native pose
*be* rank 1 — that rule failed the textbook case two times in three, because rank was measuring
which of two indistinguishable correct poses won an RNG tie. A gate that rejects benzamidine
rejects everything.

**In a data room:** ask for the redocking control at the screening protocol's own exhaustiveness,
not a friendlier one. If it was never run, the screen is unvalidated.

---

## 3. Provenance and confounding — the signal that was population structure

**Claimed:** a collateral-sensitivity trade-off in *Klebsiella pneumoniae* — imipenem, meropenem
and tetracycline — mined from 104,337 routine susceptibility records.

**It satisfied every conventional safeguard:** q < 0.002, OR 1.81–1.82 on more than 850 co-tested
isolates per edge, permutation P = 0.001, drug-class specificity, stability across year bands.

**The check:** stratify by MLST sequence type and pool by Cochran–Mantel–Haenszel. Hours.

**Found:** within lineage the association is **absent** — OR 0.932 (95% CI 0.63–1.37) and 0.952
(0.65–1.40), CMH P = 0.725 and 0.804, confidence intervals excluding the unadjusted estimates. The
two dominant lineages point in opposite directions (ST307 1.26, ST258 0.67), and excluding both
drops the pooled estimate to 0.466. The signal is clonal structure, not a trade-off.

**The step that makes the rest of it admissible, and it comes first:** the contingency tables were
rebuilt through the manuscript's *own* construction and returned crude ORs of **1.808** and
**1.819** against the published 1.81 and 1.82. Without that, "the association vanished" is
unfalsifiable — a broken join produces the same empty answer as a real negative, and defaults to
the reading you were hoping for. Only once the crude number reproduces does the stratified number
mean anything.

Re-pooled independently with `statsmodels.StratifiedTable` rather than our own Mantel–Haenszel
code, and stable at minimum stratum sizes of 5, 10 and 20 with no Breslow–Day heterogeneity. A
hand-rolled pooling over a slightly different stratum set gives 0.910 and 0.952 — the two
implementations agree to within the rounding, which is the point of running both.

**The control that makes this credible rather than merely deflationary:** stratification attenuates
odds ratios whether or not the strata mean anything, so sequence-type labels were permuted 300
times preserving stratum sizes. Size-matched random partitions left the OR at **1.76**. Only the
true partition collapsed it. Without that control, "the association vanished under stratification"
would have been unfalsifiable.

**In a data room:** large-N observational biology is cheap and confounded. Conventional
safeguards — multiple-testing correction, permutation, replication across time — protect against
chance, not against structure. Ask what the population stratification was, and ask what the
negative control for the stratification itself was.

---

## 4. Sensitivity — the claim that required a third of the nucleus

**Claimed, in our own preprint and to a foundation:** transcription-factor partition coefficients
into mHTT condensates "quantitatively consistent with" the −44.7% reduction in target gene
expression measured in HD striatum.

**The check:** sample the model's own parameter space and ask what fraction of it reproduces the
agreement. 400,000 samples, minutes.

**Found:** only 6.5% of the plausible space lands within ±10 points of −44.7%, so the claim was at
least falsifiable. But the parameter doing the work was not the one the paper reported. Predicted
depletion correlates with the **mHTT nuclear volume fraction at ρ = +0.910**, and with the
partition coefficients — the paper's actual quantitative contribution — at only +0.359.

Solving for the volume fraction needed to reach −44.7% at the paper's own coefficients gives
**16–31% of nuclear volume**. A nucleolus occupies well under 1%. At physiologically plausible
values the model predicts **0.4–3.8%** depletion — one to two orders of magnitude short.

The claim was withdrawn and the preprint reissued (Zenodo v3) reporting the limit rather than
announcing an error.

**In a data room:** an agreement between a model and a measurement is evidence only if the model
could have disagreed, and only if the parameter driving the agreement is one the paper actually
constrains. Ask which parameter the result is most sensitive to, and whether that parameter was
measured or assumed.

---

## 5. Power — the study that could not have found what it was looking for

**Live when this was written; resolved since, and the resolution is in the footnote below.**
Testing whether the Mpro inversion generalises to PD-L1.

**The check:** Hanley–McNeil standard errors on the panel actually available, computed *before*
the run. Seconds.

**Found:** PD-L1 is inactive-poor in ChEMBL — 98 measured inactives is the ceiling, not a design
choice. At ~357 actives against 98 inactives, SE ≈ 0.033, which gives a detection window of
**AUROC ≤ 0.43 or ≥ 0.60. Everything between is a blind zone**, and that blind zone covers most of
the interesting range. Mpro's own 0.427 would clear the bar here with no margin to spare.

So the reporting rule was fixed and committed before the number existed: a result in 0.44–0.59 is
reported as a **power limit, not as a failure to replicate**, because the panel cannot separate
"docking is fine here" from "inverted by less than Mpro's margin."

**In a data room:** "no significant difference" is the most common finding in the world and the
least examined. Ask what effect size the study had 80% power to detect. A negative result from an
underpowered study is not a negative result.

**What happened next, and it vindicates the rule rather than the panel.** The pre-committed power
limit was the right call for a reason we had not anticipated: PD-L1 turned out to be unusable on a
second and independent ground. Seven free descriptors score **0.9145** on that panel, so it is
very nearly separable from ligand properties alone and could never have spoken to binding at any
sample size. The power calculation protected a conclusion that a later confound check would have
voided anyway. Two independent reasons to distrust one panel, and the cheap one fired first.

A second target was needed regardless. Factor Xa supplied it: **AUROC 0.6775**, above chance, on
a panel that cleared every pre-flight gate. **The Mpro inversion does not generalise and the
general claim is withdrawn** — see the prior at the top of this document, which was rewritten for
the same reason.

---

## 6. Threshold integrity — the study that was its own yardstick

**Claimed:** a decomposition of six virtual-screening interventions on Mpro, each varied singly,
reporting which of them actually moves ranking quality. To decide whether an intervention "moved"
anything the manuscript used two resolution limits, 0.020 and 0.039.

**The check:** for each threshold in the package, name the dataset it was derived from, and ask
whether that dataset is the data being judged. Minutes.

**Found by a journal editor**, who desk-rejected the manuscript without external review. The
venue is not named here: the corrected manuscript is under active resubmission, and which journal
caught the defect adds nothing to the lesson.

- **0.020** was the disagreement between two *implementations* of a scoring function —
  implementation variance, not the run-to-run reproducibility of an unchanged protocol.
- **0.039** came from comparing two *search-effort conditions* — an intervention effect used as a
  noise floor.
- **The exhaustiveness comparison was simultaneously one of the six interventions and the
  threshold the six were judged against.** The intervention was evaluated against itself.

Because the floors are the decision rule for all six results, this is not a fixable paragraph. It
is the spine. No rebuttal was attempted; the editor was right.

**The part that is more useful than the rejection.** The repair — measure the floor from replicates
of an *unchanged* protocol, varying only the random seed — was implemented, and then our own
pre-submission gate blocked the resubmission on 19 September for a reason the editor never raised:
the new floor had been measured at exhaustiveness 4, while the manuscript's headline comparisons
run at 32. **A limit measured at one operating point and applied at another is the same defect one
level up.** There was a perfectly good argument that it was safe — less search is more stochastic,
so the exh=4 floor should bound the exh=32 floor — and an argument in the place where a
measurement belongs is what caused the rejection in the first place.

So it was measured. Matched on the same 339 compounds and 6 seeds: exh=4 sd **0.00812**, exh=32 sd
**0.00626**, ratio 0.77, paired Pitman–Morgan **p = 0.624**. Conservative in direction, **not
established** — so the resolution was to use the exh=32 floor rather than to win the argument.

Three findings followed that no argument would have produced:

1. **The headline intervention did not survive.** Eight-fold search effort was reported at
   **+0.0185**, from one unpaired run minus another. Paired within seed on a fixed panel it is
   **+0.00417, sd 0.00968, n=6, 95% CI [−0.00598, +0.01433]** — spanning zero, t p = 0.339,
   Wilcoxon p = 0.5625. Roughly a third the size, and indistinguishable from nothing.
2. **Floors are panel-size specific.** A bound measured on 755 compounds is about **1.5×** too
   small to charge against a claim computed on 339, so using it there waves through effects inside
   the noise. Predicted 0.00742 by 1/n scaling against a measured 0.00812 — a ratio of 1.094, from
   a prediction using data the check does not otherwise touch.
3. **One of our own readings did not survive matching.** An earlier conclusion that exhaustiveness
   32 was *noisier* came from comparing a 343-compound sd against a 755-compound one.

**In a data room:** ask where every threshold came from. A decision rule derived from the data it
judges invalidates everything downstream of it no matter how carefully that work was done — and
this is the one defect class that a careful reader can find without re-running anything.

---

## 7. Artefact integrity — the bundle that was assembled around a receptor that had just failed

**Caught 23 August 2026, minutes before 455 ligands went to compute.**

The receptor selection was correct: 5J89 failed its redocking gate, 5J8O passed, and the passing
structure was registered in the configuration.

**The check:** md5 the receptor inside the shipped bundle against the receptor that passed the
gate. Seconds.

**Found:** they differed — `b83fcd22` against `4ca3a9d9`. The bundling script resolved the receptor
by **naming convention** (`receptors/{target_id}_receptor.pdbqt`) rather than from the path in the
configuration file. The convention path held the 5J89 structure that had just failed.

So the run would have docked 455 ligands against a **gate-failed receptor**, using the **passing
receptor's box coordinates** — which in the other crystal's frame sit in empty solvent — and
returned a perfectly well-formed AUROC. No exception, no warning, nothing in any log.

**In a data room:** ask whether the artefact that was validated is the artefact that was used, and
whether anything checks. Validation and execution are two different files until someone compares
them.

---

## Four shorter cases

Each sits under a check already covered above, and each is short because the check is cheap. They
are included because the seven long cases all end in a withdrawal, and these do not — three were
caught before anything was spent, which is what the checklist is actually for.

### 8. The gate whose first criterion rejected the textbook case *(check 2)*

The redocking gate originally required the near-native pose to **be rank 1**. That rule fails
trypsin/benzamidine (3PTB), the easiest redocking case in structural biology, **two times in
three**:

| seed | near-native RMSD | its rank | top-pose RMSD | score gap |
|---|---|---|---|---|
| 1 | 0.42 Å | 2 | **0.43 Å** | +0.030 kcal/mol |
| 7 | 0.41 Å | 1 | **0.41 Å** | +0.000 |
| 99 | 0.43 Å | 2 | **0.45 Å** | +0.029 |

The top pose is *itself* near-native every time. Rank was recording which of two indistinguishable
correct poses won an RNG tie, at a score separation of 0.03 kcal/mol against a Vina RMSE of ~2.8.
**A gate that rejects benzamidine rejects everything**, so it would have been switched off inside a
week and the pipeline would have gone back to ungated docking. The criterion is now top-pose
RMSD ≤ 2.0 Å — what a prospective screen actually consumes, since it reads rank 1 and nothing else.

What makes the gate worth anything is that it discriminates in both directions, stably across
seeds (exhaustiveness 16, seeds 1/7/42/99):

| target | verdict | top-pose RMSD across seeds |
|---|---|---|
| 3PTB trypsin/benzamidine | **PASS** | 0.43, 0.41, 0.43, 0.45 |
| 1STP streptavidin/biotin | **FAIL** | 6.00, 5.99, 5.98, 5.99 |
| 3AI8 cathepsin B | **FAIL** | 2.61, 2.61, 2.60, 2.60 |

No verdict flips on seed, and the failing RMSDs reproduce to 0.01–0.02 Å — these are properties of
the protocol, not sampling noise. **A suite where everything fails cannot distinguish a working
gate from a broken one**, which is why the passing case has to be in it.

**In a data room:** ask what the gate passes, not only what it rejects.

### 9. The receptor the gate correctly refused to judge *(check 2)*

Cathepsin S (3N4C) returns **NOT_APPLICABLE**, not PASS and not FAIL: its native ligand is covalent
to the catalytic **Cys25** — measured at 1.77 Å to CYS25A — and cathepsins are cysteine proteases
whose inhibitors are mostly covalent. A non-covalent scoring function cannot represent that binding
mode at all, so a FAIL would have been read as "this protocol is bad here" when the truth is "this
question cannot be asked this way."

The same defect sat in the Mpro arm, where **7K40 was rejected** because its co-crystal ligand is
covalent to **Cys145** and its box sat 6.3 Å off the ligand centroid; 7VU6 was adopted instead.

A third verdict costs one branch and it is the difference between a gate that informs and a gate
that mislabels. Checking the covalency of a native ligand takes seconds; it was skipped once and
cost days of decoy-control and redocking work.

**In a data room:** a screen against a covalent-inhibitor site, scored non-covalently, is not a
weak result. It is not a result.

### 10. The control that could not have passed, and then did *(check 4)*

A positive control ran at **n = 4 pairs against a two-sided sign-flip test**, whose minimum
achievable two-sided p is 2/2⁴ = **0.125**. Observed p was **0.12475** — the control had fired
*maximally*, 4 of 4 in the correct direction, the most extreme arrangement available — and still
could not reach 0.05. Unpassable by construction: the mirror of a control that cannot fail, and
both look like a passing experiment. Re-run at **n = 8**, where the minimum reachable p is 0.0078,
it fires at **p = 0.0081**. The effect was real the whole time; the instrument could not say so.

Three siblings landed in the same run: an **unpaired contrast gate in a paired design**, comparing
a mean shift to 3× the *between-slab* spread — the variance pairing exists to remove — which called
a 5-of-5 consistent effect "no contrast"; a **stationarity test that measured fluctuation**,
differencing two single checkpoints, making 40,000 steps look *less* equilibrated than 20,000; and
a **units error**, an fmax *force* criterion in eV/Å used as an energy floor.

**Every one was caught by a number failing to make sense, not by review.** That is the
uncomfortable part, and the reason to check the arithmetic of a test before running it rather than
after.

**In a data room:** ask what the smallest p this design can return at this n is. If it exceeds the
threshold the study reports against, the control was decorative.

### 11. The bundle that was assembled around the wrong receptor *(check 6)*

Told in full as case 7. Recorded separately here because the fix generalises past the incident:
`make_bundle.py` resolved the receptor by the naming convention `receptors/{target_id}_receptor.pdbqt`
while the validated path lived in `receptors.json`. On PD-L1 the convention path held **5J89**,
which had just failed its gate, while the configuration pointed at **5J8O**, which passed. The
bundle would have docked **455 ligands** against the failed structure using the passing structure's
box — a different crystal frame, so the box sat in empty solvent — and returned a well-formed AUROC.
Caught by comparing receptor md5s, `b83fcd22` against `4ca3a9d9`, minutes before compute.

**The rule, which is cheaper than the check:** a path built by naming convention will eventually
disagree with the path in the configuration. Prefer the configuration field and let a missing one
fail loudly.

---

## The pattern underneath all eleven

**A verification is a negation.** Its success state is "found nothing wrong" — which is exactly
what a *broken* verification produces. Positive results announce themselves; absent ones do not.
In all eleven cases the failure mode was indistinguishable from a clean result, and defaulted to
the permissive reading.

Which yields the working rules:

1. **Every gate needs a positive control that must pass.** A suite where everything fails cannot
   distinguish a working gate from a broken one.
2. **Before trusting a null, reproduce a known-good number through the same code path.**
3. **A stale or missing input must read as UNKNOWN**, never as its last value and never as a pass.
4. **md5 what shipped against what was validated.**
5. **Never resolve a path by convention when a configuration field exists.**
6. **No threshold may be derived from the data it judges**, and a noise floor must be measured on
   the axis the change moves, at the operating point the claim is made at, on a panel of the
   claim's own size.
7. **Order of operations: identity → applicability → provenance → power → threshold → integrity →
   compute.** The cheap check always comes before the expensive work. Every time it was skipped
   here, it would have saved days.

And one that is not a check but a habit, because it accounts for three of the corrections in this
document and every one of them flattered us until it was run: **where you can measure instead of
argue, measure.** The argument for the exhaustiveness-4 bound was sound. The argument that the
Mpro inversion was general was sound. Both were wrong, and in both cases the measurement was
cheaper than the case being made for skipping it.
