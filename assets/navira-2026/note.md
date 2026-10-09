---
title: "NAVIRA: Decoupled Stochastic Remasking for Masked Diffusion Language Models"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# NAVIRA: Decoupled Stochastic Remasking for Masked Diffusion Language Models

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: compare remasking policies across fixed forward-pass budgets using perplexity, entropy, and LLM evaluation (Sections 4-5).
- Mechanism verified: score all revealed positions, form a deterministic top-k or stochastic remask set, unmask another selected set, and optionally decouple scoring from regeneration with a second pass (Sections 4.1-4.3).
- Scope/insight: full-set remasking revises decisions from prior iterations; fixed deterministic top-k can collapse entropy, motivating progress-dependent stochastic scheduling (Related Work and Sections 4-5).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## Local Sources

- [PDF](paper-pdf/navira-2026.pdf)
- [Extracted text](paper-pdf/navira-2026.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
