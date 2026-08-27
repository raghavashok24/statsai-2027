# Martingale Flow Matching — Literature Review & Verification

In-depth literature review for the ICLR proposal **"Martingale Flow Matching: arbitrage-free generative models via constrained stochastic bridges."** This repo contains the 37 foundation papers (PDFs, organized by pillar), per-paper descriptions of what each contributes to the proposal, the **verification audit of every theorem** against the published mathematics, the **gaps and inconsistencies found and fixed**, and the **new frontier directions** the review opened.

> **Bottom line.** The review confirmed the core lane is open (no neural/simulation-free algorithm for martingale Schrödinger bridges exists), but it materially changed the proposal in seven ways — including one genuine mathematical inconsistency found and repaired (§3.G2), one theorem whose one-period case turns out to be already proven (§3.G3), and one theorem upgraded from *open* to *strategy-sound* because the literature supplies its missing component (§3.G4).

> **v2.0 — [DEVELOPMENTS.md](DEVELOPMENTS.md).** The six new theoretical developments (T-A … T-F) were subsequently written out in full and put through a 13-agent adversarial verification (hostile-referee pass with explicit numerical counterexamples, literature pass, completeness critic). Four of six broke *as first stated* and were repaired into stronger final forms — most importantly, the pricing certificate now targets the **arbitrage-free price interval**, which the counterexamples prove is the only correct target. The complete corrected statements, every counterexample found, the fixes, and the errors-vs-novelty ledger live in `DEVELOPMENTS.md`. The nine additional papers the verifiers surfaced are in the tiers below. Note: v2.0 supersedes some v1.1 claims in §3–§4 of this README (notably G4's certificate form and G7's Kellerer framing); §3–§4 are kept as the historical record of the review, with `DEVELOPMENTS.md` as the current truth.

---

## 1. Repo layout — a reading path

Papers are organized as a **pedagogical reading path**: tier 0 is what you need to understand the paper at all; each later tier adds one layer of the paper's machinery, in increasing depth; file-name prefixes (`01-`, `02-`, …) give the suggested order within each tier.

```
papers/
  0-baselines-read-first/          # entropic OT notes, flow matching, interpolants, IPM/WGAN, deep hedging
  1-martingale-transport-core/     # the mathematical object: mimicking, martingale Benamou–Brenier, Bass martingales
  2-classical-algorithms-sinkhorn/ # how it is computed classically: Sinkhorn theory, martingale Sinkhorn, Bass fixed point, IPF
  3-ml-bridge-matching/            # the ML machinery MFM extends: DSB, DSBM, [SF]²M, constrained flow matching
  4-stability-and-certificates/    # the certificate layer: stability/INSTABILITY, adapted Wasserstein, statistics
  5-frontier-2025-2026/            # the newest mathematics the paper sits on (martingale Schrödinger bridges, Schrödinger–Bass)
  6-baselines-to-beat/             # the empirical generative-model baselines
DEVELOPMENTS.md                    # v2.0: the corrected theorem suite + full verification record
```

