# MFM v2.0 — The Developed Theorems, After Adversarial Verification

This document contains the six new theoretical developments for the Martingale Flow Matching paper **in their corrected, final form**, together with the full verification record: what was originally claimed, what broke under hostile refereeing (with the explicit counterexamples found), what was repaired, and what each item's honest final status is.

**Verification protocol (the "triple check").** Each development went through three independent passes: (1) my own derivation and inline audit; (2) a **hostile-referee pass** — one max-effort agent per item instructed solely to refute it, checking every inequality line by line and constructing explicit numerical counterexamples; (3) a **literature pass** — one agent per item confirming every citation says what we use it for and hunting for prior art, plus a global **completeness critic** checking that the six items compose consistently. The refutation pass found genuine fatal-as-stated errors in four of six items. Every error is recorded below, because *the corrections are the reason the final suite can be trusted*: a suite that survived this process unchanged would be evidence the process was toothless.

**Honest status vocabulary.** PROVEN-AS-STATED = the final statement's proof was verified step-by-step by the hostile referee (after repairs). PROVABLE = proof route standard and checked, write-up remains. ASSUMPTION-EXPLICIT = correct under named, checkable assumptions stated in the theorem. CONJECTURE = clearly labeled, used by nothing else.

---

## Global setting (corrected)

Discrete time t ∈ {0,…,T}; maturities t_1 < … < t_K. Two ambient regimes, **never silently identified** (this was a verified source of error, see T-A):

- **ℝᵈ-regime:** path space (ℝᵈ)^{T+1}, integrable coordinates; ℳ = integrable martingale laws on it.
- **Grid regime** (for the projection algorithm): time-indexed finite grids K_0,…,K_T with **K_t ⊆ relint conv(K_{t+1})** (this replaces the broken "one grid at all times": on a time-constant grid, extreme points are absorbing, so no full-support martingale exists — the original Slater condition (A2′) was *unsatisfiable as written*, caught by both verification lenses).

Defect: 𝔡(P) := Σ_t E_P|E_P[X_{t+1} − X_t | X_{0:t}]|, with the verified adversarial identity 𝔡(P) = sup over measurable |H_t| ≤ 1 of the expected trading gain.

Payoffs F are L-Lipschitz for the sum path metric; where processes exit a compact set (they do — see T-A), F is extended by McShane extension, preserving L (a bookkeeping lemma the completeness critic demanded).

**Standalone Restoration Lemma (L-R), load-bearing for T-B, T-C, T-F.** Estimated marginals μ̂_k generically violate convex order, mean-consistency, and domain-support. L-R: there is a map onto grid-supported, mean-consistent, convex-ordered tuples (μ̃_k) with Σ W₁(μ̃_k, μ̂_k) ≤ C·(input violations), computable as a finite LP; constructive with explicit constants in d = 1 (isotonic-type), finite-LP with grid constants in d ≥ 2. The completeness critic identified this as a single unproved lemma silently used three times in v1; it is now stated once, as its own result. Status: PROVABLE (finite-dimensional convex analysis; the d ≥ 2 constants are grid-dependent and say so).

---

## T-A. Defect–Distance Equivalence — PROVEN-AS-STATED (ℝᵈ-regime)

**Theorem.** For every integrable P on (ℝᵈ)^{T+1}:
𝔡(P) ≤ 2·AW₁(P, ℳ)  and  AW₁(P, ℳ) ≤ T·𝔡(P),
where AW₁ is the bicausal (adapted) Wasserstein-1 distance and ℳ the integrable martingale laws on (ℝᵈ)^{T+1}. The constant 2 is **sharp**: the deterministic path (0, 1, 0) attains it. The upper bound is by the mean-shift construction Y_{t+1} = X_{t+1} − m_t(X_{0:t}) + Y_t, whose synchronous coupling is bicausal because the recursion is adaptedly invertible.

**What broke and how it was fixed.** As first stated, ℳ was the martingale laws *supported on the state grid K*, and in that form the upper bound is **false with unbounded violation ratio**: the referee's counterexample takes K = three affinely independent unit vectors (vertices of a triangle); barycentric uniqueness forces every K-supported martingale to be constant, so ℳ_K collapses to constant paths, and a two-point target law gives AW₁(P, ℳ_K)/(T𝔡(P)) → ∞ along an angle family. The repair is a one-line restatement (ℳ on ℝᵈ) under which the referee then verified **every step** of both proofs, including the anticausality argument E[ΔY_t | X_{0:t}, Y_{0:t}] = 0 and the endpoint-counting in the factor-2 bound. The triangle example is kept in the paper as a *warning result*: grid-supported no-arbitrage classes are genuinely farther away than the defect sees, so any grid-side defect-to-feasibility conversion must go through a grid-geometry constant (see T-B), never through this theorem.

