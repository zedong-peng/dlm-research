---
title: DLM Literature Search Log
domain: research
area: dlm
type: timeline
status: active
updated: 2026-07-16
tags: [literature-search, diffusion-language-models, provenance]
---

# DLM Literature Search Log

## 2026-07-16 Flow-Matching and Backbone Gap Audit

Focused `paper-search` runs covered all configured sources for four queries over 2021--2026:

| Query | Raw result | Decision |
|---|---:|---|
| `Flow Matching for Generative Modeling` | 40 rows | Added the ICLR foundation and separated path learning from numerical solving |
| `Discrete Flow Matching` | 30 rows | Added DFM and Fisher Flow as direct discrete alternatives |
| `Dirichlet Flow Matching discrete sequence` | 30 rows | Added Dirichlet/Fisher geometries and variable-length/edit-flow follow-ups to the map |
| `Mamba state space diffusion language model text generation` | 30 rows | Classified Mamba as a backbone; found MDLM's Mamba-based DNA experiment but no equally established general-text Mamba-DLM line |

Primary arXiv metadata and full texts then verified Flow Matching, DFM, Dirichlet Flow, Fisher Flow, General Discrete Paths, DiffuSeq, SSD-LM, Mamba, Mamba-2, and the image-only Dimba distinction. Seven missing PDFs and layout-preserving text extractions were retained locally. Full synthesis: [[research/dlm/reference/transport-and-backbones]].

Connector limitations were material: OpenAlex returned HTTP 504 on focused searches, OpenReview hit HTTP 429 or returned zero rows, DBLP had a proxy failure, and Crossref mixed in unrelated graph-matching papers. These errors are recorded rather than treated as evidence of absence.

## 2026-07-15 Search Rounds

The search used the `paper-search` skill across arXiv, Semantic Scholar, Crossref, DBLP, OpenAlex, and OpenReview, followed by the `idea-spark` connectors and targeted official-arXiv verification.

| Round | Query | Window | Main result |
|---|---|---:|---|
| 1 | `diffusion language models discrete diffusion text generation` | 2015-2026 | Found foundations plus MDLM/SEDD and current model families; 31 raw hits |
| 2 | `diffusion language model sampling decoding inference acceleration` | 2021-2026 | Found acceleration and inference-time scaling work; 20 raw hits, but many image/AR false positives |
| 3 | `masked diffusion language model adaptive decoding confidence remasking` | 2021-2026 | High-signal cluster: DUS, Saber, KLASS, LookUM, confidence schedules; 30 raw hits |
| 4 | `discrete diffusion decoding optimization dynamic programming optimal scheduling` | 2021-2026 | Mostly unrelated OR results; useful negative evidence about vocabulary, not novelty |
| 5 | `adaptive token unmasking schedule masked diffusion language model parallel decoding` | 2021-2026 | Found learned/structured selection, dependency-guided and cluster decoding; 20 raw hits |
| 6 | `diffusion probabilistic flow ODE solver adaptive step schedule optimization` | 2020-2026 | Continuous-solver transfer literature: DPM-Solver family, solver search, instance-aware grids; 28 raw hits |
| 7 | `survey diffusion language models discrete diffusion large language models` | 2023-2026 | Found the 2025 general survey and 2026 inference-efficiency survey; 31 raw hits |
| 8 | `diffusion language model revision remasking KV cache invalidation refresh` | 2024-2026 | 22 raw hits; surfaced dKV-Cache, Elastic-Cache, SPA-Cache, EntropyCache, and COVER as collisions for round 3 |

The structured `idea-spark` map used six more targeted queries and retained 41 deduplicated papers in `paper/legacy/ideas/runs/budgeted-decoding/phase0/lit_results.json`. Its human-readable evidence table is `phase0/lit_table.md`.

## Connector Coverage and Errors

- `idea-spark`: arXiv, OpenAlex, and Semantic Scholar available; OpenReview skipped because `OPENREVIEW_USER` and `OPENREVIEW_PASS` were missing. The run records `.connectors_degraded`.
- `paper-search` OpenAlex failed under the current Python 3.9 runtime with: `unsupported operand type(s) for |: 'type' and 'NoneType'`.
- Semantic Scholar intermittently rate-limited and later produced SSL/proxy failures. No blind retries were used to fill those missing source rows.
- The paper-search OpenReview source returned zero results. This should not be read as "no OpenReview papers"; the authenticated `idea-spark` connector was unavailable.
- Crossref had high false-positive rates and missing years for preprints.

Because of these limitations, this is a strong working map, not a proof of exhaustive recall.

## Cross-Source Summary

### Overview

The combined search spans 2015-2026 for foundations and 2021-2026 for the active inference frontier. The durable structured corpus contains 41 deduplicated recent papers, while the broader raw searches also supplied foundations, continuous-solver transfer work, and some high-noise false positives.

### Trends

