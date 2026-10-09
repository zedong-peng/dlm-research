---
title: "Plan for Speed: Dilated Scheduling for Masked Diffusion Language Models"
updated: 2026-10-09
---

# Plan for Speed: Dilated Scheduling for Masked Diffusion Language Models

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：正文与附录 A–B 及附录 C.1 的引理 3.3 证明（PDF 文本约 1689 行，读到约 1225 行；其后为附录 C.2/C.3 的证明与后续图，未读）。图为文本抽取，坐标轴数值多已损坏，只引用表格数字。

## Summary

问题：masked diffusion LM（MDLM）常用 confidence/entropy 的 top-k planner 决定每步 unmask 哪些位置。这类 planner 倾向挑相邻、强相关的 token 并行揭示，行为接近自回归，并行加速时质量掉得快（§2, §3.4, 附录 B.7）。

方法：Dilated Unmasking Scheduler（DUS）是纯推理期、不依赖模型输出的确定性 planner，用在 semi-AR 的 block 内。块大小 B，底数 a=2，共 R=⌈log_a B⌉ 轮，第 t 轮步长 s_t=B/a^t，揭示位置 (k-1) mod s_t = 0 且未揭示者，即由粗到细的间隔采样（式 8–9）。每块 NFE 为 log2 B，整体加速名义为 B/log2 B：B=8/16/32/64 对应 2.7/4/6.4/10.7 倍（§3.7）。

理论动机：在“最优 denoiser”假设下，并行采样的代价是 sum of marginal entropies 与联合熵之间的 gap（Lemma 3.3, Cor. 3.4）。在 fast-mixing 的 VLMC 假设下，间隔越大互信息按指数衰减，gap 越小（Lemma 3.5–3.6）。

结果（LLaDA-8B Base/Instruct、Dream-7B-Instruct、DiffuCoder-7B Base/Instruct）：
- 与等 NFE 的 self-confidence（k=log2 B）相比，DUS 在数学、代码上几乎全面更好。例：LLaDA-Instruct GSM8K，B=32 时 65.73 对 38.74；B=64 时 35.18（Base）对 8.04（Table 1）。BBH、MMLU-Pro 增益较小但方向一致（Table 2）。
- 墙钟（RTX 6000 Ada，LLaDA-Base，GSM8K）：DUS B=16 为 3.9 倍、准确率 59.51；B=32 为 5.8 倍、49.36；token-by-token 为 72.63（Table 12）。
- 把 dilated spacing 作为后置过滤器加到 EB/CB sampler 上，在 B=32、g0=8 下 6 个 (模型, 数据集) 组合准确率均上升，NFE 同步增加，如 LLaDA-Inst. HumanEval EB 24.4/35 → 37.8/59（Table 4, 附录 B.10）。
- 消融：a=2、base skip=1 最优（Table 9）；固定 k 与递增 k 的对比显示是“间隔”而非“每步多揭示”在起作用（Table 3）；B=G 单块时 NFE 约 8–11，LLaDA-Instruct GSM8K 为 42.3（Table 8）。

## Evidence and Limits

- 结论措辞与证据有差距。摘要和结论称 DUS “同时加速并提升质量”，但相对 token-by-token 的 ×1 基线，DUS 在所有加速档位都掉点（如 LLaDA-B GSM8K 72.63 → 59.51 @ B=16）。真正成立的是“相对同预算的 confidence planner 掉得更少”。
- 与自适应 sampler 的比较：附录 B.9 与 Table 12 承认 EB/CB 在更高 NFE 下准确率更高（EB γ=1.0：2.8 倍、71.72；CB τ=0.7：3.4 倍、72.02，而 DUS B=16 为 3.9 倍、59.51）。DUS 的卖点是确定性、可预测的加速，不是更优的 Pareto 前沿。该比较未在匹配 NFE 下给出。
- 主基线 self-confidence 使用固定 k=log2 B，且是 block 内 top-k，本身较弱；Table 3 显示 Conf. 递增 k 反而更差，说明基线设计对结论有影响。DUS 在 B=16 墙钟上还略慢于同 NFE 的 self-confidence（6314 对 5783 ms）。
- 理论依赖强假设：最优 denoiser、平稳遍历的 fast-mixing VLMC；作者自己承认代码、诗歌等长程依赖场景不满足，只当作启发式（§3.6）。晚期细粒度轮次中相邻 token 仍强相关，界不覆盖。
- 实验设置：评测在 V100 上，计时在 RTX 6000 Ada 上；GSM8K、HumanEval、MBPP 用全集，BBH 抽 540、MMLU-Pro 抽 560，消融只用 GSM8K 前 300 条；生成长度 256（代码 512，IFEval 1024）；few-shot CoT，4/0/4/3/5-shot 不等。表中未见多种子或置信区间，Table 1 中个别格子差距很小（如 LLaDA-I HumanEval B=32：10.37 对 9.76）。
- 小模型与代码任务绝对分数很低（Dream-I HumanEval B=16：11.59），“数学上 27%、40% 的提升”口径在正文和结论里不一致（up to 27% 对 up to 40%），相对/绝对提升未说清。
- 部分 AR 参照（Llama-3-8B、Qwen3-8B）为作者自行复现，协议与 MDLM 相同，但数字偏低（Llama GSM8K 49.81）。
- 代码已公开：https://github.com/omerlux/DUS。

## Open Questions

- 在严格匹配 NFE 的条件下，DUS 与 EB/CB（以及 DUS 后置过滤版本）的完整 Pareto 前沿如何？目前只有各自 sweep 的散点。
- 间隔式调度的收益有多少来自“更均匀的上下文传播”，有多少只是弥补了 confidence planner 的聚簇缺陷？更强的基线（带去相关约束的 confidence planner、学习式 planner 如 P2）未比较。
- 在长生成、非 semi-AR 的整序列设置，以及已训练过 block 内 any-order 的模型上，收益是否保持？单块 B=G 仅在 LLaDA 上、准确率很低。