**Why it matters (ML novelty).** The adversarial FTAP training loss is, up to constants (2, T), *the adapted-Wasserstein distance to the arbitrage-free class* — an adversarial loss with an exact metric identity, feeding directly into the certificate T-C. One-step antecedent: Blanchet–Wiesel–Zhang–Zhang (arXiv:2401.12197); new here: multi-step construction, bicausal invertibility, two-sided equivalence, sharp constant.

---

## T-B. Inexact cyclic I-projections — REPAIRED: correct weakened statement, PROVABLE

**What broke.** Both original claims were **refuted by explicit numerical counterexamples**. (a) Summable projection errors do *not* give convergence to the bridge P*: a single inexact step can land on a different point of ℳ∩𝒫, where every subsequent exact projection stalls (verified 4-point example, KL gap ≈ 0.03 forever). (b) With constant per-step error ε, errors accumulate *tangentially along the feasible set* — there is no restoring force — and the iterate drifts an ε-independent distance (verified: 55 steps of ε = 10⁻⁴ produced KL(P*‖P_n) ≈ 0.34). Quasi-Fejér arguments identify the limit only as *some* feasible point; Csiszár's limit-is-the-projection theorem is for the exact scheme started at R only. Additionally the scheme must be **cyclic over K+1 sets** (one per marginal, one for ℳ): the joint marginal projection is itself an inner Schrödinger system, not closed-form (critic's catch).

**Corrected theorem (budget form).** For the cyclic scheme started at P_0 = R on the corrected grid domain, with per-step inexactness ε_n measured against the exact I-projection and Σ√ε_n < ∞ (the √ is necessary — Pinsker enters once; lit-lens catch), the iterates converge to some P_∞ ∈ ℳ∩𝒫 with
KL(P*‖P_∞) ≤ C(ρ, grid) · Σ_n (ε_n + √ε_n).
Convergence **to P\* itself** is recovered under either (i) dual-coordinate inexactness (errors in the Sinkhorn potentials, not the measures — inexact-dual schemes preserve the limit), or (ii) a Dykstra-type correction step. The defect→KL conversion for the neural martingale step is a separate lemma via a grid-internal linear correction with an explicit grid-geometry constant (not via T-A, whose grid version is false — see above).

**Positioning (updated by verification).** The lit pass found **arXiv:2502.20264** (2025): exponential convergence of *general* iterative proportional fitting *including martingale constraints* — so the exact-scheme convergence is now essentially settled in the literature, and this item's honest novelty is precisely the **inexact/budget analysis with neural oracles and the computable dual-residual surrogate for ε_n**: the error-propagation theory that neural bridge methods (DSB, DSBM) also lack. That narrower claim is the defensible one, and the two counterexamples above are themselves publishable content: they show *why* naive inexact IPF loses the bridge, which no one has recorded.

---

## T-C. Robust-pricing certificate (interval form) — RESTRUCTURED: the correct target, PROVABLE in d=1

**What broke.** The original certificate bounded |E_P̂F − E_{P*}F| by defect + marginal errors, for a single target P*. This is **structurally false** — refuted independently by two agents and by my own inline audit, with a verified counterexample: two *exactly calibrated, zero-defect* martingales price F = |X_1 − X_0| at 2 and 5/3 while the entropic bridge prices it at ≈ 1.59; the right-hand side is 0. Defect + marginals cannot identify path-dependent prices because calibrated martingale measures are non-unique — this is the *textbook reason MOT price intervals are nondegenerate* (Beiglböck–Henry-Labordère–Penkner), and the v1 statement contradicted it. The completeness critic added a second structural failure: the pipeline trained one object (Bass/MBB selection, T-E) while certifying against another (KL bridge) — two selection principles in one pipeline.

**Corrected theorem (d = 1).** Let [V_min(μ), V_max(μ)] be the arbitrage-free price interval for payoff F over martingales calibrated to μ = (μ_k). Then
dist( E_P̂F, [V_min(μ), V_max(μ)] ) ≤ L·T·𝔡(P̂) + L·ω( Σ_k W₁(μ̂_k, μ_k) + T·𝔡(P̂) ),
where ω is the **explicit nonlinear modulus** ω(r) = inf_δ [K(δ)·r + δ] from Wiesel's quantitative 1D stability (arXiv:1905.04574) — *not* a linear Lipschitz constant, which the cited literature does not provide (verified over-claim, fixed). Route: T-A projects P̂ to a true martingale Q̂ at AW-cost ≤ T𝔡; Q̂'s own marginals are **automatically mean-consistent and convex-ordered** (the referee verified this step and called it out as a genuinely good feature of the construction); 1D marginal-stability moves Q̂'s price into the interval for μ. The interval form resolves the two-selection-principles inconsistency: it certifies the output of *either* training track (T-B's KL track or T-E's Bass track) without naming a bridge.

