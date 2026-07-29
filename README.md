# Clinical-Trial Drug-Demand Forecasting

![Computational framework](figures/framework.svg)

**Forecasting the drug a clinical-trial depot must stock under uncertain enrolment and consumption —
a compound-distribution model, computed exactly by FFT convolution and cross-validated by three
independent methods (FFT · Monte Carlo · `aggregate`).**

---

## Overview

A trial must place drug at its supply depots *before* it knows how much will be needed: enrolment is
uncertain, and so is how much each patient consumes before completing or dropping out. This repository
models total depot demand as a **compound distribution** and sizes the buffer stock (the *overage*) a
depot needs to hit a chosen service level.

Its organising principle is that a forecast is only trustworthy if the same distribution, computed by
**independent methods**, agrees. Total demand is therefore obtained several ways that must reconcile:
an exact FFT convolution as the reference, a Monte-Carlo simulation, and the field-standard
`aggregate` library — anchored by closed-form moments and a known-answer case.

The accompanying technical note (`Clinical_Supply_Demand_Note.pdf`) develops the methodology and
results in full.

---

## Headline results

On a real GLP-1 cohort (weekly injectable, ~72-week treatment), a depot serving five sites over a
twelve-month horizon:

![Sizing overage: stock to the 99th percentile for 99% service](figures/fig_overage.png)

- **A small median, an enormous tail.** Typical demand is 416 kits (q50); covering 99 % of scenarios
  needs **≈ 97,000 kits** (q99) — a roughly **230-fold gap**, driven by heavy-tailed enrolment. That
  gap is the price of a service guarantee, and the cumulative distribution above is the instrument for
  choosing the service level.
- **Three independent methods agree to the kit.** From-scratch FFT, Monte Carlo, and `aggregate` land
  on the same q99 (97,238), with the known-answer case exact to ~10^-16.
- **A real numerical finding, surfaced by the cross-check.** The methods first *disagreed* in the tail
  (the FFT read q99 ~12 % low); the cause was **FFT aliasing** — the heavy tail wrapped around a
  too-narrow lattice — resolved by sizing the lattice to the distribution. A single method would have
  hidden it.

---

## Method

| Component | Approach |
|---|---|
| Patient count *N* | Over-dispersed **Negative Binomial** (mixed-Poisson / Poisson–Gamma), fitted by moments |
| Per-patient consumption *X* | Kits from the dosing schedule, tempered by a constant-hazard dropout model |
| Aggregation | Compound distribution *S = ΣXi*, solved **exactly** by FFT convolution of the generating function |
| Lattice control | Adaptive width — grown until the wrapped (aliased) tail mass is negligible |
| Validation | Known-answer case · moment identities · Monte Carlo · independent `aggregate` implementation |
| Data | Real Phase-3 trial parameters from ClinicalTrials.gov (curated to chronic parallel-group), dosing cross-checked against DailyMed |

The exact FFT reference and the Monte-Carlo simulation agree across the whole demand distribution,
including the far tail that sizes the buffer:

![FFT reference vs Monte Carlo agree on real GLP-1 demand](figures/fig_fftmc.png)

The aggregate machinery uses S. J. Mildenhall's [`aggregate`](https://github.com/mynl/aggregate) package.

---

## Scope

The model fits trials with **uncertain patient counts and supply committed ahead of enrolment** —
small molecules, oral drugs, and (the high-value case) **monoclonal antibodies and other biologics**
dispensed from depot stock. It does **not** fit bespoke per-patient therapies such as **CAR-T**, where
each dose is made for an already-identified patient and supply is a scheduling problem, not a
distribution.

---

## Repository contents

| Path | Description |
|---|---|
| `clinical_supply_validation.ipynb` | Complete, reproducible implementation (acquisition + model + validation + figures) |
| `Clinical_Supply_Demand_Note.pdf` | Technical note: methodology, validation, results |
| `figures/` | Framework banner and result figures used in the note and this README |

---

## Requirements & reproduction

Python 3.12 or later, with:

```
numpy · pandas · scipy · matplotlib · requests · aggregate
```

To reproduce:

1. Run the notebook top to bottom (Kernel → Restart & Run All). The **acquisition half** (§1–§2)
   fetches real trial parameters live from ClinicalTrials.gov and DailyMed and needs internet.
2. The **model half** (§3–§4) runs offline — from the frozen `params.json` if acquisition has run, or
   from realistic built-in defaults otherwise — and regenerates every figure and validation table.

*Real public parameters, synthetic generating layer:* inputs trace to real trials (by NCT id), the
simulation runs from them, so the ground truth is known and the study reproduces without re-fetching.

---

## Author

**Erik Van Releghem** · PHNX

A reproducible research implementation, intended for actuarial and educational use rather than
production software.
