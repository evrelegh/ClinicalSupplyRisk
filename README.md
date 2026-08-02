# Clinical-Trial Drug-Demand Forecasting

![Computational framework](figures/framework.svg)

> **Forecasting depot inventory for clinical trials through exact
> probabilistic modelling, independently verified by three computational
> implementations (FFT · Monte Carlo · `aggregate`) and validated before
> any operational conclusions are drawn.**

------------------------------------------------------------------------

## Overview

Clinical trials must commit expensive investigational drug inventory
months before enrolment is known. Underestimating demand risks missed
patient doses; overestimating demand leads to costly expiry.

This repository computes the **entire probability distribution** of drug
demand---not merely its expected value---and sizes depot inventory for
any desired service level.

The probabilistic ingredients themselves are well established: an
over-dispersed patient count, patient-level consumption, and
compound-distribution aggregation.

**The novelty is not the probabilistic model.**

The contribution is the **computational validation methodology**.

Every important numerical result is reproduced independently by three
fundamentally different computational approaches:

-   exact FFT convolution;
-   Monte Carlo simulation;
-   the industry-standard `aggregate` package.

Agreement between independent implementations is treated as a
prerequisite for interpretation rather than an optional consistency
check.

The accompanying technical note (`Clinical_Supply_Demand_Note.pdf`)
develops the methodology and results in detail.

------------------------------------------------------------------------

## Why this repository is different

Most studies stop once they have produced a forecast.

This repository first asks whether the forecast itself is numerically
trustworthy.

The exact FFT implementation, a separately developed Monte Carlo
simulation, and an independent implementation using the `aggregate`
package are required to reproduce the same distribution before any
engineering conclusions are accepted.

This validation strategy proved its value. The first comparison revealed
a significant discrepancy in the extreme tail. Rather than accepting one
implementation, the discrepancy was investigated until its origin was
identified: FFT aliasing caused by an insufficient lattice width. Once
corrected, all three methods agreed to the kit, while the known-answer
case remained exact to machine precision.

The validation process is therefore **part of the scientific
contribution**, not merely software testing.

The repository demonstrates a computational discipline suitable for
high-consequence quantitative models:

-   independent implementations;
-   investigation rather than acceptance of discrepancies;
-   reproducible numerical results;
-   engineering decisions based only on validated computations.

------------------------------------------------------------------------

## Contributions

-   exact probabilistic evaluation of the compound demand distribution
    by FFT convolution;
-   adaptive lattice sizing eliminating FFT aliasing;
-   independent Monte Carlo implementation for computational
    verification;
-   independent validation against the `aggregate` package;
-   analytical validation through closed-form moments and known-answer
    cases;
-   reproducible workflow using public clinical-trial data.

------------------------------------------------------------------------

## Headline results

-   **Heavy-tailed demand dominates planning.** A typical depot requires
    only a few hundred kits, while achieving a 99% service level
    requires roughly 79,000 kits.
-   **Three independent implementations converge.** FFT, Monte Carlo and
    `aggregate` agree on the complete distribution, including the
    extreme quantiles that determine inventory.
-   **Independent validation uncovered a genuine numerical issue.**
    Cross-validation exposed FFT aliasing that a single implementation
    would probably not have detected.

------------------------------------------------------------------------

## Method

  -----------------------------------------------------------------------
  Component                             Approach
  ------------------------------------- ---------------------------------
  Patient count                         Negative Binomial (mixed
                                        Poisson--Gamma)

  Patient demand                        Dosing schedule with
                                        constant-hazard dropout

  Aggregation                           Exact FFT convolution of the
                                        compound distribution

  Numerical control                     Adaptive lattice sizing

  Verification                          Known-answer case · moment
                                        identities · Monte Carlo ·
                                        `aggregate`

  Data                                  ClinicalTrials.gov + DailyMed
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Computational philosophy

This repository belongs to a broader series of quantitative
risk-modelling studies built around one principle:

> **Important numerical results should be independently verified before
> they are interpreted.**

The objective is therefore not merely to produce another forecast, but
to demonstrate a computational methodology in which **exact
probabilistic methods**, **independent numerical verification**, and
**decision-oriented interpretation** receive equal importance.

------------------------------------------------------------------------

## Repository contents

-   `clinical_supply_validation.ipynb` --- complete implementation.
-   `Clinical_Supply_Demand_Note.pdf` --- technical note.
-   `figures/` --- framework and result figures.

------------------------------------------------------------------------

## Author

**Erik Van Releghem · PHNX**

*Demonstrating quantitative modelling for high-consequence decision
support through exact probabilistic computation, independent
computational verification, and reproducible scientific software.*
