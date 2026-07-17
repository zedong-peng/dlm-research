---
title: Deadline Paths Scoop Check - Step 6
domain: research
area: dlm
type: comparison
status: stable
updated: 2026-07-16
tags: [novelty-audit, comparison]
---

# Step 6 - Comparison Result

Timestamp: 2026-07-15 (Asia/Shanghai)

- **Proposed work**
  - Title: Deadline Paths for Training-Free Masked Diffusion Decoding
  - Date: 2026-07
  - Source: [[research/dlm/paper/legacy/ideas/index]] Round 2
  - Problem framing: Complete masked-DLM decoding under a hard denoiser-call budget.
  - Core mechanism: Re-score masked and committed positions together and project to a budget-indexed top-`K_b` committed set.
  - Key insight: Put remaining calls inside every commit/repair decision so revisions cannot break completion.
  - Application domain: Pretrained masked diffusion language models.

- **Mask-Predict**
  - Problem framing: partial; predetermined iteration budget rather than modern DLM latency constraints.
  - Core mechanism: match; progress-indexed lowest-confidence remasking defines the same fixed-cardinality path.
  - Key insight: match; confidence chooses the reliable conditioning subset while the schedule guarantees completion.
  - Application domain: match; parallel masked text generation.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

- **MaskGIT**
  - Problem framing: match; complete masked-token generation in fixed `T` refinements.
  - Core mechanism: match; keep the most confident and remask the rest to a scheduled cardinality.
  - Key insight: match; progress controls the committed-set size and convergence.
  - Application domain: differs; image tokens rather than language.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

- **Saber**
  - Problem framing: match.
  - Core mechanism: partial; separate adaptive threshold and rollback quota, not one projection.
  - Key insight: match; context-dependent confidence requires revision.
  - Application domain: match.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

- **Re-evaluating Confidence Remasking / WINO**
  - Problem framing: match.
  - Core mechanism: partial; shadow-score threshold remasking plus a net-progress cap.
  - Key insight: match; revisions must be constrained to preserve progress and tested against strong unmasking.
  - Application domain: match.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

- **RACC**
  - Problem framing: match.
  - Core mechanism: partial; trajectory-calibrated global top-k rather than cross-status fixed-cardinality projection.
  - Key insight: match; committed-token confidence changes expose regret.
  - Application domain: match.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

- **TACG**
  - Problem framing: match.
  - Core mechanism: partial; readiness gate and capped promotions without the same remask projection.
  - Key insight: match; commitment decisions need temporal evidence and resource control.
  - Application domain: match.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

- **NAVIRA**
  - Problem framing: match.
  - Core mechanism: partial; full-set top-k remasking and unmasking are separate sets, with optional extra scoring pass.
  - Key insight: match; progress and budget should control revision policy.
  - Application domain: match.
  - Result: 3 axes match -> **Level 2 - High Overlap**.

Worst-case rule: the minimum per-paper level is **Level 2 - High Overlap**.
