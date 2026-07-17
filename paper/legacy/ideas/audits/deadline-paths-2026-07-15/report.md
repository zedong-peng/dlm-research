---
title: Deadline Paths Novelty Audit
domain: research
area: dlm
type: synthesis
status: stable
updated: 2026-07-16
tags: [novelty-audit, masked-diffusion, decoding, optimization]
---

# Deadline Paths Novelty Audit

## Verdict

**Level 2 - High Overlap.** The exact cross-status projection was not found verbatim, but every load-bearing ingredient is established by close prior work. [[step6]] gives the per-paper level calculation.

## Delta

Unlike Mask-Predict and MaskGIT, which use progress-indexed confidence schedules but freeze retained tokens, the proposal re-scores retained and masked positions in one top-`K_b` projection for a pretrained masked DLM, permitting repair without another denoiser call.

This delta is too narrow to support a primary novelty claim because Saber, WINO, RACC, and NAVIRA already cover re-evaluation, remasking, confidence trajectories, and full-set correction.

## Decomposed Claim

- **Problem framing:** complete masked-DLM decoding under a hard call budget.
- **Core mechanism:** one budget-indexed top-`K_b` projection across masked and committed positions.
- **Key insight:** the remaining horizon should constrain commit and repair jointly.
- **Application domain:** training-free inference for masked diffusion language models.

## Structured Papers

The complete ten-paper abstract record is [[step3]]. The strongest collisions are:

- **Mask-Predict (2019):** fixed iterations; remask the lowest-confidence scheduled subset; masked text generation; 3/4 abstract overlap.
- **MaskGIT (2022):** fixed `T`; keep most confident and remask the rest by progress; image tokens; 3/4 overlap.
- **Saber (2025):** adaptive commitment plus backtracking remasking in DLMs; 4/4 abstract overlap.
- **Confidence Remasking/WINO (2026):** re-score committed tokens and cap remasks to guarantee progress; 4/4 overlap.
- **RACC (2026):** confidence-trajectory regret modifies global top-k commitment; 3/4 overlap.
- **TACG (2026):** trajectory-aware commitment with capped promotions; 3/4 overlap.
- **NAVIRA (2026):** full-set top-k remasking and schedules across forward budgets; 4/4 overlap.

## Comparison Result

The full field-aligned comparison is [[step6]]. All seven deep-dive candidates land at **Level 2 - High Overlap** after body-level inspection: three axes match and only one differs. Mask-Predict/MaskGIT differ in model generation or modality; recent DLM papers differ mainly in whether commitment and repair use one ranking or separate controls.

## Research Decision

Do not advance Deadline Paths as a headline method. Keep the fixed-cardinality projection as a diagnostic baseline for future work, because it provides a clean control against threshold, rollback-quota, and stochastic-remasking policies. Future ideas must change the information used to decide actions or expose a new correctness/latency guarantee, not only rearrange existing confidence operations.

## Audit Trail

[[step1]] | [[step2]] | [[step3]] | [[step4]] | [[step5]] | [[step6]] | [[step7]]
