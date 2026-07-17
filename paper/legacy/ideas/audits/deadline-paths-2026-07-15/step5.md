---
title: Deadline Paths Scoop Check - Step 5
domain: research
area: dlm
type: comparison
status: stable
updated: 2026-07-16
tags: [novelty-audit, full-text, prior-art]
---

# Step 5 - Full-Paper Deep Dive

Timestamp: 2026-07-15 (Asia/Shanghai)

All seven PDFs were read from local extracted text under [[research/dlm/reference/papers/index]]. Section references below are the three-pass evidence used for each judgment.

## Mask-Predict

- Problem verified: conditional machine translation with a user-selected constant iteration count `T`; quality/speed is reported for `T=4...10` (Sections 3.1 and 4).
- Mechanism verified: at each iteration, mask the `n` lowest-probability positions; set `n=N(T-t)/T`; repredict in parallel (Section 3.1, Figure 1).
- Scope/insight: previously generated tokens can be selected again in later iterations; the growing observed subset collapses inconsistent parallel modes (Sections 3.2 and 5.1).
- Refined axes: problem partial, mechanism match, insight match, domain match.
- Venue: EMNLP 2019.

## MaskGIT

- Problem verified: finish discrete-token generation in constant `T` iterations, with experiments centered on `T=8...12` (Sections 3.2, 3.3, and 4.4).
- Mechanism verified: predict all masked tokens, use probability as confidence, compute `n=floor(gamma(t/T)N)`, and mask the `n` lowest-confidence positions (Section 3.2).
- Scope/insight: a monotone decreasing schedule guarantees convergence; cosine scheduling provides the best image-generation trade-off (Sections 3.3 and 4.4).
- Refined axes: problem match, mechanism match, insight match, domain differs.
- Venue: CVPR 2022.

## Saber

- Problem verified: training-free DLM decoding on reasoning and code, measuring Pass@1, denoising steps, and wall-clock latency (Abstract and Section 6).
- Mechanism verified: AADU unmasking uses a historical dynamic threshold; BERM separately re-evaluates committed-token confidence drops and remasks the largest `mu_t` drops (Sections 4.2-4.3, Algorithm 1).
- Scope/insight: confidence changes with new context and early irreversible commitments need backtracking; the theory assumes calibrated confidence with slack (Sections 3 and 5, Appendix assumptions).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## Re-evaluating Confidence Remasking / WINO

- Problem verified: whether remasking improves LLaDA/Dream beyond Fast-dLLM at matched latency across block sizes and decoding temperatures (Sections 3-4).
- Mechanism verified: WINO scores committed tokens through shadow positions, remasks those below a threshold, and limits remasks to fewer than newly unmasked tokens to guarantee net progress (Section 2.3, Algorithm 1).
- Scope/insight: under standard short blocks, greedy remasking adds little; it helps more when stochastic decoding introduces errors and only when the model can propose alternatives (Abstract, Sections 3-5).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## RACC

- Problem verified: consistent masked-DLM decoding without extra forward passes, evaluated on generation and reasoning tasks (Abstract and experiments).
- Mechanism verified: position-wise exponential momentum tracks belief; confidence collapse creates a regret penalty that modifies scores used in global top-k commitment (Sections 3.2-3.3).
- Scope/insight: temporal feedback can reveal contextual conflict after commitment, but it calibrates future competition rather than using the exact proposed cardinality projection (Introduction and Section 3).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: Findings of ACL 2026.

## TACG

- Problem verified: training-free commitment readiness under comparable decoding compute, reporting steps and tokens per forward (Abstract and experiments).
- Mechanism verified: a history-logit support score, persistence requirement, confidence escape, confidence floor, and capped promotion budget decide commits (Sections 1 and 3).
- Scope/insight: snapshot confidence conflates probability with readiness; trajectory stability supplies a distinct gate without extra forward passes (Introduction and method).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## NAVIRA

- Problem verified: compare remasking policies across fixed forward-pass budgets using perplexity, entropy, and LLM evaluation (Sections 4-5).
- Mechanism verified: score all revealed positions, form a deterministic top-k or stochastic remask set, unmask another selected set, and optionally decouple scoring from regeneration with a second pass (Sections 4.1-4.3).
- Scope/insight: full-set remasking revises decisions from prior iterations; fixed deterministic top-k can collapse entropy, motivating progress-dependent stochastic scheduling (Related Work and Sections 4-5).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

The body-level evidence downgrades the apparent exact collision with Saber, RACC, TACG, and NAVIRA from full to partial mechanism matches. It does not rescue the proposal: Mask-Predict and MaskGIT already supply the fixed-horizon confidence/cardinality skeleton, while recent DLM papers supply cross-iteration re-evaluation and remasking.
