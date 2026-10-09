---
title: Diffusion Language Models Research Base
domain: research
area: dlm
type: overview
status: active
updated: 2026-10-09
tags: [diffusion-language-models, discrete-diffusion, flow-matching, optimization, inference]
---

# Diffusion Language Models Research Base

The active research project and wiki references use the following directories:

| Directory | Start here | Role |
|---|---|---|
| `reference/` | [[research/dlm/reference/index]] | Literature, PDFs, extracted text, search logs, and reusable technical maps |
| `paper/` | [[research/dlm/paper/index]] | English ICML-style paper draft, TikZ figures, bibliography, and archived idea provenance |
| `assets/` | [[research/dlm/reference/papers/index]] | Paper archives: citation records, PDF/text pairs, and notes with explicit historical reading scope |

Additional collaborator materials are retained in `reportfrom-wang/`:
[document](<reportfrom-wang/CIR_Diffusion Doc.pdf>) and
[slides](<reportfrom-wang/CIR_Diffusion Slide.pdf>). No reading record or canonical
source metadata is recorded for these files; do not treat them as reviewed evidence.

## Current Working Position

No algorithmic direction is selected yet. The immediate deliverable is a shared background foundation:

1. distinguish continuous diffusion, flow matching, general discrete diffusion, and absorbing-mask language diffusion;
2. connect the forward process, reverse conditional model, training objective, and executable sampler;
3. separate probability-path, training-target, backbone, sampler, length, and runtime changes;
4. use optimization and operations-research language only after the changed interface and budget are explicit.

The active discussion artifact is the [compiled ICML-style foundation](paper/main.pdf).

## Workflow

1. Add or refresh sources in [[research/dlm/reference/index]].
2. Maintain the shared English foundation in [[research/dlm/paper/index]].
3. Keep raw idea-generation provenance only under `paper/legacy/ideas/`; do not treat it as the active idea interface.

## Guardrails

- Compare full quality-latency frontiers, not just diffusion steps.
- Report NFE, wall-clock latency, p50/p95, policy overhead, memory, length, batch, and hardware.
- Separate inference-only methods from methods requiring retraining.
- Keep research direction open until the team agrees on the mathematical object and reproduces a matched baseline.

返回 [[research/index]]。
