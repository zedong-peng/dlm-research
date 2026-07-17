---
title: Diffusion Process Visual Primer
domain: research
area: dlm
type: concept
status: active
updated: 2026-07-16
tags: [diffusion-process, visualization, masked-diffusion, primer]
---

# Diffusion Process Visual Primer

## 先区分三种“解 diffusion”

```mermaid
flowchart TB
    Q["solving the diffusion process"] --> C["Continuous diffusion"]
    Q --> D["General discrete diffusion"]
    Q --> M["Masked diffusion language model"]

    C --> C1["状态：连续向量"]
    C --> C2["算法：SDE/ODE solver + time grid"]

    D --> D1["状态：有限类别/token"]
    D --> D2["算法：transition / CTMC / score ratio"]

    M --> M1["状态：部分 token + MASK"]
    M --> M2["算法：选择、commit、remask、停止"]
```

continuous diffusion 主要问“积分怎么算得更准、更少步”；masked DLM 主要问“每次 forward 后该对哪些离散位置采取什么动作”。两者可以互相借思想，但不是同一个优化问题。

## Forward 与 Reverse

训练时的 forward process 把干净文本逐渐替换成 mask；生成时的 reverse process 从 mask canvas 恢复文本。

```mermaid
flowchart LR
    X0["x0: diffusion models generate text"] --> X1["x1: diffusion [MASK] generate text"]
    X1 --> X2["x2: [MASK] [MASK] generate [MASK]"]
    X2 --> XT["xT: [MASK] [MASK] [MASK] [MASK]"]

    XT -.->|reverse / generation| Y2["[MASK] models [MASK] text"]
    Y2 -.-> Y1["diffusion models [MASK] text"]
    Y1 -.-> Y0["diffusion models generate text"]
```

在 absorbing-mask 模型中，可把单个位置的 forward corruption 直观理解为：随时间增大，token 留在原值的概率下降，进入 `[MASK]` 后不再回到普通 token。模型学习的是给定部分被 mask 的 canvas 时，对原 token 的条件分布。

## 一次反演迭代到底做什么

```mermaid
flowchart LR
    A["当前 canvas x_t"] --> B["DLM forward<br/>得到每个位置的分布"]
    B --> C["计算 confidence / entropy / KL / dependency"]
    C --> D{"sampler/controller"}
    D --> E["commit U_t"]
    D --> F["remask R_t"]
    D --> G["cache refresh / extra compute"]
    E --> H["下一 canvas x_(t-1)"]
    F --> H
    G --> H
    H --> I{"完成或预算耗尽？"}
    I -->|否| B
    I -->|是| J["输出 x_0"]
```

最小状态与动作可以写成：

```text
state  s_t = (x_t, masked set M_t, model scores,
              history, dependency signal, cache state, budget b_t)

action a_t = (commit set U_t, remask set R_t,
              compute/cache mode, stop)
```

## 为什么不能一次把所有 token 都填上

并行预测通常近似为各位置独立，但真实文本 token 彼此依赖。两个位置各自都高 confidence，不代表它们同时 commit 后仍然兼容。

```mermaid
flowchart LR
    M["部分上下文"] --> A["位置 i: 高 confidence"]
    M --> B["位置 j: 高 confidence"]
    A --> C{"联合是否一致？"}
    B --> C
    C -->|可能否| E["重复、语法冲突、推理链错误"]
    C -->|是| F["安全并行 commit"]
```

这就是 DUS、ADAS、CLAD 等方法研究 spacing、attention 或 cluster dependence 的原因，也是简单 top-k confidence 的结构性局限。

## 与运筹优化的最短连接

在固定 forward-call 预算 `B` 下，可以写成：

```text
maximize_pi  E[terminal quality(x_0)]
subject to   sum_t model_evaluations_t <= B
             x_0 contains no MASK
```

如果目标是服务 SLO，则更自然的是：

```text
minimize_pi  E[latency]
subject to   E[task loss] <= epsilon
             P(latency > SLO) <= delta
```

难点不在写出目标，而在于 terminal quality 很晚才知道、动作是组合子集、token 之间耦合，而且 controller 本身也消耗时间。

下一步：[[research/dlm/paper/legacy/math-or-discussion]]、[[research/dlm/reference/optimization-bridge]]。
