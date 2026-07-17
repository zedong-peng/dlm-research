---
title: Deadline Paths Scoop Check - Step 3
domain: research
area: dlm
type: comparison
status: stable
updated: 2026-07-16
tags: [novelty-audit, abstract-triage, decoding]
---

# Step 3 - Abstract-Level Triage

Timestamp: 2026-07-15 (Asia/Shanghai)

## Structured Papers

### Mask-Predict: Parallel Decoding of Conditional Masked Language Models

- Date: 2019-04
- Problem framing: Fast non-autoregressive conditional text generation in a predetermined number of refinement iterations.
- Core mechanism: Repeatedly mask the lowest-confidence subset, with its size decreasing linearly with iteration, and predict the masked tokens in parallel.
- Key insight: Conditioning later predictions on a growing reliable subset resolves inconsistent parallel predictions.
- Application domain: Machine translation with conditional masked language models.
- Overlap score: 3/4
- Source: https://arxiv.org/abs/1904.09324 (model recall, live verified)

### MaskGIT: Masked Generative Image Transformer

- Date: 2022-02
- Problem framing: Generate a complete discrete-token object in a fixed, small number of parallel refinement steps.
- Core mechanism: Predict every masked token, retain the most confident predictions, and remask the remainder according to a progress-indexed mask schedule.
- Key insight: A decreasing mask ratio converts confidence into a coarse-to-fine committed-set path.
- Application domain: Discrete image-token generation and editing.
- Overlap score: 3/4
- Source: https://arxiv.org/abs/2202.04200 (model recall, live verified)

### Fast-dLLM: Training-Free Acceleration of Diffusion LLM by Enabling KV Cache and Parallel Decoding

- Date: 2025-05
- Problem framing: Reduce masked-DLM latency while retaining generation quality.
- Core mechanism: Confidence-aware parallel decoding plus cache reuse compatible with changing masks.
- Key insight: Many tokens can be committed together when confidence is high, and caching must follow the evolving dependency pattern.
- Application domain: Open diffusion LLM inference.
- Overlap score: 2/4
- Source: https://arxiv.org/abs/2505.22618

### Plan for Speed: Learning Structured Latent Variables for Efficient Diffusion Language Models

- Date: 2025-06
- Problem framing: Learn parallel decoding schedules that trade denoiser steps against text quality.
- Core mechanism: Structured latent planning of token update order rather than a fixed hand-written schedule.
- Key insight: Update order is a learnable dependency structure, not just a scalar noise schedule.
- Application domain: Diffusion language generation.
- Overlap score: 2/4
- Source: https://arxiv.org/abs/2506.19037

### Saber: Adaptive Acceleration and Backtracking Enhanced Remasking for Diffusion Language Models

- Date: 2025-10
- Problem framing: Improve both speed and output quality in training-free DLM sampling.
- Core mechanism: A dynamic confidence threshold controls unmasking, while a separate rollback quota remasks committed tokens whose confidence drops.
- Key insight: Confidence evolves as context grows, so aggressive commitment requires backtracking.
- Application domain: Diffusion LLM reasoning and code generation.
- Overlap score: 4/4
- Source: https://arxiv.org/abs/2510.18165

### Deferred Commitment Decoding for Diffusion Language Models

- Date: 2026-01
- Problem framing: Avoid forcing uncertain tokens at fixed semi-autoregressive block boundaries.
- Core mechanism: A sliding eligibility window commits tokens only above a certainty threshold or among the best candidates.
- Key insight: Low-certainty boundary tokens need more nearby context before commitment.
- Application domain: Full-attention and semi-causal DLMs.
- Overlap score: 2/4
- Source: https://arxiv.org/abs/2601.02076

### Re-evaluating Confidence Remasking in Masked Diffusion Language Models

- Date: 2026-06
- Problem framing: Determine when training-free remasking adds value beyond adaptive unmasking at matched latency.
- Core mechanism: Re-evaluates WINO, which remasks low-confidence committed tokens and caps remasking below new commitments to guarantee progress.
- Key insight: Revision only helps if the model can propose a better alternative; remasking gains are highly regime-dependent.
- Application domain: LLaDA and Dream on math and code benchmarks.
- Overlap score: 4/4
- Source: https://arxiv.org/abs/2606.12232

### RACC: Regret-Aware Confidence Calibration for Consistent Masked Diffusion Language Models

- Date: 2026
- Problem framing: Correct inconsistent DLM commitments without extra forward passes.
- Core mechanism: Track confidence momentum, penalize abrupt confidence collapse, and feed the calibrated score into global top-k commitment.
- Key insight: Confidence trajectories expose contextual regret that snapshot confidence misses.
- Application domain: Masked diffusion language generation.
- Overlap score: 3/4
- Source: ACL Findings 2026 paper `2026.findings-acl.1138`

### TACG: Trajectory-Aware Commit Gating for Diffusion Language Models

- Date: 2026-07
- Problem framing: Decide when a masked-token proposal is stable enough to commit while reducing denoising steps.
- Core mechanism: Historical-logit support, short-term persistence, a confidence floor, and a capped extra-promotion budget gate commitments.
- Key insight: Current confidence is not commitment readiness; temporal stability is an additional signal.
- Application domain: Training-free masked-DLM decoding.
- Overlap score: 3/4
- Source: https://arxiv.org/abs/2607.03236

### NAVIRA: Decoupled Stochastic Remasking for Masked Diffusion Inference

- Date: 2026-06
- Problem framing: Improve the quality-diversity trade-off across fixed forward-pass budgets.
- Core mechanism: Score revealed tokens, remask a full-set top-k or sampled subset, and optionally use a separate forward pass for regeneration.
- Key insight: The remasking policy and its schedule determine correction trajectories and entropy collapse.
- Application domain: Masked diffusion text generation.
- Overlap score: 4/4
- Source: https://arxiv.org/abs/2606.06031
