---
title: Deadline Paths Scoop Check - Step 2
domain: research
area: dlm
type: note
status: stable
updated: 2026-07-16
tags: [novelty-audit, literature-search, provenance]
---

# Step 2 - Search and Deduplicate

Timestamp: 2026-07-15 (Asia/Shanghai)

Queries sent through the `paper-search` workflow:

1. Original problem: `masked diffusion language model hard deadline commit remask decoding`
2. Broad domain: `diffusion language model sampling schedule confidence remasking`
3. Method signature: `top confidence remask committed tokens fixed iteration mask schedule`

Search results were combined with the prior DLM survey corpus and deduplicated by normalized title. Search surfaced the recent DLM cluster but missed two canonical ancestors, so the required model-recall augmentation added and live-verified:

- Mask-Predict: Parallel Decoding of Conditional Masked Language Models (`arXiv:1904.09324`)
- MaskGIT: Masked Generative Image Transformer (`arXiv:2202.04200`, CVPR 2022)

The focused deduplicated set advanced to abstract triage contains ten papers: Mask-Predict, MaskGIT, Fast-dLLM, Plan for Speed, Saber, Deferred Commitment Decoding, Re-evaluating Confidence Remasking, RACC, TACG, and NAVIRA. The full wider search is recorded in [[research/dlm/reference/search-log]] and the 41-paper structured map in `paper/legacy/ideas/runs/budgeted-decoding/phase0/lit_results.json`.

Coverage caveat: OpenReview authentication was unavailable; OpenAlex failed in the `paper-search` Python 3.9 environment; Semantic Scholar intermittently rate-limited. These failures reduce exhaustiveness but do not affect the canonical collisions verified from full text.