Files are named `<order>-<slug>-<arXiv-id>.pdf`. Every PDF was downloaded from arXiv (or the author's public page) and verified against its intended title. Classical pre-arXiv works (Strassen 1965; Kellerer 1972; Csiszár 1975; Breeden–Litzenberger 1978; Bass 1983; Gyöngy 1986; Avellaneda et al. 1997; Guyon 2020/2024; Conze–Henry-Labordère 2021) are cited but not redistributed here.

**Added in v2.0 (surfaced by the adversarial verification):** tier 2: `2502.20264` (exponential convergence of general IPF, incl. martingale constraints — resets T-B's novelty), `2311.14567` (Bass local-vol calibration, linear-rate fixed point); tier 4: `2407.21492` (bounding adapted Wasserstein by W₁ under Lipschitz kernels), `1807.04211` (Obłój–Wiesel, robust superhedging estimation — the plugin-failure warning T-F must engage); tier 5: `2204.12250` (Nutz–Wiesel–Zhao, martingale Schrödinger bridges & semistatic portfolios), `2401.05209` (Nutz–Wiesel, one-period martingale Schrödinger bridge), `2603.27712` (Schrödinger–Bass semimartingale OT); tier 6: `2601.19312` (LightSBB-M) and `2604.07159` (SBBTS) — the two 2026 neural Schrödinger–Bass generators T-E must be positioned against.

**Keep this repo private.** arXiv PDFs are downloaded under arXiv's license for personal research use; several do not permit public redistribution.

---

## 2. Verification methodology (the "triple check")

Each claim in the proposal was checked three independent ways:

1. **Dependency check** — each theorem was decomposed into the published results it must rest on, and each of those results was located, downloaded, and its statement read against the use the proposal makes of it (not just its title).
2. **Novelty check** — targeted searches (August 2026) for each contribution claim, specifically hunting for scooping papers; the searches that killed two sibling ideas earlier in this project (adversarial CVaR control; performative-portfolio framing) demonstrate the sweep finds negative results, not just confirmation.
3. **Consistency check** — the proposal's objects were checked for internal mathematical coherence (well-definedness of the entropy functional, feasibility of constraint sets, attainment issues, dimension restrictions). This pass is what caught the continuous-time entropy inconsistency (G2 below).

What "verified" cannot mean: theorems not yet written have no proofs to check. The honest statuses are **verified-classical** (assembly of published results), **strategy-sound** (standard machinery, conditions identified, no speculative step), and **open** (new mathematics required, gated before implementation).

---

## 3. What the review changed in the proposal (v1.0 → v1.1)

### G1 — Missing prior art: the martingale-Sinkhorn lineage *(positioning fixed)*
The proposal's v1.0 claim of being "the first scalable algorithm" was too strong. A whole lineage solves entropic martingale problems with Sinkhorn-type iterations, all grid-based / low-dimensional:
- **Guyon (2020, Risk; 2024, Finance & Stochastics)** — dispersion-constrained martingale Schrödinger problems solved by an extended Sinkhorn algorithm; *exactly* jointly calibrates SPX and VIX smiles. The most important omitted citation.
- **De March & Henry-Labordère (2019)** — entropic MOT Sinkhorn for arbitrage-free surface construction; Henry-Labordère (2019) — martingale Schrödinger bridges for stochastic volatility.
- **The Martingale Sinkhorn Algorithm** (`2310.13797`) — computes Bass martingales in arbitrary dimension via measure-preserving Sinkhorn, proven equivalent to the Conze–Henry-Labordère fixed point.
- **Acciaio–Marini–Pammer** (SIAM J. Fin. Math.) — the Bass local-vol fixed point converges at a **linear rate**; `2411.04321` makes it robust and fast.
- **Avellaneda et al. (1997)** — weighted Monte Carlo: entropy-calibrated simulators are a 25-year-old idea (without martingale projection or theory).

**Change:** MFM's claim becomes *"the first neural, simulation-free, path-space algorithm with statistical guarantees"* — the relationship to grid martingale-Sinkhorn is exactly the relationship neural OT bears to classical Sinkhorn. Bonus: the grid methods become strong exact baselines for the experiments (they strengthen E2/E3, since low-dimensional ground truth now exists beyond LPs).

### G2 — Mathematical inconsistency found and repaired: infinite KL in continuous time *(correctness fix)*
In continuous time, two martingale diffusions with different volatility functions induce **mutually singular** path measures, so KL(P‖R) = +∞ for essentially every candidate P — the v1.0 continuous-time entropic objective was ill-posed as written. The literature supplies the correct objects:
- **Specific relative entropy** (Föllmer; Gantert), the renormalized per-unit-time entropy, is the right divergence between continuous martingales — studied precisely in this calibration context by **Backhoff & Zhang** (`2602.14776`), where it is written as an integral functional of the quadratic-variation density.
- The **Schrödinger–Bass bridge** (`2604.02312`) instead penalizes drift entropy plus a quadratic volatility cost — an alternative well-posed continuous-time formulation.

**Change:** MFM v1.1 is formulated **primarily in discrete time** (maturities plus a simulation grid), where KL is finite and Csiszár's I-projection theory applies verbatim; the continuous-time limit is discussed via specific relative entropy with `2602.14776` cited. This costs nothing algorithmically (the algorithm was always discrete-step) and removes a false statement.

### G3 — Theorem 2's one-period case is already proven *(credit reassigned, novelty repositioned)*
**Chen–Conforti–Ren–Wang** (`2407.14186`, Math. of OR) prove, for one-period entropic MOT on ℝ: dual well-posedness *without assuming optimal potentials exist*, **no primal–dual gap**, and **exponential convergence of Sinkhorn**. This is the single-period core of v1.0's Theorem 2.

**Change:** Theorem 2 v1.1 = the **multi-period, path-space extension** plus the genuinely new part, an **inexact-projection analysis**: alternating I-projections where each projection is executed by a neural regression with error ε (statistical + approximation), with convergence to an O(ε)-neighborhood. Error-propagation for neural Sinkhorn-type schemes does not exist in the martingale setting and is the ML-native contribution; the exact-projection skeleton is now cited, not claimed.

### G4 — Theorem 5's missing component exists in the literature *(status upgraded: open → strategy-sound)*
The v1.0 certificate needed a new lemma bounding the adapted-Wasserstein distance to the bridge by (marginal error + martingale defect). The review found both halves:
- **Blanchet–Wiesel–Zhang–Zhang** (`2401.12197`) — *empirical martingale projections in adapted Wasserstein distance*: an explicit characterization of the (smoothed) AW-projection distance onto martingale couplings, with **parametric convergence rates** and finite-sample results — this is precisely the "defect controls AW-distance-to-martingale-class" component, in the one-step case.
- **Bartl–Wiesel** (`2208.05656`) — multiperiod stochastic optimization values (superhedging, optimal stopping, pricing) are **Lipschitz in adapted Wasserstein distance** — this is precisely the "AW controls pricing error" component, and it also *defines* the payoff class for the certificate in a citable way.

**Change:** Theorem 5 v1.1 composes: (defect ⟹ AW-projection bound, extending `2401.12197` from one step to the multi-maturity chain) ∘ (marginal stability of the entropic bridge, 1D, via `1904.04171`/`1905.04574` + entropic strong convexity) ∘ (AW-Lipschitz pricing, `2208.05656`). One composition lemma remains new (the multi-step extension of the projection bound); everything else is assembly. Status upgraded to **strategy-sound**; the two-week proof gate stays, but it now targets a much smaller gap. The d ≥ 2 exclusion stands (`2101.06964`).

### G5 — Constrained flow matching moved in 2025–26 *(positioning updated)*
PolyFlow (`2607.04513`, polyhedral constraints by architecture), Physics-Constrained FM (`2506.04171`, hard equality constraints via corrections), mirror FM (`2510.08929`), chance-constrained FM. **None handles conditional-moment (martingale) constraints** — pointwise/almost-sure constraints on states, not conditional expectations over the filtration — so the new-constraint-class claim survives, but the related-work section must include this family and say exactly why their techniques don't transfer (a martingale constraint cannot be enforced samplewise; it couples the law across time).

### G6 — Better evaluation methodology, from the literature *(experiments strengthened)*
`2401.12197` already applies its **martingale-coupling hypothesis test** to detect arbitrage in neural generative asset-pricing models. MFM's experiments adopt SE-MPD as the *external, published* arbitrage metric for all baselines (replacing v1.0's ad-hoc defect readout), and MFM should be the only model that passes by construction. Using the field's own test as the judge is both more rigorous and more persuasive.

### G7 — New frontier: the smoothed d ≥ 2 program *(new direction, potential second paper)*
Two independent threads show **Gaussian smoothing is the lever that tames every d ≥ 2 pathology**:
- **Pammer–Robinson–Schachermayer** (`2210.13847`): Kellerer's theorem *fails* in d ≥ 2 without regularization, but **after Gaussian regularization a mimicking Markov martingale diffusion exists in arbitrary dimension** (with counterexamples showing smoothing is necessary).
- `2401.12197` and `2503.10827`: smoothing also fixes the statistical side (smoothed empirical martingale projections; fast rates for smooth adapted Wasserstein).

**Conjectured frontier result:** for Gaussian-smoothed marginals, a d ≥ 2 pricing certificate holds — smoothing restores exactly the structure whose absence drives the Brückerhoff–Juillet instability. If true, this converts the paper's hard d = 1 boundary into a *bandwidth-parameterized* guarantee in any dimension: certified pricing for smoothed surfaces, with the bandwidth as an explicit knob trading certificate strength against calibration exactness. This is the strongest candidate for "new foundation" status found in the entire review — it is not in the literature, both of its ingredients are, and it is falsifiable quickly on the `2101.06964` counterexample (test whether the instability survives smoothing).

---

## 4. Theorem ledger v1.1 (post-review)

| # | Statement (short) | Rests on | Status v1.0 → v1.1 |
|---|---|---|---|
| T1 | No static arbitrage ⟹ convex order ⟹ feasibility, with a finite-entropy element | Strassen 1965; Kellerer 1972; `1708.04869`, `2306.11019` | verified → **verified** |
| T2 | Alternating neural I-projections converge to the entropic martingale bridge (discrete time, multi-period, ε-inexact) | Csiszár 1975; **`2407.14186` (one-period case proven)**; new: inexact-projection propagation | strategy-sound → **strategy-sound, core case now in the literature; novelty = multi-period + inexact analysis** |
| L3 | Martingale ⟺ zero optimal trading gain; defect is an IPM over predictable strategies | elementary; IPM framing per `1701.07875` | verified → **verified** |
| T4 | Drift removal by Markovian projection preserves marginals | Gyöngy 1986; `1011.0111` (Brunick–Shreve, weaker conditions) | verified → **verified (conditions weakened via Brunick–Shreve)** |
| T5 | d = 1 pricing certificate: pricing error ≲ Σ marginal error + martingale defect | `2401.12197` + `2208.05656` + `1904.04171`/`1905.04574`; one new composition lemma | **open → strategy-sound** |
| — | d ≥ 2 certificate impossible in general | `2101.06964` | excluded → **excluded (unsmoothed); G7 conjectures the smoothed version** |
| — | Continuous-time formulation | `2602.14776` (specific relative entropy); `2604.02312` | **ill-posed as v1.0 wrote it → repaired (G2)** |

---

## 5. The foundation papers, by pillar

### A — Martingale transport, Bass martingales, martingale Schrödinger bridges

| Paper | What it is | Role in MFM |
|---|---|---|
| `1708.04869` Backhoff-Veraguas, Beiglböck, Huesmann, Källblad — *Martingale Benamou–Brenier* (Ann. Prob. 2020) | The martingale analogue of Benamou–Brenier; optimizers are **stretched Brownian motions** / Bass martingales | Existence of finite-entropy feasible points (T1); the canonical structure MFM's solutions approximate |
| `2306.11019` Backhoff, Beiglböck, Schachermayer, Tschiderer — *Existence of Bass martingales in ℝᵈ* | Duality and existence theory for Bass martingales in arbitrary dimension | Multi-asset feasibility; grounds the d ≥ 2 empirics |
| `2310.13797` — *The Martingale Sinkhorn Algorithm* | Measure-preserving Sinkhorn computing Bass martingales, any dimension; equivalent to the Conze–Henry-Labordère fixed point | **Prior art (G1)**; exact baseline for E2; candidate initialization for MFM |
| `2411.04321` — *Robust and Fast Bass Local Volatility* | Numerically robust Bass local-vol calibration | Baseline; engineering reference for the fixed-point route |
| `2508.20017` — *Stretched BM: convergence of dual optimizers* | Dual-side convergence theory for stretched Brownian motion | Supports T2's dual-free (primal) proof choice; recent dual attainment context |
| `2604.02312` — *Schrödinger–Bass bridge* | Interpolates Schrödinger bridge ↔ martingale Benamou–Brenier; optimal process = monotone map ∘ Schrödinger bridge | The structure theorem behind the **Bass architectural parameterization** (§6); well-posed continuous-time objective (G2) |
| `2510.10860` — *MOT and martingale Schrödinger bridges for SV calibration* | The 2025 calibration-by-bridge paper (grid/PDE) | The immediate mathematical predecessor MFM makes scalable |
| `2607.15872` Acciaio, Marini — *q-Bass martingales, semidiscrete* | Extends Bass existence to semidiscrete settings | Handles discrete option-implied marginals rigorously |
| *(no PDF)* Guyon 2020/2024 — *Dispersion-constrained martingale Schrödinger problems* (Risk; Fin. & Stoch.) | Exact joint SPX/VIX calibration via extended Sinkhorn | **The key omitted prior art (G1)**; must-cite; strongest evidence entropic martingale calibration works on real markets |

### B — Stability, instability, adapted Wasserstein, statistical theory

| Paper | What it is | Role in MFM |
|---|---|---|
| `1904.04171` Backhoff-Veraguas, Pammer — *Stability of MOT and weak OT* (Ann. Appl. Prob. 2022) | MOT is stable in d = 1 | The stability half of T5 |
| `1905.04574` Wiesel — *Continuity of MOT on the real line* | Quantitative continuity of 1D MOT in its marginals | Quantitative version used in T5's bound |
| `2101.06964` Brückerhoff, Juillet — *Instability of MOT in d ≥ 2* (ECP 2022) | Explicit counterexample: MOT unstable in value and optimizers for d ≥ 2 | Scopes T5 to d = 1; the obstruction G7 attacks via smoothing; falsification target for the G7 conjecture |
| `2102.02718` — *Stability of MOT: set-valued approach* | Complementary stability formulation | Robustness of T5 to the choice of stability notion |
| `2002.07261` Backhoff, Bartl, Beiglböck, Wiesel — *Estimating processes in adapted Wasserstein* (AAP 2022) | Adapted empirical measure achieves optimal W-rates | Finite-sample statistical layer for the certificate (§6, statistical certificate) |
| `2401.12197` Blanchet, Wiesel, Zhang, Zhang — *Empirical martingale projections via adapted Wasserstein* | Explicit AW-projection distance onto martingale couplings; parametric rates; **arbitrage hypothesis test applied to neural generative pricing models** | **The missing component of T5 (G4)**; SE-MPD becomes the external evaluation metric (G6) |
| `2208.05656` Bartl, Wiesel — *Sensitivity of multiperiod optimization in AW* | Pricing/hedging/stopping values are AW-Lipschitz | Defines T5's payoff class; the pricing half of the certificate |
| `2503.10827` — *Fast rates for smooth adapted Wasserstein* | Smoothing beats the curse of dimensionality for AW rates | Statistical leg of the smoothed d ≥ 2 program (G7) |
| `2210.13847` Pammer, Robinson, Schachermayer — *Regularized Kellerer theorem in arbitrary dimension* | Kellerer fails in d ≥ 2 unsmoothed; Gaussian regularization restores mimicking Markov martingale diffusions | **Foundation of the G7 frontier**; also the honest scope note for T1 in d ≥ 2 |
| `2602.14776` Backhoff, Zhang — *Reciprocal specific relative entropy between continuous martingales* | The correct divergence between continuous martingale laws (KL is infinite between different-vol martingales) | **The G2 repair**; continuous-time formulation of MFM |

### C — Entropic OT, Sinkhorn, I-projections

| Paper | What it is | Role in MFM |
|---|---|---|
| Csiszár 1975 *(cited, no PDF)* — *I-divergence geometry* (Ann. Prob.) | Alternating I-projections onto affine sets converge to the projection onto the intersection | T2's convergence skeleton |
| `2407.14186` Chen, Conforti, Ren, Wang — *Sinkhorn convergence for entropic MOT* (Math. OR) | One-period EMOT on ℝ: duality without assumed potentials, no gap, exponential Sinkhorn rate | **Proves T2's one-period core (G3)**; MFM extends to multi-period + inexact neural projections |
| `2212.06000` — *Convergence rate of Sinkhorn* | Modern quantitative Sinkhorn analysis | Rate techniques for the inexact analysis |
| `nutz-entropic-ot-lecture-notes` — Nutz, *Introduction to entropic OT* | The standard reference for entropic OT structure | Background; duality and stability toolbox |

### D — ML: flow matching, bridges, constrained generation, adversarial, hedging

| Paper | What it is | Role in MFM |
|---|---|---|
| `2210.02747` Lipman et al. — *Flow Matching* (ICLR 2023) | Simulation-free training of continuous flows via conditional regression | The training paradigm MFM extends |
| `2303.08797` Albergo, Boffi, Vanden-Eijnden — *Stochastic Interpolants* | Unifying framework for flows and diffusions with arbitrary endpoint couplings | The interpolant machinery for the marginal step |
| `2106.01357` De Bortoli et al. — *Diffusion Schrödinger Bridge* (NeurIPS 2021) | IPF for neural Schrödinger bridges | The neural-alternating-projection precedent (without martingale constraints) |
| `2303.16852` Shi et al. — *Diffusion Schrödinger Bridge Matching* (NeurIPS 2023) | Iterative Markovian fitting; scalable bridge solving | Closest ML algorithmic template; MFM adds the second (martingale) constraint family |
| `2307.03672` — *Simulation-free Schrödinger bridges ([SF]²M)* | Score + flow matching for SB | Simulation-free implementation of the marginal step |
| `2310.02233` — *Generalized Schrödinger Bridge Matching* | SB matching with task-specific costs | Template for adding cost structure (e.g., dispersion constraints à la Guyon) |
| `2506.04171` — *Physics-Constrained Flow Matching* | Hard state constraints via guided corrections | Constrained-FM family (G5): samplewise constraints — contrast class |
| `2607.04513` — *PolyFlow: Lagrangian dual flows* | Polyhedral constraints by architecture | Constrained-FM family (G5); relevant to price-space constraints |
| `2510.08929` — *Mirror Flow Matching* | FM on convex domains via mirror maps | Constrained-FM family (G5) |
| `1701.07875` Arjovsky et al. — *Wasserstein GAN* | IPM-based adversarial training | The defect 𝔡(P) is an IPM over predictable strategies (L3); stability heuristics for the adversarial step |
| `1802.03042` Buehler et al. — *Deep Hedging* | RL/deep hedging framework | Downstream consumer (E5); the system harmed by simulator arbitrage |
| `1011.0111` Brunick, Shreve — *Mimicking an Itô process* (AAP 2013) | Gyöngy's theorem under weaker conditions | T4 under realistic assumptions (unbounded coefficients) |

### E — Finance generative baselines

| Paper | What it is | Role in MFM |
|---|---|---|
| `2105.11053` — *Arbitrage-free neural-SDE market models* | Parametric neural SDEs, no static arbitrage by construction | Baseline; parametric-family contrast |
| `2608.00616` — *Latent FM for arbitrage-aware IV surfaces* (2026) | Soft-penalty latent flow matching for surfaces | Baseline; the penalty-vs-projection contrast (G5/G6) |
| `2601.11209` *SANOS* / `2608.12587` *DYSANOS* (2026) | Parameterized arbitrage-free surface generators | Baselines; surface-level (not path-measure) contrast |

Classical citations without PDFs: **Strassen 1965** (existence under convex order), **Kellerer 1972** (Markov mimicking martingales, d = 1), **Csiszár 1975** (I-projections), **Breeden–Litzenberger 1978** (marginals from call prices), **Bass 1983** (the Bass construction), **Gyöngy 1986** (mimicking), **Avellaneda et al. 1997** (weighted Monte Carlo), **Guyon 2020/2024** (dispersion-constrained martingale Schrödinger; SSRN/Risk/Finance & Stochastics).

---

## 6. New directions the review opened (beyond the fixes)

1. **The smoothed d ≥ 2 certificate (G7)** — the headline frontier; see §3.G7. First experiment: test whether the `2101.06964` counterexample survives Gaussian smoothing. If it doesn't, the conjecture has legs and this is a second paper laying a genuinely new foundation.
2. **Bass architectural parameterization** — `2604.02312` proves the optimal process is (monotone map) ∘ (Schrödinger bridge); Bass martingales are gradients of convex functions applied to Brownian motion. Parameterize the generator as **∇(ICNN) ∘ Brownian bridge** (input-convex neural network): martingality holds *by architecture* in the irreducible one-period regime, trained by the linear-rate fixed-point iteration (Acciaio–Marini–Pammer) instead of an adversarial game. The adversarial defect then becomes a *residual audit*, not the enforcement mechanism — likely a large stability win, and a clean ablation: architecture-enforced vs. adversarially-enforced martingality.
3. **A statistical certificate** — compose T5 with the adapted empirical measure rates of `2002.07261` (+ `2503.10827` smoothed): a **finite-sample** pricing-error guarantee from n observed surfaces/paths, turning the certificate from analytic to statistical — the form a model-validation function actually consumes.
4. **Dispersion-constrained MFM** — Guyon's VIX constraints are additional affine constraints on the path measure; the alternating scheme accepts them as a third projection. Neural joint SPX/VIX calibration at scale would be a flagship experiment (E3+) with an exact grid benchmark to verify against.
5. **Inexact-Sinkhorn theory as a standalone ML contribution** — the ε-inexact I-projection analysis (G3) applies to *any* neural Sinkhorn-type scheme (DSB, DSBM included), where error-propagation analyses are thin; stating it at that generality widens the ICLR audience.

---

## 7. Relation to the proposal document

The living proposal (v1.1, updated with G1–G7) is published here: **https://claude.ai/code/artifact/da28994d-0183-4208-afc7-0e2077ed278d**

Change log: v1.0 (2026-08-24) initial proposal → v1.1 (2026-08-25) this review: prior art added (G1), continuous-time formulation repaired (G2), T2 repositioned (G3), T5 upgraded (G4), constrained-FM positioning updated (G5), SE-MPD evaluation adopted (G6), smoothed-d≥2 frontier added (G7).