**Bridge-proximity addendum (separate, assumption-explicit).** Proximity to the specific entropic bridge holds only with an added hypothesis tying P̂ to it — a KL-optimality gap η, which is exactly what the T-B track outputs — plus strong convexity of KL on the full-support grid domain. Stated as its own corollary, never merged into the certificate.

**Fixed-temperature d ≥ 2 regime (assumption-explicit, honestly scoped).** With a *declared* entropic temperature (reference R_ε; the v1 spec used ε undefined — critic's catch), on a fixed grid, under (A2′-corrected) and (A3) dual nondegeneracy — now correctly stated as a **named assumption checkable numerically** (λ_min of the reduced dual Hessian), *not* attributed to Chen–Conforti–Ren–Wang, whose arXiv:2407.14186 proves one-period duality/no-gap/Sinkhorn-convergence but does not state (A3) (verified over-attribution, fixed) — the bridge map is C¹ by the implicit function theorem and a fixed-grid, local certificate follows. It is a **fixed-grid statement**: constants are not uniform in the mesh, and the continuum fixed-ε statement is flagged open (it would be the martingale analogue of entropic-OT stability à la Ghosal–Nutz–Bernton, unproven with the martingale constraint). The "instability is an ε→0 phenomenon" narrative survives only in this precise, declared-temperature form.

---

## T-D. Smoothed multi-asset program — DOWNGRADED CORRECTLY: two proven lemmas, one scoped transfer, one labeled conjecture

**What held.** (P1) Convex order is preserved by common Gaussian smoothing — verified, with the integrability convention made explicit. (P2) Finite-maturity discrete-time feasibility in any dimension is Strassen (1965) — verified; the precise d ≥ 2 statement is that *Markov/mimicking-diffusion* existence (Kellerer-type) fails unsmoothed and is restored by Gaussian regularization (Pammer–Robinson–Schachermayer, arXiv:2210.13847, AAP 2025), while bare martingale existence never fails — the v1.1 README's framing overclaimed and is corrected.

**What broke.** (P3)'s adapted-Wasserstein bound AW₁(X, X+G) ≤ Σσ_k√d is **false**: two agents independently built information-masking counterexamples (a tiny time-0 signal δ ≪ σ determines the next period's volatility; the noise masks it; a 1-Lipschitz *optimal-stopping* value drops by Θ(1) while the claimed bound is O(σ)). The synchronous coupling is causal but not bicausal — independent noise destroys information, and X is not adaptedly recoverable from X + G. (My inline audit had flagged exactly this risk; the referees turned it into theorems-with-counterexamples.)

