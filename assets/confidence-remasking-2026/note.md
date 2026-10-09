---
title: "Re-evaluating Confidence Remasking in Masked Diffusion Language Models"
domain: research
area: dlm
type: paper
status: stable
updated: 2026-10-09
tags: [diffusion-language-models, migrated-reading-record]
---

# Re-evaluating Confidence Remasking in Masked Diffusion Language Models

## Reading Boundary

This note preserves the existing 2026-07-15 section-level full-text audit in [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/step5]]. Migration did not reread or reverify the paper. The cited sections and audit scope below define the recorded coverage.

## Preserved Evidence

- Problem verified: whether remasking improves LLaDA/Dream beyond Fast-dLLM at matched latency across block sizes and decoding temperatures (Sections 3-4).
- Mechanism verified: WINO scores committed tokens through shadow positions, remasks those below a threshold, and limits remasks to fewer than newly unmasked tokens to guarantee net progress (Section 2.3, Algorithm 1).
- Scope/insight: under standard short blocks, greedy remasking adds little; it helps more when stochastic decoding introduces errors and only when the model can propose alternatives (Abstract, Sections 3-5).
- Refined axes: problem match, mechanism partial, insight match, domain match.
- Venue: arXiv preprint.

## Local Sources

- [PDF](paper-pdf/confidence-remasking-2026.pdf)
- [Extracted text](paper-pdf/confidence-remasking-2026.txt)
- [Citation](citation.bib)

Return to [[research/dlm/reference/papers/index]].
