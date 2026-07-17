---
title: DLM Local Paper Materials
domain: research
area: dlm
type: source
status: active
updated: 2026-07-16
tags: [papers, pdf, full-text, provenance]
---

# DLM Local Paper Materials

Each downloaded paper has the original PDF and a `pdftotext -layout` extraction with the same slug. These files support section-level comparison and can be regenerated from the canonical arXiv URL.

| Slug | Paper | Canonical source | Why retained |
|---|---|---|---|
| `acceleration-survey-2026` | Accelerating Masked Diffusion Large Language Models | [arXiv:2607.12829](https://arxiv.org/abs/2607.12829) | Current inference taxonomy and latency accounting |
| `discrete-diffusion-survey-2025` | Discrete Diffusion in Large Language and Multimodal Models | [arXiv:2506.13759](https://arxiv.org/abs/2506.13759) | Broad field taxonomy and bibliography |
| `mdlm-2024` | Simple and Effective Masked Diffusion Language Models | [arXiv:2406.07524](https://arxiv.org/abs/2406.07524) | Modern masked-DLM mathematical baseline |
| `flow-matching-2023` | Flow Matching for Generative Modeling | [arXiv:2210.02747](https://arxiv.org/abs/2210.02747) | Continuous probability-path and vector-field foundation |
| `discrete-flow-matching-2024` | Discrete Flow Matching | [arXiv:2407.15595](https://arxiv.org/abs/2407.15595) | Categorical probability paths, velocities, schedulers, and correctors |
| `dirichlet-flow-matching-2024` | Dirichlet Flow Matching | [arXiv:2402.05841](https://arxiv.org/abs/2402.05841) | Simplex flow geometry for discrete sequence design |
| `fisher-flow-matching-2024` | Fisher Flow Matching | [arXiv:2405.14664](https://arxiv.org/abs/2405.14664) | Fisher-Rao geometry for categorical distributions |
| `general-discrete-paths-2024` | Flow Matching with General Discrete Paths | [arXiv:2412.03487](https://arxiv.org/abs/2412.03487) | Kinetic-optimal arbitrary path design with text experiments |
| `diffuseq-2023` | DiffuSeq | [arXiv:2210.08933](https://arxiv.org/abs/2210.08933) | Early conditional continuous text diffusion branch |
| `ssd-lm-2023` | SSD-LM | [arXiv:2210.17432](https://arxiv.org/abs/2210.17432) | Semi-AR simplex diffusion and modular control branch |
| `eb-sampler-2025` | Accelerated Sampling via Entropy Bounded Unmasking | [arXiv:2505.24857](https://arxiv.org/abs/2505.24857) | Error decomposition and adaptive selection threat |
| `plan-for-speed-2025` | Plan for Speed | [arXiv:2506.19037](https://arxiv.org/abs/2506.19037) | Structural scheduling threat |
| `saber-2025` | Saber | [arXiv:2510.18165](https://arxiv.org/abs/2510.18165) | Adaptive allocation and rollback threat |
| `learning-unmasking-policies-2025` | Learning Unmasking Policies | [arXiv:2512.09106](https://arxiv.org/abs/2512.09106) | MDP/RL formulation threat |
| `medal-mcts-2025` | Diffusion Language Model Inference with MCTS | [arXiv:2512.12168](https://arxiv.org/abs/2512.12168) | Tree-search threat |
| `lookahead-unmasking-2025` | Lookahead Unmasking | [arXiv:2511.05563](https://arxiv.org/abs/2511.05563) | Multi-path lookahead threat |
| `klass-2025` | KLASS | [arXiv:2511.05664](https://arxiv.org/abs/2511.05664) | Temporal-stability signal threat |
| `adas-2026` | Attention-Discounted Adaptive Sampler | [arXiv:2606.10829](https://arxiv.org/abs/2606.10829) | Interaction-aware subset-selection threat |
| `mask-predict-2019` | Mask-Predict | [arXiv:1904.09324](https://arxiv.org/abs/1904.09324) | Fixed-iteration confidence-remasking ancestor in text |
| `maskgit-2022` | MaskGIT | [arXiv:2202.04200](https://arxiv.org/abs/2202.04200) | Progress-indexed keep/remask ancestor in discrete-token generation |
| `deferred-commitment-2026` | Deferred Commitment Decoding | [arXiv:2601.02076](https://arxiv.org/abs/2601.02076) | Confidence-gated commitment and dynamic-window threat |
| `confidence-remasking-2026` | Re-evaluating Confidence Remasking | [arXiv:2606.12232](https://arxiv.org/abs/2606.12232) | WINO mechanism, progress guarantee, and matched-baseline evidence |
| `racc-2026` | RACC | [Findings of ACL 2026](https://aclanthology.org/2026.findings-acl.1138/) | Confidence-trajectory feedback and global top-k threat |
| `tacg-2026` | TACG | [arXiv:2607.03236](https://arxiv.org/abs/2607.03236) | Trajectory-aware, budget-capped commitment threat |
| `navira-2026` | NAVIRA | [arXiv:2606.06031](https://arxiv.org/abs/2606.06031) | Full-set top-k and scheduled stochastic remasking threat |

The downloader initially lacked an executable bit, so it was invoked through `bash`; its own PDF validation and extraction fallback pipeline still ran. All 25 retained papers have a validated PDF and extracted text. The initial MaskGIT download had a malformed cross-reference table and was replaced with the official CVPR Open Access PDF before extraction.

Do not edit extracted `.txt` files by hand. Durable synthesis belongs in [[research/dlm/reference/landscape]], [[research/dlm/reference/optimization-bridge]], and [[research/dlm/reference/reading-list]].