**Corrected P3.** X̃ = X + G is a martingale calibrated to the smoothed surface (proof completed with piecewise-constant interpolation of G and the two-filtration tower argument), and the transfer
|E F(X) − E F(X̃)| ≤ L·Σ_t E|G_t|
holds for **static path-Lipschitz payoffs** via the trivial coupling — no adapted machinery, hence unbreakable. The transfer for stopping/superhedging values is **explicitly excluded** (the counterexample above is now part of the statement's scope note); an AW-level transfer is available only under a kernel-Lipschitz assumption on the model class via AW ≤ C√W₁ (Blanchet–Larsson–Park–Wiesel, arXiv:2407.21492), stated as a conditional corollary.

**Conjecture C1 (sharpened, still labeled, still used by nothing).** Stability of the calibrated-martingale correspondence under maturity-wise Gaussian convolution of the marginals in d ≥ 2, uniformly in the entropic temperature. Falsification test unchanged (smooth the Brückerhoff–Juillet line-segment marginals). Competing regularization routes now cited: the EJP 2024 extension of martingale transport restoring stability, and bi-martingale OT (arXiv:2510.27451).

---

## T-E. Tower-property (Bass) architecture — COMPLETED: sound core, missing components added, novelty narrowed honestly

**What held.** The flagship mechanism is **verified correct**: X_t = E[g_θ(W_1) | W_t] = (heat-kernel smoothing of g_θ)(W_t) is an exact martingale — in the Brownian filtration *and its own* — for any integrable g_θ; martingality is architecture-level. The audit-not-enforcement role of the adversarial defect is verified with the corrected rate.

**What broke, and the completed construction.** (i) With W started at a point, X_0 is constant — the architecture as written could only fit Dirac initial laws; the verified counterexample (two-point μ_0, Gaussian-smoothed μ_1) shows real-analytic heat smoothing can never produce an atomic X_0. The completed architecture carries the **Bass measure as a learnable initial law α_θ** (W_0 ~ α_θ), which is precisely the fixed-point unknown in the Bass literature; with it, expressivity is exactly the existence-and-uniqueness theorem of arXiv:2306.11019 (irreducible convex-ordered pairs; attribution corrected to that paper, not 1708.04869). (ii) Naive multi-period chaining is *not* a martingale (verified: cubic-map junction defect); the completed chaining uses the **inverse-heat junction link** — the Conze–Henry-Labordère mechanism — making junction martingality architectural, with per-interval hypotheses (atomic-or-density marginals, pairwise convex order, componentwise irreducibility) stated. (iii) The audit bound is corrected to expectation form E[defect] ≤ C·T·√d·Lip(g_θ)·N^{−1/2} **plus junction-inversion tolerances** (driven to machine precision in d = 1, solver-tolerance in d > 1), and the exact-fit claim is scoped to marginals with densities — for atomic grid marginals Lip(g_θ) blows up as the fit sharpens, so the architecture's natural habitat is the **smoothed surface of T-D**, a genuinely pleasing consonance between the two developments. (iv) The basis must be a Gaussian martingale with independent increments — an fBM basis breaks own-filtration martingality (verified counterexample).

**Novelty, narrowed by the literature pass.** The construction-level idea is Conze–Henry-Labordère (SSRN 3853085, 2021, non-neural, including multi-marginal chaining); neural Bass solvers exist (neural martingale-Sinkhorn in the arXiv:2310.13797 ecosystem; **LightSBB-M**, arXiv:2601.19312; **SBBTS**, arXiv:2604.07159 — both found by the verifier, both 2026). The defensible delta: **ICNN terminal map + tower-property exactness as an ML architecture principle + the defect demoted to a quantified audit feeding the T-C certificate**, positioned against all of the above. Its trained limit is the Bass/MBB martingale (uniqueness per 2306.11019) — stated openly as the second selection principle, certified by the interval form of T-C.

---

## T-F. Statistical certificate — CORRECTED: all missing terms added, observation models split

**What broke.** Three real errors. (i) Inherited the single-P* target (fixed by T-C's interval form; verified counterexample: two zero-defect calibrated martingales priced F = x₁x₂ at 1 vs 0). (ii) **Missing approximation term**: the neural class ℋ *under*-estimates the true defect, and no Rademacher term can close that gap (the Arora-et-al. neural-net-distance limitation, with a verified sign-pattern counterexample); the corrected bound carries an explicit ε_approx(ℋ_n) term under a stated smoothness assumption on conditional-mean fields (or a Markovian restriction), balanced against Rad_n(ℋ_n) via a sieve. (iii) **Sampling-model conflation**: the defect is an expectation under the *model* — it must be Monte-Carlo estimated with m fresh samples from P̂ (a compute-side budget, separate from the data size n); and "n surface observations" is an ill-posed inverse problem (Breeden–Litzenberger differentiation of noisy quotes), not i.i.d. sampling — the corrected statement splits the two observation models and flags the quote-noise branch as assumption-laden.

**Corrected statement (grid regime, i.i.d. branch).** With probability 1 − δ (failure split δ/(K+1) across the K marginal events and the defect event):
dist( E_P̂F, [V_min, V_max] ) ≤ L·T·𝔡̂ + L·ω( ΣŴ₁ + T𝔡̂ ) + L·C·[ ε_approx(ℋ_m) + 2Rad_m(𝒢) + T·diam·√(2log(2(K+1)/δ)/m) + Σ_k r_k(n) ],
with 𝒢 the gain class (vector-contraction to ℋ), r_k(n) = diam·√(|grid|/n) on a fixed grid (or Fournier–Guillin n^{−1/d} rates off-grid; Goldfeld–Greenewald smoothed rates *only* with the bridging step to T-D's smoothing stated as such — the vanilla claim "smoothing improves r(n,d)" was a verified smoothing-mismatch over-claim and is downgraded to a motivated conjecture). All constants C = C(T, K, diam, regime).

**Positioning, updated.** Must engage: Obłój–Wiesel (arXiv:1807.04211, Ann. Statist.) — statistical estimation of robust superhedging prices, including the documented failure of the naive plugin (a warning this certificate inherits and addresses via the interval target and explicit error budget) — and Blanchet–Wiesel–Zhang–Zhang (arXiv:2401.12197), whose one-period test is the closest existing statistical arbitrage certificate. No published work assembles the multi-period defect + marginal estimation + interval-stability composition; that composition, with every error term now present, is the contribution.

---

## Verification ledger v2.0

| Item | v2 claim | Hostile-referee outcome | Final status |
|---|---|---|---|
| L-R | Marginal restoration lemma | demanded by critic (was silently assumed ×3) | PROVABLE, standalone |
| T-A | 𝔡 ≃ AW₁-distance to ℳ (ℝᵈ), constants (2, T), 2 sharp | grid version refuted (unbounded ratio); ℝᵈ version verified line-by-line | **PROVEN-AS-STATED** |
| T-B | Inexact cyclic I-projections: budget bound to feasible limit; P* recovered under dual-inexactness/Dykstra | both original conclusions refuted by numerical counterexamples; weakened form's route verified | PROVABLE (weakened, correct) |
| T-C | Interval certificate, nonlinear modulus ω, d=1; fixed-grid declared-temperature d≥2 under (A2′),(A3) | single-P* form refuted (zero-RHS counterexample); interval route's key steps verified | PROVABLE d=1; ASSUMPTION-EXPLICIT d≥2 |
| T-D | P1, P2 proven; P3 static-payoff transfer; AW transfer only under kernel-Lipschitz | AW form of P3 refuted (information-masking); static form unbreakable (trivial coupling) | PROVEN (P1, P2, P3-static); CONJECTURE (C1) |
| T-E | Bass architecture with learnable α_θ, inverse-heat junctions, audited defect | core verified; three missing components exhibited by counterexample and added | PROVABLE (construction complete) |
| T-F | Finite-sample interval certificate, all error terms explicit, split observation models | three errors found (target, approximation gap, sampling model); all corrected | PROVABLE (i.i.d. branch); quote branch ASSUMPTION-LADEN |

## What changed vs. what was improved (the honest ledger)

**Errors found and fixed (would have failed when tested):**
1. T-A's grid-supported form — false with unbounded ratio (triangle counterexample). → ℝᵈ restatement; grid warning kept as a result.
2. T-B's convergence-to-P* under summable errors — false (limit-identification counterexample); constant-ε neighborhood — false (tangential drift). → budget form + dual-inexactness recovery.
3. T-C/T-F's single-P* certificate — structurally false (non-uniqueness of calibrated martingales; zero-RHS counterexamples). → interval certificate.
4. T-C's linear stability constant — unsupported by the cited theorems. → explicit nonlinear modulus ω.
5. T-C's (A3) attribution to arXiv:2407.14186 — over-attribution. → named checkable assumption.
6. T-D's AW smoothing bound — false (information-masking counterexamples). → static-payoff transfer + conditional AW corollary.
7. T-E without the Bass measure — cannot fit non-Dirac μ_0 (verified counterexample); naive chaining not a martingale. → α_θ + inverse-heat junctions.
8. T-F's missing ℋ-approximation term and sampling-model conflation. → ε_approx + split observation models.
9. (A2′) unsatisfiable on a time-constant grid; ε-temperature undefined; ℳ-symbol ambiguity; McShane extension bookkeeping; cyclic K+1 scheme. → global setting corrected.
10. v1.1 README's regularized-Kellerer framing — overclaimed what fails in d ≥ 2. → scoped to Markov/mimicking existence.

**Improved for novelty (correct before and after; now sharper or better positioned):**
- T-A's two-sided equivalence with sharp constant (upgraded from a one-directional lemma) — the paper's cleanest new theorem.
- T-B repositioned from "convergence proof" (now largely in arXiv:2502.20264) to the open inexact/budget theory — including publishing the two counterexamples as results.
- T-C's interval form is *stronger* science than the broken bridge form: it is the certificate a regulator can actually consume, and it unifies both training tracks.
- T-D's consonance: the Bass architecture's natural habitat is the smoothed surface — two developments that independently survived verification and then fit together.
- T-E narrowed against Conze–Henry-Labordère, LightSBB-M, SBBTS — with the ICNN-tower-audit delta stated precisely.
- New citations the verifiers surfaced, all now in the repo: 2502.20264, 2407.21492, 2601.19312, 2604.07159, 2204.12250, 2401.05209, 2603.27712, 1807.04211, 2311.14567.
