---
title: DLM Curated Reading List
domain: research
area: dlm
type: comparison
status: active
updated: 2026-07-16
tags: [papers, diffusion-language-models, reading-list]
---

# DLM Curated Reading List

This list is organized by what a paper teaches, not just by date. "Local" means the PDF and extracted text are stored under `reference/papers/`.

## Tier 0: Survey Anchors

| Paper | Why read it | Local |
|---|---|---|
| [Discrete Diffusion in Large Language and Multimodal Models: A Survey](https://arxiv.org/abs/2506.13759) (2025) | Broad taxonomy across foundations, training, inference, large models, and multimodality | PDF + text |
| [Accelerating Masked Diffusion Large Language Models: A Survey of Efficient Inference Techniques](https://arxiv.org/abs/2607.12829) (2026) | Current deployment-oriented taxonomy and latency decomposition; posted 2026-07-14 | PDF + text |

## Tier 1: Foundations

| Paper | Main contribution | Relevance to the optimization question |
|---|---|---|
| [Structured Denoising Diffusion Models in Discrete State-Spaces](https://arxiv.org/abs/2107.03006) (D3PM, NeurIPS 2021) | General discrete-state Markov diffusion | Defines the discrete process that later masked models simplify |
| [Diffusion-LM Improves Controllable Text Generation](https://arxiv.org/abs/2205.14217) (NeurIPS 2022) | Continuous embedding diffusion for controllable text | Important ancestor, but its numerical geometry differs from masked DLM decoding |
| [Flow Matching for Generative Modeling](https://arxiv.org/abs/2210.02747) (ICLR 2023) | Simulation-free vector-field regression for chosen continuous probability paths | Separates path/training design from the inference ODE solver; local PDF + text |
| [Discrete Flow Matching](https://arxiv.org/abs/2407.15595) (NeurIPS 2024) | Probability paths and velocities over discrete states, with text and code experiments | Direct alternative foundation to discrete diffusion; local PDF + text |
| [Dirichlet Flow Matching](https://arxiv.org/abs/2402.05841) and [Fisher Flow Matching](https://arxiv.org/abs/2405.14664) (2024) | Simplex and Fisher-Rao geometries for categorical sequences | Clarify that continuous relaxations of discrete data have non-Euclidean design choices; local PDFs + text |
| [Flow Matching with General Discrete Paths](https://arxiv.org/abs/2412.03487) (2024) | Decouples arbitrary categorical paths from probability velocities | Makes path selection itself an optimization surface; local PDF + text |
| [DiffuSeq](https://arxiv.org/abs/2210.08933) (ICLR 2023) and [SSD-LM](https://arxiv.org/abs/2210.17432) (ACL 2023) | Conditional continuous diffusion and semi-AR simplex diffusion for text | Important pre-large-DLM branches with different state/length interfaces; local PDFs + text |
| [[research/linear-attention/papers/mamba-2023/index|Mamba]] and [[research/linear-attention/papers/mamba-2-2024/index|Mamba-2]] | Linear-time selective SSM backbones | Backbone choice is orthogonal to diffusion/flow and sampler design; MDLM uses a Mamba-based SSM for DNA |
| [DiffusionBERT](https://arxiv.org/abs/2211.15029) (2022) | Connects masked LMs and diffusion for text generation | Early absorbing-mask language model |
| [Mask-Predict](https://arxiv.org/abs/1904.09324) (EMNLP 2019) | Iterative masked text decoding with a fixed iteration budget and linear confidence-remasking schedule | Canonical ancestor for any progress-indexed keep/remask policy; local PDF + text |
| [MaskGIT](https://arxiv.org/abs/2202.04200) (CVPR 2022) | Keeps the most confident discrete tokens and remasks the rest under a decreasing schedule | Cross-modal canonical ancestor for fixed-cardinality confidence projection; local PDF + text |
| [A Reparameterized Discrete Diffusion Model for Text Generation](https://arxiv.org/abs/2302.05737) (2023) | Simplifies discrete diffusion parameterization and sampling | Helps explain why masking/unmasking becomes the operative interface |
| [Discrete Diffusion Modeling by Estimating the Ratios of the Data Distribution](https://arxiv.org/abs/2310.16834) (SEDD, ICML 2024) | Score/ratio estimation for discrete diffusion | Strong likelihood foundation; not a deployment sampler by itself |
| [Simple and Effective Masked Diffusion Language Models](https://arxiv.org/abs/2406.07524) (MDLM, NeurIPS 2024) | Rao-Blackwellized masked objective and effective sampling recipe | Best starting point for the modern masked-DLM formulation; local PDF + text |
| [Block Diffusion](https://arxiv.org/abs/2503.09573) (ICLR 2025) | Interpolates AR and diffusion, supports arbitrary length and KV caching | Shows architecture and block granularity are part of the efficiency problem |

## Tier 2: Scaled DLMs and Reasoning

| Paper | Main contribution | Caveat for sampler research |
|---|---|---|
| [Large Language Diffusion Models](https://arxiv.org/abs/2502.09992) (LLaDA, 2025) | Scales masked diffusion to an 8B instruction-following model | Common open baseline; results depend strongly on decoding protocol |
| [Dream 7B](https://arxiv.org/abs/2508.15487) (2025) | AR initialization plus context-adaptive noise rescheduling | Second important open model family for cross-model validation |
| [d1](https://arxiv.org/abs/2504.12216) (2025) | SFT plus diffusion-specific GRPO for reasoning | Post-training work, distinct from drop-in inference-only sampling |
| [TESS 2](https://arxiv.org/search/?query=TESS+2+large-scale+generalist+diffusion+language+model&searchtype=title) (ACL 2025) | Generalist instruction-following DLM and reward guidance | Shows extra inference compute can improve alignment/quality |
| Seed Diffusion (2025) | Reports high-speed large-scale code generation | Details and reproducibility should be checked before using as a baseline |
| [Improved Large Language Diffusion Models](https://arxiv.org/abs/2606.25331) (iLLaDA, 2026) | Larger-scale bidirectional masked training and variable-length inference | Very recent; useful for testing whether sampler conclusions survive stronger bases |

## Tier 3: Must-Read Sampling and Scheduling

| Paper | Mechanism | Collision risk for a new OR idea | Local |
|---|---|---|---|
| [Accelerated Sampling via Entropy Bounded Unmasking](https://arxiv.org/abs/2505.24857) (EB-Sampler, NeurIPS 2025) | Adaptive unmasking under approximate error tolerance | Direct collision with budget/error-constrained token selection | PDF + text |
| [Plan for Speed](https://arxiv.org/abs/2506.19037) (DUS, 2025) | Dilated nonadjacent groups minimize an upper bound on joint entropy gain | Direct collision with structure-aware subset scheduling | PDF + text |
| [Fast-dLLM](https://arxiv.org/abs/2505.22618) (2025) | Confidence-aware parallel decoding plus approximate KV cache | Direct collision with joint sampler/system acceleration |  |
| [Saber](https://arxiv.org/abs/2510.18165) (2025/2026 revision) | Adaptive unmask count plus confidence-drop backtracking | Direct collision with dynamic allocation plus rollback | PDF + text |
| [KLASS](https://arxiv.org/abs/2511.05664) (NeurIPS 2025) | KL-based prediction-stability score | Direct collision with temporal stability as an allocation signal | PDF + text |
| [Lookahead Unmasking](https://arxiv.org/abs/2511.05563) (2025) | Multi-path proposal and uncertainty verification | Direct collision with rollout/lookahead optimization | PDF + text |
| [Learning Unmasking Policies for Diffusion Language Models](https://arxiv.org/abs/2512.09106) (2025) | Formalizes sampling as an MDP and trains a lightweight RL policy | Fully occupies the generic "formulate unmasking as an MDP" claim | PDF + text |
| [Diffusion Language Model Inference with Monte Carlo Tree Search](https://arxiv.org/abs/2512.12168) (MEDAL, 2025) | MCTS over high-confidence early unmasking paths | Fully occupies generic MCTS/search-over-order claims | PDF + text |
| [Attention-Discounted Adaptive Sampler](https://arxiv.org/abs/2606.10829) (ADAS, 2026) | Greedy attention-discounted subset construction | Direct collision with interaction-aware subset optimization | PDF + text |
| [Reversible Diffusion Decoding](https://arxiv.org/abs/2602.00150) (RDD, 2026) | Cached block backtracking and selective reinitialization | Collision with reversible/receding-horizon decoding |
| [Cluster-Level Attention-Guided Parallel Decoding](https://arxiv.org/abs/2605.29607) (CLAD, 2026) | Span clusters plus attention-based conflict selection | Collision with cluster/set packing approaches |
| [Supportive Token Revealing](https://arxiv.org/abs/2606.04236) (AXON, 2026) | Reveals context that helps hard remaining tokens | Collision with value-of-information/future-decodability ideas |
| [Where-to-Unmask](https://arxiv.org/abs/2602.09501) (2026) | Ground-truth-guided learning of unmasking order | Collision with supervised scheduling policies |
| [Adaptive Parallel Decoding](https://arxiv.org/abs/2602.23792) (2026) | Request/state-adaptive parallel decoding | Collision with divide-and-conquer allocation claims |
| [Deferred Commitment Decoding](https://arxiv.org/abs/2601.02076) (2026) | Sliding eligibility window and confidence-gated commitment | Collision with boundary-aware or uncertainty-deferred commitment; local PDF + text |
| [Re-evaluating Confidence Remasking](https://arxiv.org/abs/2606.12232) (2026) | Tests WINO remasking against strong unmasking at matched settings and documents a net-progress guard | Essential negative evidence for generic post-hoc remasking; local PDF + text |
| [RACC](https://aclanthology.org/2026.findings-acl.1138/) (Findings of ACL 2026) | Confidence-momentum regret feeds back into global top-k commitment | Collision with temporal-feedback or confidence-calibration controllers; local PDF + text |
| [TACG](https://arxiv.org/abs/2607.03236) (2026) | Historical-logit support and persistence gate commitment under a capped promotion budget | Collision with trajectory-aware commitment readiness; local PDF + text |
| [NAVIRA](https://arxiv.org/abs/2606.06031) (2026) | Full-set deterministic/stochastic remasking with progress schedules across forward-pass budgets | Collision with scheduled top-k revision policies; local PDF + text |

## Tier 4: Inference Systems and Caching

| Paper/system | Main lever | What to measure |
|---|---|---|
| dLLM-Cache (2025) | Adaptive cache reuse across denoising steps | Hit rate, refresh policy, memory, quality drift |
| [dKV-Cache](https://arxiv.org/abs/2505.15781) (NeurIPS 2025) | Diffusion-specific delayed/conditioned KV cache | Cache validity under token changes |
| [Elastic-Cache](https://arxiv.org/abs/2510.14973) (2025) | Attention-aware drift test plus depth-aware refresh schedule | Direct collision with adaptive where/when cache refresh |
| [SPA-Cache](https://arxiv.org/abs/2602.02544) (2026) | Low-dimensional update-critical-token proxy and heterogeneous layer budgets | Direct collision with joint token/layer refresh allocation |
| [COVER](https://arxiv.org/abs/2602.06161) (2026) | KV-cache override for leave-one-out verification during revocable decoding | Direct collision with revision-aware cache/verification co-design |
| FlashDLM (2025) | Efficient KV caching plus guided diffusion | Separate cache savings from extra guidance calls |
| SparseD / Window-Diffusion (2025-2026) | Sparse attention, token pruning, local windows | Long-range failures and actual kernel speed |
| dInfer (2025) | Modular model/iteration/decoder/cache runtime | Portability beyond multi-H800 results |
| BlockBatch (2026) | Multi-block-size parallel branches and consensus | Branch memory and p95 latency |
| [Speculative Diffusion Decoding](https://arxiv.org/abs/2408.05636) (NAACL 2025) | Draft-and-verify generation | Draft/verify evaluation accounting and acceptance rate |

## Tier 5: Theory and Limits

| Paper | Question answered | Remaining research opening |
|---|---|---|
| Theoretical Benefit and Limitation of Diffusion Language Model (2025) | Shows sampling-step benefits depend on metric; sequence error can scale differently from perplexity | Build methods/evaluations around sequence-level correctness, not likelihood alone |
| Why Masking Diffusion Works (NeurIPS 2025) | Conditions on jump schedules for improved discrete diffusion | Connect training schedule theory to inference-time control carefully |
| Fast Solvers for Discrete Diffusion Models (NeurIPS 2025) | High-order algorithms for discrete diffusion | Determine whether solver guarantees survive large masked-LM approximations |
| [Scaling Beyond Masked Diffusion Language Models](https://arxiv.org/abs/2602.15014) (2026) | Compares scaling/speed-quality across discrete diffusion families | Avoid treating masked diffusion as categorically dominant |

## Tier 6: Continuous-Solver Transfer

| Paper | Transferable concept | Non-transferable part |
|---|---|---|
| [DPM-Solver](https://arxiv.org/abs/2206.00927) (NeurIPS 2022) | Specialized solver, nonuniform time discretization, few NFE | Continuous probability-flow ODE geometry |
| [DPM-Solver++](https://arxiv.org/abs/2211.01095) | Guided sampling and stability at few steps | Still a continuous numerical solver |
| A Unified Sampling Framework for Solver Searching (ICLR 2024) | Search over solver components under a budget | Search space must be redefined for token actions |
| [Optimizing Few-Step Sampler](https://arxiv.org/abs/2412.10786) (2024) | Direct optimization of few-step schedules | Image-domain objective and continuous state |
| Few-Step Diffusion Sampling Through Instance-Aware Discretizations (2026) | Allocate grids per instance | Needs a discrete analogue of local integration error |

## Recommended Order for the Classmate Discussion

1. MDLM: agree on the mathematical process and baseline sampler.
2. Mask-Predict and MaskGIT: identify the inherited fixed-horizon keep/remask skeleton before claiming a new schedule.
3. EB-Sampler and Plan for Speed: determine whether the proposed algorithm is already an entropy/spacing policy.
4. Saber, Confidence Remasking, and NAVIRA: determine whether it includes rollback or revision.
5. Learning Unmasking Policies and MEDAL: determine whether "optimization" means RL/MDP or search.
6. ADAS, CLAD, and AXON: determine whether it models token interactions or future information value.
7. Elastic-Cache and COVER: check whether sampler/cache or revocable verification is already occupied.
8. Acceleration survey: translate the claimed complexity improvement into end-to-end latency terms.

Only after this comparison should the proposed method be written as a novelty claim in [[research/dlm/paper/legacy/ideas/index]].

## Citation Snapshot Caveat

Semantic Scholar counts observed during this pass make Dream 7B, d1, dLLM-Cache, DPM-Solver/++, and energy-based DLM work highly visible, but counts are volatile, duplicated across preprint/publication records, and not a substitute for relevance. The reading order above is based on conceptual proximity to the optimization question.
