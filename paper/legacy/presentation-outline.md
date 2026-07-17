---
title: DLM Research Briefing Outline
domain: research
area: dlm
type: checklist
status: active
updated: 2026-07-16
tags: [presentation, briefing, diffusion-language-models]
---

# DLM Research Briefing Outline

## 15 分钟版本

| 页 | 时间 | 标题 | 核心信息 | 使用材料 |
|---:|---:|---|---|---|
| 1 | 0:30 | 我们到底在解什么 diffusion？ | continuous solver 与 masked DLM controller 不同 | [[research/dlm/paper/legacy/diffusion-process-visual]] 第一图 |
| 2 | 1:30 | 历史主线 | 研究重心已从建模转向 inference 与 systems | [[research/dlm/paper/legacy/history-and-landscape]] 时间线 |
| 3 | 1:30 | Masked DLM 的 forward/reverse | 从 clean text 到 mask，再从 mask 恢复 | process 图 |
| 4 | 2:00 | 一次 sampler iteration | predict、select、commit、remask、cache、stop | controller loop |
| 5 | 1:30 | 为什么并行不等于安全 | token-wise confidence 不保证 joint compatibility | dependency 图 |
| 6 | 2:00 | 运筹优化形式化 | state、action、budget、quality/SLO constraint | [[research/dlm/paper/legacy/math-or-discussion]] |
| 7 | 2:00 | 现有算法版图 | schedule、subset、revision、search、cache | landscape 图 |
| 8 | 1:30 | 三轮 idea 淘汰 | obvious ideas 已被占据 | [[research/dlm/paper/legacy/ideas/index]] |
| 9 | 1:00 | 仍值得做什么 | shift calibration、sequence certificate、p95 control | surviving portfolio |
| 10 | 0:30 | 讨论问题 | 同学的新算法落在哪一类，唯一 delta 是什么？ | 七问清单 |

## 三分钟版本

1. Masked DLM 的算法不是普通 ODE solver，而是在每次模型预测后控制 token subset、revision 与预算。
2. 该空间已经有 entropy/confidence、RL、MCTS、attention-aware selection、remasking 和 cache 方法。
3. 因此新算法必须针对一个已测得的 residual failure，并在 matched latency 下优于 closest prior。
4. 当前最值得验证的是 quality-latency controller 在 task/model/length shift 下是否失准。

## 汇报中不要说

- 不说“第一个用 optimization/MDP/MCTS 的 DLM 方法”。这些方向已有直接 prior art。
- 不把 diffusion steps 直接等同于速度；extra forward、cache refresh 和 controller cost 都要计入。
- 不把 attention 或 confidence 自动解释成 token 的真实边际价值。
- 不把一个内部 IdeaSpark `advance` 当作 novelty 结论；独立 audit 已推翻过一次。

## 最后一页的讨论问题

> 给定相同模型与端到端预算，你的算法获得了什么现有 sampler 观察不到的信息，或者满足了什么现有方法没有的保证？

参考文献入口：[[research/dlm/reference/index]]。idea 审计入口：[[research/dlm/paper/legacy/ideas/index]]。

