---
title: "Lookahead Unmasking Elicits Accurate Decoding in Diffusion Language Models"
updated: 2026-10-09
---

# Lookahead Unmasking Elicits Accurate Decoding in Diffusion Language Models

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文 §1–6、参考文献、附录 A–C）。Figure 2、Figure 3 的数据点在文本提取中丢失，只能读到图注与正文描述；表格可读。

## Summary

问题：masked diffusion LM（MDM，如 LLaDA）的结果强依赖推理时的 unmasking 顺序。confidence / margin / entropy 等贪心策略只看单个 token 的局部确定性，早期一次错误 unmask 会级联，且没有恢复机制（§1, §2）。

方法：Lookahead Unmasking（LookUM），把采样改写为在 unmasking 顺序空间中的路径选择，不需要外部 reward model（§3.2, Algorithm 1）。
- Path generator：每步从高确定性候选池 P_t（N-best，或按阈值过滤的 certainty filtering）中采样 k 个 unmasking 集合。
- Verifier：对每条候选路径，按模型预测对被选位置采样 token，得到 look-ahead 状态，再用全序列平均负熵（或平均 confidence）打分，把它当作代理 reward。
- 选择：用 NIS（逐步 importance weighting）或 SMC（粒子重加权）从 k 条路径里重采样一条。额外开销是 k 倍前向，作者称 k=2–3 即接近最优，开销类似 classifier-free guidance（§3.3）。

动机性证据（§3.1, Table 1）：在 2000 题的算术数据上，人为引入局部错误后，后续预测熵从约 0.24–0.53 升到 1.61–1.82，confidence 从 0.93–0.97 降到约 0.82。Figure 2 称 GPT-4o 判定的句子级局部错误率比基线低约 10%。

主结果（Table 2）：LLaDA-8B-Instruct 与 LLaDA-1.5，长度 128/256，每步 unmask 2 个 token，六个基准（MBPP、HumanEval、GSM8K、MATH500、Countdown、Sudoku）。
- LLaDA 上 LookUM 在多数格子最优，例如 MBPP-256 为 36.2（基线最好 28.4），HumanEval-128 为 27.4（Margin 25.6），GSM8K-128 为 72.7（ReMDM 69.1）。
- LLaDA-1.5 上同样多数最优，例如 GSM8K-256 为 82.3（ReMDM 80.1），MBPP-128 为 45.0（PC-Sampler 42.8）。
- 作者称 base LLaDA + LookUM 可与 LLaDA-1.5 相当（"competitive or superior on several benchmarks"）。

分析：用 Qwen2.5-Math-PRM-7B 作外部 verifier，在 NIS/SMC、1/4/16 粒子下不如内在不确定性（Table 3，MATH500 约 22.6–26.6，GSM8K 约 68–69.5）。粒子数扩展（Figure 3）称提升到 2–4 条路径后饱和，MATH500 在 2 条最好，更多略降。消融（Table 4，长度 128）：负熵 verifier 在三个基准上不低于 confidence；NIS 优于 SMC（Countdown 31.3 对 23.1）；certainty filtering 在 GSM8K 最高（72.6）但 Countdown 掉到 19.5。

## Evidence and Limits

- 设置：LLaDA-8B-Instruct 与 LLaDA-1.5，2 张 A100，沿用 d1 的解码设置；默认 |P_t|=5、选 2 条路径、平均负熵 verifier。基线含 Confidence、Margin、Entropy、PC-Sampler、ReMDM。代码、论文均未给出多 seed 或方差。
- 一致性并非完全：Table 2 中 LookUM 在个别格子不是最优，如 LLaDA-1.5 的 Countdown-256 为 17.9，低于 Confidence 的 23.4 和 PC-Sampler 的 19.1；MATH500 的增幅很小（LLaDA-256 为 34.6 对 Margin 34.4）。
- 摘要"up to 4 points on HumanEval and GSM8K, 8 points on MBPP"与 §4.2 "HumanEval-128 提升 8 点"的说法不一致；表中 LLaDA HumanEval-128 相对最优基线只提升 1.8，对 Confidence 才是 7.9。
- 算力对比不公平的可能：LookUM 用 2–3 倍前向，基线按单路径；文中未做等算力对比（例如与 best-of-k 或其他 test-time scaling）。"LookUM 接近 LLaDA-1.5"仅在部分基准成立（如 MBPP、HumanEval 上 LLaDA-1.5 基线仍更高）。
- 局部错误率实验由 GPT-4o 判定，仅有图和约 10% 的描述，未给出绝对数值或判分可靠性；Table 1 的"错误"版本也由 GPT-4o 构造，序列仅 8 token，属受控小实验，不能直接证明真实解码中"高不确定性路径更易出错"的因果方向。
- 外部 PRM 比较中 PRM 为数学专用，仅测数学任务，且作者对"PRM 不适配噪声中间态"的解释未经验证。
- 附录 C.2 提到评估了 LLaDA、Dream、LLaDA-1.5，但正文未报告 Dream 结果；Figure 3 称"至 4 个粒子饱和"与摘要"2–3 条"表述略有差异。
- 论文自述局限（附录 A）：仅依赖输出分布，未用注意力等内部信号。

## Open Questions

- 不确定性低的路径不一定正确（自信但错误的路径会被偏好）；verifier 与最终正确性的相关性只在小规模算术实验和 GPT-4o 判分中检验，缺少对真实解码路径的直接统计。
- 在等前向预算下，LookUM 与其他 test-time 方法（best-of-k、更多 denoising steps、ReMDM 加大预算）的比较如何？
- 增益在其他 DLM（如 Dream）、更长序列、每步 unmask 更多 token 的设置下是否保持？论文未给出结果。
