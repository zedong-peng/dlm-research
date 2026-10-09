---
title: "Mask-Predict: Parallel Decoding of Conditional Masked Language Models"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# Mask-Predict: Parallel Decoding of Conditional Masked Language Models

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: conditional machine translation with a user-selected constant iteration count `T`; quality/speed is reported for `T=4...10` (Sections 3.1 and 4).
- Mechanism verified: at each iteration, mask the `n` lowest-probability positions; set `n=N(T-t)/T`; repredict in parallel (Section 3.1, Figure 1).
- Scope/insight: previously generated tokens can be selected again in later iterations; the growing observed subset collapses inconsistent parallel modes (Sections 3.2 and 5.1).
- Refined axes: problem partial, mechanism match, insight match, domain match.
- Venue: EMNLP 2019.

## Local Sources

- [PDF](paper-pdf/mask-predict-2019.pdf)
- [Extracted text](paper-pdf/mask-predict-2019.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
