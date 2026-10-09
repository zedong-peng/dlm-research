---
title: "MaskGIT: Masked Generative Image Transformer"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# MaskGIT: Masked Generative Image Transformer

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: finish discrete-token generation in constant `T` iterations, with experiments centered on `T=8...12` (Sections 3.2, 3.3, and 4.4).
- Mechanism verified: predict all masked tokens, use probability as confidence, compute `n=floor(gamma(t/T)N)`, and mask the `n` lowest-confidence positions (Section 3.2).
- Scope/insight: a monotone decreasing schedule guarantees convergence; cosine scheduling provides the best image-generation trade-off (Sections 3.3 and 4.4).
- Refined axes: problem match, mechanism match, insight match, domain differs.
- Venue: CVPR 2022.

## Local Sources

- [PDF](paper-pdf/maskgit-2022.pdf)
- [Extracted text](paper-pdf/maskgit-2022.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
