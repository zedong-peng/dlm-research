---
title: "Diffusion Language Model Inference with Monte Carlo Tree Search"
updated: 2026-10-09
---

# Diffusion Language Model Inference with Monte Carlo Tree Search

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、参考文献、附录 A.1–A.4 与各 prompt 图）。Figure 1/2/3 只有文字残片，Figure 2 的曲线数值无法读取；未见代码链接。

## Summary

问题：masked DLM 推理时要决定每步 unmask 哪些位置、填哪些 token，现有做法多为 confidence 贪心，被认为"短视"；另一类需要额外训练 sampler 或 RL。

方法 MEDAL（§3，Algorithm 1/2）：无需训练，三部分。
- 只在生成初期用 MCTS 做初始化：树节点是部分 unmask 的序列，action 为 (位置 i, token v)。选满 C 个长度为 Lc 的候选前缀后停止搜索，再用已有的 confidence 解码填完剩余位置。
- Confidence-adjusted score s = p(v)·exp(−H)·sigmoid(γ·top2 margin)，先每位置取 top-K1，再全局取 top-K2 作为扩展动作（§3.1.1）。
- Simulation 让 DLM 一次性填完剩余 mask；reward 为剩余位置的 entropy 相对下降量（information-gain，式 9）；selection 用 UCB。
- 任务分解：用两条示例把 prompt 扩成 3 个子任务，让模型先自己分解再作答（§3.3，附录 Figure 4–8）。

设置（§4.1）：LLaDA-8B-Instruct、LLaDA-1.5、Dream-7B，生成长度 256，Lc=20、K1=3、K2=5、C=3，2×A100 40GB，seed 固定为 1。六个数据集：GSM8K、ARC-C、HumanEval、MMLU、DROP、Countdown。对照为原模型、Best-of-5 多数投票、Llama-3 8B。

结果（Table 1）：LLaDA 上 GSM8K 58.3→66.7，ARC-C 72.2→82.1，HumanEval 40.2→47.5，MMLU 36.0→44.0，DROP 58.2→71.0（相对提升 22.0%，即摘要的 "up to 22.0%"），Countdown 15.6→18.9。Best-of-5 的提升小很多（LLaDA 平均 +4.1 vs MEDAL +8.1）。LLaDA1.5 与 Dream 趋势相同。
- 消融（Table 2，LLaDA）：去掉 MCTS 后 ARC-C 81.0、HumanEval 44.3、DROP 64.9；去掉任务分解后 77.3/43.4/65.7；去掉 confidence 分数只留 top-2 margin 后 75.0/40.9/60.1。
- 超参（Table 3/4、Figure 2）：子任务数 3 最好（5、10 明显下降）；K2=5 最好；Lc 增大到约 20 后饱和。
- 算力（Table 7，GSM8K）：MEDAL 耗时 12.3c，Best-of-15 为 15c，准确率 66.7 vs 65.3。
- Agent 案例（Table 5）：把 ADAS 里的 LLM 换成 MEDAL+LLaDA，DROP 73.0、MMLU 46.5，高于 LLaDA+ADAS（71.2/41.0）与 Llama+ADAS（65.2/45.2）。

理论（§3.4、A.4）：沿用 Ben-Hamu et al. 2025 的 entropy-gap 上界 B(z) 作为联合依赖误差的代理，称 MCTS 初始化在可行前缀内最小化累积代理成本。

## Evidence and Limits

- 提升的来源不清。完整方法同时改了 prompt（两条示例的任务分解）和解码；表中"原模型"是否使用同样的示例 prompt 未明确说明。去掉 MCTS 仍能拿到大部分增益（ARC-C 81.0 vs 82.1），说明相当一部分来自 prompt 与 confidence 打分，而不是树搜索本身。
- 论文称"优于现有推理策略"，但没有与任何已有的 DLM 解码方法（entropy 规划、path planning、Ben-Hamu 等，均在 Related Works 中提到）做对比，只比了原始解码和 Best-of-5。Best-of-5 的实现细节（如 HumanEval 如何做"多数"）未说明。
- 单 seed。Table 6 的标准差为 ±1.4–4.4（来源未说明），与不少提升幅度同量级；正文却说方法"降低方差"，而表中半数以上项的标准差反而变大。LLaDA 的 Table 6 仅此一次，Dream/Llama 同理。
- 理论部分：Assumption 1（B 是依赖误差上界）直接假设，MCTS 实际优化的是 information-gain reward 而非 J，二者关系没有证明；结论是 UCT 渐近一致的标准论断，对有限预算（C=3）没有实际约束力。
- 正文把 Table 1 称为"五个数据集"，实为六个；Llama 在 Countdown 仅 3.2。MMLU 数值（LLaDA 36.0）明显低于常见报告，评测协议（few-shot、长度 256 截断）未交代。
- 局限（§7）：仅文本 DLM；agent 场景只是一个小案例。附录算力对比只在 GSM8K 上做，且未给出相对原始 LLaDA 的绝对开销倍数的分解（仅 c=9.64s）。

## Open Questions

- 把示例 prompt 同样给原始 LLaDA（以及 Best-of-N、其他 DLM 解码器）后，MCTS 初始化本身还剩多少增益？
- Information-gain reward（熵下降）与最终答案正确率的相关性如何？能否换成更直接的信号？
- 为什么 Lc 超过约 20 后收益饱和，在更长生成长度或更难任务上这个"早期决策最关键"的结论是否仍成立？
