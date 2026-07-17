---
title: Deadline Paths Scoop Check - Step 1
domain: research
area: dlm
type: note
status: stable
updated: 2026-07-16
tags: [novelty-audit, masked-diffusion, decoding]
---

# Step 1 - Decompose the Novelty

Timestamp: 2026-07-15 (Asia/Shanghai)

- **Problem framing:** Under a fixed number of denoiser calls, decode a complete sequence from a pretrained masked diffusion language model while maximizing terminal task quality and accounting for both new commitments and revisions.
- **Core mechanism:** After every forward pass, score both masked and already committed positions under the current canvas, retain exactly a budget-indexed number `K_b` of highest-support positions as committed, and remask the rest. The same fixed-cardinality projection performs commitment and repair without an auxiliary model or extra forward pass.
- **Key insight:** Remaining calls should determine the feasible committed-set path, not merely act as an external stopping rule. A single cross-status ranking could prevent repair from consuming the calls needed for completion.
- **Application domain:** Training-free inference for masked diffusion language models, especially reasoning and code generation with LLaDA/MDLM-like models.

See [[report]] for the rolled-up audit.
