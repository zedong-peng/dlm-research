---
title: "Accelerated Sampling from Masked Diffusion Models via Entropy Bounded Unmasking"
updated: 2026-10-09
---

# Accelerated Sampling from Masked Diffusion Models via Entropy Bounded Unmasking

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文 + 附录 A–D），含参考文献。图为文本提取，坐标轴数值只能读到刻度，曲线上的具体点读不到；正文外的数字仅来自 Table 1 与附录 Table 3–6。

## Summary

问题：masked diffusion model (MDM) 的最佳采样器（按 confidence / entropy / margin 排序、每步只解一个 token）NFE 高；固定 Top-k 并行解多个 token 会因为独立采样迅速掉点 (§3.1, §3.2, Fig. 2–3)。

方法：EB-Sampler，不需重训，直接替换采样器。每步先按已有的误差代理（confidence / entropy / margin）给 masked token 升序排序，再取最大前缀 U，使 sum H − max H ≤ γ (Eq. 2)。γ=0 退化为每步一个 token，γ=∞ 一步全解。实现相对 Top-k 只多几行 (Fig. 4, Alg. 1)。

理论（§5, 附录 A）：把一族自适应多 token 采样器写成有序划分上的分布 ϕ，KL(q, p_ϕ) 的上界分解为 model error 与 joint dependence error。后者是联合分布与边缘乘积之间的 KL，上界为 sum H − max H (Eq. 8)。若所选 token 的 model error 可忽略，则 Eq. 2 近似控制总误差。

结果（§6）：
- LLaDa 8B Base、Dream 7B Base，HumanEval / MBPP / GSM8K / MATH，在 generate_until 口径的 NFE 下，EB 在 accuracy–NFE 前沿上优于 Top-k；文中称同精度下比 Top-1 快 2–4x (Fig. 5)，摘要与结论取 2–3x。
- Table 1（Dream 7B, MBPP）：Top-1 pass@1 58.8%，NFE 101.71；EB entropy γ=0.1 为 58.0%，NFE 25.49（3.99x）；加 semi-AR 后 58.6%，21.19 NFE（3.05x）。
- 小模型逻辑题：6M 参数 discrete DiT，10x10 迷宫、9x9 Sudoku（各 48K 训练 / 2K 验证）。EB 在 NFE 降到约 5（迷宫）、10–15（Sudoku）前基本保持精度，Top-k 在迷宫约 10 NFE 就明显下降 (Fig. 6–7)。

## Evidence and Limits

- 设置：8×H100；零温度；Top-k 基线用 k∈{1,2,4,8,16}；评测为 pass@1。Dream 的 HumanEval 需后处理提取函数体，否则比官方低约 8%，处理后差距 <2%；GSM8K / MATH 版本与官方不同，Top-1 有小差距 (§6.1, 附录 C.1.3)。所有对比均在同模型同评测内。
- 作者自己指出 NFE 度量有偏：MBPP 上模型会在结束符之后继续生成，使 γ=0 的 NFE 偏高，所以 6x 的数字不能当作真实加速，改用 semi-AR 块生成 (block_len=64) 得到 2–3x 的估计 (Table 1)。
- 加速随任务与口径变化很大：GSM8K 上 generate_until 下约 2–2.7x（Table 5–6，LLaDa 约 2.07x），LLaDa MBPP 加 semi-AR 后 γ=0.1 为 2.21x 且 pass@1 从 39.4% 降到 38.8% (Table 4)。“无损”主要对应较小 γ，较大 γ 有轻微掉点。
- 加速用 NFE 衡量，没有报告 wall-clock；每步多出的排序和 cumsum 开销未讨论。Table 3 只给了 Top-1 评测耗时。
- 理论是上界，且依赖“所选 token 的 model error 可忽略”这一假设，由经验上排序代理有效来支撑，未验证；entropy 界用的是模型预测熵，不是真实数据熵。
- 对比的只有 Top-k 同类采样器，没有与 remasking / planning、speculative decoding 等做实验对比。逻辑题模型很小（6M），迷宫上三种代理几乎没有差别。
- 作者承认：只做 unmask 不回改已解 token；未学习参数化的自适应采样器。

## Open Questions

- 熵界 γ 如何随任务、模型、序列长度选取？文中只扫了一组值，没有给出不用扫参的选法。
- 在 wall-clock 和更长生成（含 KV cache 近似、块缓存等）下，NFE 减少能转成多少实际加速？
- 模型在结束符之后的“浪费”生成是否有更根本的处理方式，使 NFE 度量不依赖 semi-AR 这类额外设定？
