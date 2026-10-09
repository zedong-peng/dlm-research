---
title: Transport Objectives and Backbone Choices for DLMs
domain: research
area: dlm
type: synthesis
status: active
updated: 2026-07-16
tags: [flow-matching, discrete-flow, mamba, state-space-models, diffusion-language-models]
---

# Transport Objectives and Backbone Choices for DLMs

## First-Principles Separation

The missing literature falls on two different axes. Flow matching changes the probability path and training target. Mamba changes the neural sequence backbone. Neither choice alone specifies the sampler.

| Layer | Mathematical object | Representative choices | Question for algorithm design |
|---|---|---|---|
| Probability path | (p_t) from noise/source to data | Gaussian diffusion, absorbing mask, arbitrary categorical path, OT path | What process is being approximated? |
| Training target | learned field or conditional | score, clean-token posterior, density ratio, continuous velocity, probability velocity/rates | What does one model evaluation return? |
| Backbone | map from corrupted state to prediction | bidirectional Transformer, Mamba/SSM, hybrid | What is the cost and dependency structure of one evaluation? |
| Sampler | numerical or discrete state update | ODE/SDE solver, CTMC simulation, unmask/remask policy, corrector | Which approximation or control action is changed? |
| System | execution state | cache, kernels, batching, memory | Does the algorithm reduce wall-clock cost? |

## Flow-Matching Line

| Work | Level | Durable contribution | DLM relevance |
|---|---|---|---|
| [Flow Matching for Generative Modeling](https://arxiv.org/abs/2210.02747) (ICLR 2023) | continuous transport | Simulation-free regression of a vector field for a chosen probability path; diffusion paths are a subset | Establishes that path design, training objective, and ODE solver are separate choices |
| [Discrete Flow Matching](https://arxiv.org/abs/2407.15595) (NeurIPS 2024) | categorical transport | General discrete probability paths, learned posteriors, probability velocities, correctors, and schedulers; includes text/code experiments up to 1.7B parameters | Directly belongs in the DLM foundation alongside D3PM, SEDD, and MDLM |
| [Dirichlet Flow Matching](https://arxiv.org/abs/2402.05841) (ICML 2024) | simplex geometry | Dirichlet-mixture paths and guidance for categorical sequences | Important alternative geometry, demonstrated on DNA rather than general text |
| [Fisher Flow Matching](https://arxiv.org/abs/2405.14664) (NeurIPS 2024) | statistical manifold | Fisher-Rao geometry and geodesic transport for categorical distributions | Shows that Euclidean simplex interpolation is not the only principled continuous relaxation |
| [Flow Matching with General Discrete Paths](https://arxiv.org/abs/2412.03487) (2024) | categorical path design | Decouples probability paths from probability velocities and optimizes kinetic energy | Makes corruption/path selection an explicit algorithmic surface; reports gains over masking on text |

The main research implication is precise: a better "diffusion solver" may actually be a better probability path, rate/velocity parameterization, time grid, CTMC simulator, or token controller. Those claims need different baselines.

## Earlier Text-Diffusion Branches

- [DiffuSeq](https://arxiv.org/abs/2210.08933) (ICLR 2023) is an early continuous diffusion model for conditional sequence-to-sequence generation.
- [SSD-LM](https://arxiv.org/abs/2210.17432) (ACL 2023) combines simplex diffusion with semi-autoregressive blocks and modular control.
- These works are historically important but expose different state and length interfaces from modern absorbing-mask DLMs.

## Mamba Verdict

[[research/linear-attention/assets/mamba-2023/note|Mamba]] and [[research/linear-attention/assets/mamba-2-2024/note|Mamba-2]] are sequence-model backbones. They change the complexity, memory state, and parallelism of a denoiser evaluation; they do not define a diffusion or flow process.

There is a real DLM connection: MDLM Section 5.2 fine-tunes a Mamba-based state-space backbone for biological sequences. This demonstrates compatibility between masked diffusion objectives and SSMs. It does not yet establish a large, general-text Mamba-DLM family comparable to Transformer-based LLaDA, Dream, or Block Diffusion.

The similarly named [Dimba](https://arxiv.org/abs/2406.01159) is a Transformer-Mamba text-to-image diffusion model. It is relevant architecture-transfer evidence but not a diffusion language model and is therefore excluded from the main text lineage.

## Discussion Checklist

When a proposed algorithm invokes flow matching or Mamba, ask:

1. Is the claim about the probability path, learned target, sampler, backbone, or kernel?
2. Is the state continuous, categorical, simplex-valued, or an absorbing-mask canvas?
3. Does the baseline use the same model evaluations and numerical/discrete time budget?
4. If the backbone changes, is the gain from asymptotic complexity, kernel quality, or fewer sampling steps?
5. Does the result hold on general text, or only images, DNA, graphs, or another modality?

## Search Provenance

Focused `paper-search` runs covered Semantic Scholar, OpenAlex, arXiv, OpenReview, Crossref, and DBLP for `Flow Matching for Generative Modeling`, `Discrete Flow Matching`, `Dirichlet Flow Matching discrete sequence`, and `Mamba state space diffusion language model text generation` over 2021--2026. The queries returned 30--40 raw rows each before deduplication.

- Semantic Scholar surfaced the core accepted works and indicated high visibility for Flow Matching, Discrete Flow Matching, Dirichlet Flow Matching, and Fisher Flow Matching.
- Crossref confirmed the NeurIPS records for Discrete and Fisher Flow Matching but also returned many unrelated combinatorial-matching papers.
- arXiv metadata and downloaded full texts were used as the primary evidence for titles, authors, claims, and modality.
- OpenAlex repeatedly returned HTTP 504, OpenReview reached HTTP 429 or returned no matches, and DBLP had a proxy failure on one run. These failures prevent an exhaustiveness claim.

Return to [[research/dlm/reference/index]].
