---
title: "RACC: Regret-Aware Confidence Calibration for Consistent Masked Discrete Diffusion Decoding"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# RACC: Regret-Aware Confidence Calibration for Consistent Masked Discrete Diffusion Decoding

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: consistent masked-DLM decoding without extra forward passes, evaluated on generation and reasoning tasks (Abstract and experiments).
- Mechanism verified: position-wise exponential momentum tracks belief; confidence collapse creates a regret penalty that modifies scores used in global top-k commitment (Sections 3.2-3.3).
- Scope/insight: temporal feedback can reveal contextual conflict after commitment, but it calibrates future competition rather than using the exact proposed cardinality projection (Introduction and Section 3).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: Findings of ACL 2026.

## Local Sources

- [PDF](paper-pdf/racc-2026.pdf)
- [Extracted text](paper-pdf/racc-2026.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
