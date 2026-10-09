---
title: "Deferred Commitment Decoding for Diffusion Language Models"
updated: 2026-10-09
---

# Deferred Commitment Decoding for Diffusion Language Models

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、参考文献、附录 A–D）。图（Fig. 1–4）只有标题，无法看到曲线；Table 1 中 NBDiff 行的列对应略有错位，Table 3–6 的 NBDiff 行也有疑似错位。

## Summary

- 问题：block-based 扩散解码（为兼容 KV cache）要求当前 block 内的 token 在进入下一 block 前全部提交。靠近 block 右边界的 token 看不到近处的右侧上下文，被迫低置信度提交。作者称之为 Boundary-Induced Context Truncation (BICT)（§4）。依据是 DLM 的局部上下文偏置（引 Piskorz et al.）。
- 方法：Deferred Commitment Decoding (DCD)，training-free（§5）。
  - 用滑动窗口取代固定 block：左端锚定在最左侧的 mask，右端最多扩到 s_max，且窗口内 mask 数不超过 s_init。
  - 窗口内只解码置信度（或负熵）≥ min(τ, 窗口内最大值) 的 token，其余推迟。
  - 沿用 Fast-dLLM 的 prefix/dual cache，并按 dKV-Cache-Greedy 的思路把活跃区间略微放宽（Eq. 11）。
  - 对半因果（semi-causal）DLM 加了 Dynamic Block Extension (DBE)：窗口被大 block 边界卡住且置信度低于 τ_low 时，临时扩大 block（§5.2）。
- 设置：LLaDA-8B-Instruct、Dream-v0-Instruct/Base-7B（全注意力），Fast-dLLM-v2-7B、NBDiff（半因果）。基准为 HumanEval、MBPP、MATH500、GSM8K、IFEval。cache 配置为 none/prefix/dual。统一 τ=0.9，L=512，s_init=16，s_max=128（半因果 s_init=8）。
- 结果（Table 1）：
  - 相对 (sub-)block 基线，平均指标 +1.73%，耗时平均 -4.4%。
  - 按模型平均：LLaDA +1.16、Dream-Instruct +2.63、Dream-Base +0.77、Fast-dLLM-v2 +0.62、NBDiff +5.22。
  - NBDiff 的 IFEval 提升最大，从 40.1 到 56.6，即文中的 16.5%。
  - 对 AdaBlock，LLaDA 平均 +0.28，Dream-Base 平均 +1.20，耗时分别少 19% 和 71%。
- BICT 证据：置信度 < 0.3 的解码步数明显减少，例如 Dream-Base 的 GSM8K 从 9111 降到 601（Table 4，Fig. 3）。
- DBE（Table 2）：NBDiff 平均 +1.14，耗时 +14.9%。Fast-dLLM-v2 平均 -1.30。作者据此建议半因果 DLM 用可变 block size 训练。
- 消融（Fig. 4，LLaDA、MATH500、dual cache）：s_max、s_init、τ 的精度均呈单峰。s_max=512 因上下文稀释而变差，τ=1 因失去灵活性而变差。

## Evidence and Limits

- 效果量小且不均匀。Table 1 里不少格子是持平或下降，例如 Dream-Instruct 的 HumanEval（none）54.3→53.7，LLaDA 的 MBPP（prefix）39.8→38.2。作者归因于“训练无关解码的随机性”，没有做重复实验或显著性检验。
- 每格只有单次运行，没有方差。HumanEval 只有 164 题，1–2 分的差距接近噪声。
- “平均 +1.73%”是对各格差值取平均，还是相对提升，文中未写清。
- 因果归因：低置信度步数减少与精度提升同时出现，但没有对照实验证明提升来自 BICT 的缓解，而不是窗口带来的其他效应。窗口超参是在 LLaDA/MATH500 上调的，再统一用于所有模型。
- 基线比较不完全可比。dKV-Cache-Greedy、CCD、Prophet 的数字来自原论文（Table 3 说明），评测设置未必一致。Prophet 的 Dream-Instruct 与 LLaDA 数字也在不同 cache 设置下。
- NBDiff 的耗时：DCD 为 526163 s，baseline 项缺失。Table 3 里 NBDiff 的 DCD 耗时远高于其他模型（HumanEval 35215 s），附录给的 NBDiff 行 forward length 恒为 32.0，疑似表格错位或口径不同。文中“耗时相当”的结论对 NBDiff 无法核实，Table 2 中 DBE 还要再多 14.9%。
- 硬件不统一：A100 80GB 与 A800 混用，NBDiff 用 opencompass，其余用 lm-eval-harness；batch size=1。
- 未用 stop words，作者承认这可能拉低 Dream-Base，但对两种解码一致。
- Dream-Base 的 prefix 和 dual 下 HumanEval 反而下降（57.3→53.0，57.3→56.1）。
- 不评测多选 QA，理由是它测的是 log-prob 而非解码质量。

## Open Questions

- 精度增益有多少来自 BICT 的缓解，有多少来自“按置信度动态选择解码位置”本身？缺少与仅用置信度阈值（无窗口）的解码做对照。
- 对半因果模型，DCD 的收益依赖模型是否用多种 block size 训练。Fast-dLLM-v2 上的收益小且 DBE 为负，能否推广到其他半因果模型，文中只有两个模型的证据。
- 增益在更长生成或更大模型上是否保持？实验限于 7–8B 模型，L=512。
