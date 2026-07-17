---
title: DLM Research Foundation for Team Discussion
domain: research
area: dlm
type: synthesis
status: active
updated: 2026-07-16
tags: [research-foundation, team-discussion, diffusion-language-models]
---

# DLM Research Foundation for Team Discussion

This note defines the current stage of the project: build shared research foundations before proposing a new algorithm.

返回 [[research/dlm/paper/index]]。文献入口见 [[research/dlm/reference/index]]。

## Current Goal

The goal is not yet to claim novelty. The goal is to make the next research conversation precise enough that a teammate's proposed algorithm can be placed into the right mathematical and empirical frame.

## Six Foundation Questions

| Foundation | What must be fixed | Why it matters |
|---|---|---|
| State space and forward process | Continuous vector, categorical transition matrix, CTMC, or absorbing mask | Prevents importing the wrong solver intuition |
| Reverse model | Clean-token distribution, transition kernel, score, or probability ratio | Defines what the neural network actually estimates |
| Training objective | ELBO/NELBO, score objective, weighted masked-token loss, or trajectory training | Separates model learning from decoding heuristics |
| Sampler | Time grid, update set, revision, search, length rule, and stopping | Identifies the executable inference approximation |
| Cost and constraint | NFE, wall-clock, p50/p95 latency, memory, quality, and completion | Makes comparisons fair |
| Changed interface | Model, training, sampler, length mechanism, or runtime system | Determines the relevant literature and baseline class |

## Shared Minimal Formulation

For masked DLM inference, use this as the common notation:

```text
state  s_t = (x_t, M_t, logits/scores, history, cache_state, remaining_budget)
action a_t = (commit_set, remask_set, compute_mode, stop)
```

Under a call budget:

```text
maximize_pi  E[terminal_quality(x_0)]
subject to   total_forward_calls <= B
             no MASK remains at termination
```

Under a serving objective:

```text
minimize_pi  p95_latency
subject to   task_failure_probability <= alpha
             memory <= C
```

## Background Map Before Idea Selection

| Branch | First question for discussion |
|---|---|
| Mathematical process | How do `q(z_t|x)`, the reverse posterior, and `p_theta` connect? |
| Training | Does the training mask distribution match the inference trajectory? |
| Sampling | Is the method changing a reverse-time discretization or a post-forward token policy? |
| Length | Is the response canvas fixed, predicted, extended by blocks, or changed online? |
| Systems | Which computation is saved, and when do token revisions invalidate reuse? |
| Evaluation | Are model calls, latency, memory, completion, and quality matched? |

## Meeting Output Template

Each serious discussion with a teammate should end with these five lines:

```text
Diffusion object:
Learned reverse quantity:
Sampler interface:
Budget currency:
One diagnostic experiment:
```

If any line is vague, the next step is more background reading or measurement, not implementation or idea selection.