- Before 2024, the center of gravity was model formulation and likelihood/training objectives.
- In 2025, open 7B-8B models shifted attention to inference: parallel schedules, caching, RL, guidance, and reasoning.
- In 2026, work is increasingly fine-grained: token interaction, remasking, response length, cluster/block granularity, causal support, and systems co-design.
- NeurIPS/ICLR and arXiv dominate model/sampler work; ACL-family venues increasingly cover text generation and code-specific decoding.

### Key Themes

1. **Foundations and parameterization:** D3PM, RDM, SEDD, and MDLM.
2. **Parallel update policy:** EB-Sampler, Plan for Speed, Fast-dLLM, progress-aware schedules, and KLASS.
3. **Correction/search:** Saber, RDD, LookUM, MEDAL, Particle Gibbs, and diffusion trees.
4. **Dependency-aware selection:** ADAS, DAPD, CLAD, and AXON.
5. **Architecture/systems:** block diffusion, caching, sparse attention, dInfer, and multi-scale decoding.
6. **Compute scaling and limits:** theoretical metric-dependent bounds, per-request allocation, and matched quality-latency evaluation.

### Keyword Frequency

Counts below are document frequencies over the 41-paper structured recent corpus, using title plus abstract.

| Keyword | Papers |
|---|---:|
| diffusion language | 23 |
| inference | 19 |
| parallel | 19 |
| decoding | 16 |
| attention | 7 |

### Citation Snapshot

The following counts were returned by Semantic Scholar or Crossref during this run. They are volatile and may merge preprint/publication records inconsistently.

| Rank | Accepted paper | Year | Citations observed |
|---:|---|---:|---:|
| 1 | DPM-Solver++ | 2025 journal version | 161 |
| 2 | DPM-Solver | 2022 | 108 |
| 3 | Energy-Based Diffusion Language Models | 2024 | 93 |
| 4 | KLASS | 2025 | 32 |
| 5 | A Cheaper and Better Diffusion Language Model with Soft-Masked Noise | 2023 | 14 |

| Rank | First author | Papers in this snapshot | Total citations observed |
|---:|---|---:|---:|
| 1 | Cheng Lu | 2 | 269 |
| 2 | Minkai Xu | 1 | 93 |
| 3 | Seo Hyun Kim | 1 | 32 |
| 4 | Jiaao Chen | 1 | 14 |
| 5 | Kun Zhou | 1 | 5 |

### Recommended Reading Path

1. D3PM, then MDLM: foundational discrete process and modern masked formulation.
2. The 2025 general survey: vocabulary and full model/inference landscape.
3. EB-Sampler and Plan for Speed: error-budget and structural-schedule views.
4. Saber, Learning Unmasking Policies, and MEDAL: revision, MDP, and search boundaries.
5. ADAS plus the 2026 acceleration survey: current interaction-aware frontier and honest latency accounting.

## Search Vocabulary Lesson

High-recall DLM inference searches should use mechanism vocabulary:

```text
masked diffusion unmasking policy
entropy bounded unmasking
confidence schedule remasking
dependency aware parallel decoding
diffusion language model MCTS
lookahead unmasking path selection
diffusion KV cache
compute budget adaptive denoising
```

Generic `dynamic programming` or `optimal scheduling` terms are too ambiguous across operations research, control, epidemic diffusion, and numerical PDEs.

## Full-Text Set

Downloaded and extracted locally under `reference/papers/` during the original pass; the PDF/text pairs were moved without modification to `assets/<slug>/paper-pdf/` on 2026-10-09. Current archive and reading-boundary links are in [[research/dlm/reference/papers/index]]. The historical set below is preserved:

- Accelerating Masked Diffusion Large Language Models: A Survey of Efficient Inference Techniques (2026)
- Discrete Diffusion in Large Language and Multimodal Models: A Survey (2025)
- Simple and Effective Masked Diffusion Language Models (MDLM, 2024)
- Plan for Speed (2025)
- Saber (2025)
- EB-Sampler (2025)
- Learning Unmasking Policies for Diffusion Language Models (2025)
- Diffusion Language Model Inference with Monte Carlo Tree Search / MEDAL (2025)
- Lookahead Unmasking (2025)
- KLASS (2025)
- Attention-Discounted Adaptive Sampler / ADAS (2026)
- Mask-Predict (2019)
- MaskGIT (2022)
- Deferred Commitment Decoding (2026)
- Re-evaluating Confidence Remasking (2026)
- RACC (2026)
- TACG (2026)
- NAVIRA (2026)

The `idea-spark` full-text cache separately fetched 13 of 15 selected recent papers, including FlashDLM, MEDAL, ADAS, dInfer, E2D2, and recent compute/length-adaptive work. The focused novelty audit is [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/report]].

## Freshness Rule

The acceleration survey itself was posted on 2026-07-14, one day before this research pass. Re-run all collision queries before submitting or publicly claiming novelty.
