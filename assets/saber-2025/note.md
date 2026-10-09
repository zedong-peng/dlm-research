---
title: "Saber: An Efficient Sampling with Adaptive Acceleration and Backtracking Enhanced Remasking for Diffusion Language Model"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# Saber: An Efficient Sampling with Adaptive Acceleration and Backtracking Enhanced Remasking for Diffusion Language Model

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: training-free DLM decoding on reasoning and code, measuring Pass@1, denoising steps, and wall-clock latency (Abstract and Section 6).
- Mechanism verified: AADU unmasking uses a historical dynamic threshold; BERM separately re-evaluates committed-token confidence drops and remasks the largest `mu_t` drops (Sections 4.2-4.3, Algorithm 1).
- Scope/insight: confidence changes with new context and early irreversible commitments need backtracking; the theory assumes calibrated confidence with slack (Sections 3 and 5, Appendix assumptions).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## Local Sources

- [PDF](paper-pdf/saber-2025.pdf)
- [Extracted text](paper-pdf/saber-2025.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
