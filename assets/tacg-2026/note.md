---
title: "TACG: Trajectory-Aware Commit Gating for Diffusion Language Model Decoding"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# TACG: Trajectory-Aware Commit Gating for Diffusion Language Model Decoding

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: training-free commitment readiness under comparable decoding compute, reporting steps and tokens per forward (Abstract and experiments).
- Mechanism verified: a history-logit support score, persistence requirement, confidence escape, confidence floor, and capped promotion budget decide commits (Sections 1 and 3).
- Scope/insight: snapshot confidence conflates probability with readiness; trajectory stability supplies a distinct gate without extra forward passes (Introduction and method).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## Local Sources

- [PDF](paper-pdf/tacg-2026.pdf)
- [Extracted text](paper-pdf/tacg-2026.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
