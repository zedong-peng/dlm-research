---
title: Deadline Paths Scoop Check - Step 4
domain: research
area: dlm
type: note
status: stable
updated: 2026-07-16
tags: [novelty-audit, candidate-selection]
---

# Step 4 - High-Potential Candidates

Timestamp: 2026-07-15 (Asia/Shanghai)

Seven candidates were selected for full-text inspection:

1. **Mask-Predict:** canonical fixed-iteration text ancestor; its linear remask schedule is nearly the proposed `K_b` path.
2. **MaskGIT:** canonical progress-indexed keep-most-confident projection, in another discrete-token modality.
3. **Saber:** closest training-free DLM system combining adaptive commitment and backtracking.
4. **Re-evaluating Confidence Remasking:** verifies WINO's progress-guaranteed remasking and tests whether revision adds value at matched latency.
5. **RACC:** directly uses committed-token confidence trajectories to alter global top-k competition.
6. **TACG:** a very recent budget-capped, trajectory-aware commitment gate in the same model class.
7. **NAVIRA:** explicitly evaluates full-set deterministic top-k remasking and scheduled variants across forward-pass budgets.

Fast-dLLM, Plan for Speed, and Deferred Commitment remain important baselines but were not among the seven deepest collision threats: they either lack revision or use a materially different learned/windowed control surface.
