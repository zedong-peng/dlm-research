---
title: DLM Idea Novelty Audits
domain: research
area: dlm
type: synthesis
status: active
updated: 2026-07-16
tags: [novelty-audit, prior-art, diffusion-decoding]
---

# DLM Idea Novelty Audits

返回 [[research/dlm/paper/legacy/ideas/index]]。

This folder stores independent prior-art checks for candidate ideas. The purpose is to preserve the kill logic, not to make each rejected idea sound stronger.

## Completed Audits

| Audit | Candidate | Verdict | Why it matters |
|---|---|---|---|
| [[research/dlm/paper/legacy/ideas/audits/deadline-paths-2026-07-15/report]] | Deadline Paths for Training-Free Masked Diffusion Decoding | Level 2 - high overlap | Mask-Predict, MaskGIT, Saber, confidence remasking, RACC, TACG, and NAVIRA occupy the core ingredients |

## How To Use This Folder

Before promoting an idea in [[research/dlm/paper/legacy/ideas/index]] from `hold` to `audit`, write the claimed mechanism as one sentence. Then check whether the audit should compare against model-level diffusion work, sampler-level DLM work, systems/cache work, or older non-autoregressive mask-predict work.

An audit is useful only if it ends with one of three decisions:

1. kill the idea;
2. keep it only as a baseline or ablation;
3. promote it with a precise remaining delta and a falsification test.
