---
title: "Attention-Discounted Adaptive Sampler for Masked Diffusion Language Models"
updated: 2026-10-09
---

# Attention-Discounted Adaptive Sampler for Masked Diffusion Language Models

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、附录 A–F，含详细结果表 10–15）。Figure 2/3 的曲线只有轴刻度文字，曲线本身无法读出，相关结论只能依据表格和正文。Table 13 末行（f=20）有残缺。

## Summary

问题：masked diffusion LM 每步并行揭开多个 token，Top-k、Fast-dLLM、EB-Sampler 都按 token 级 confidence 排序，只在"何时停止"上不同，没有考虑被同时选中的位置之间的依赖，高并行时质量会骤降。

方法 ADAS（训练无关）：只改子集构造的排序，不改基础 sampler 的停止/准入规则。贪心地逐个加入候选 i，边际效用为 u(i|S) = c_i − α Σ_{s∈S} A_is (1 − c_s)（Eq. 3），A 为最后一层 self-attention 的 head 平均，c 为 confidence，α 全局固定为 40。注意力保持连续，作为软惩罚；对比 DAPD 把注意力阈值化成图、取独立集的硬约束。每次新增 s 后可增量更新，选择开销 O(|S||M|)。

诊断（§4）：在合成算术谓词上，强制 Dream-7B-Base 同时预测同一谓词内的两个被遮挡数字，准确率 71% 降到 30%（不同谓词时为 71%）。最后一层注意力在依赖对上更高（LLaDA 0.007261 vs 0.001919，Dream 0.009517 vs 0.004842，Table 9）。

结果（§6.2, Table 1）：LLaDA-8B-Base 与 Dream-7B-Base，GSM8K/MATH500/HumanEval/MBPP。取基线每步平均 ≥4 token 的操作点，在相同 NFE 处插值 ADAS 曲线比较，平均提升 +9.11（LLaDA）/ +10.46（Dream）个点；90 个匹配点中 80 个为正，bootstrap 均值 +9.27，95% CI [+7.74, +10.84]（Table 7）。代码任务增益更大（MBPP 最高），MATH500 最小。例如 Dream、Top-k、k=16：HumanEval 7.93→15.24，MBPP 2.00→26.80（Table 14）。每次 forward 额外开销 3.1%（§6.3）。

## Evidence and Limits

- 设置：8-shot GSM8K、4-shot MATH500、3-shot MBPP、0-shot HumanEval，temperature 0，生成长度 256/512；4×GH200 节点。基线含 Top-k、Fast-dLLM、EB-Sampler，另比较 KLASS 和 DAPD。
- 提升集中在低 NFE 区域；低并行时基本持平。详细表里也有退步：Dream、Top-k、k=2 时 GSM8K 64.67→58.15，LLaDA 同设置 64.22→59.67；EB 在 γ=1、3 时 Dream GSM8K 也略降。"一致提升"需要限定在高并行段。
- 匹配 NFE 是靠插值得到的，且 ADAS 曲线有时 NFE 更高（如 Fast-dLLM 表中同阈值下 AD 的 NFE 更大）；Fast-dLLM+AD 额外多扫了 f=20。基线点落在 ADAS 曲线范围外的被排除。
- α 在 LLaDA HumanEval 的 6 个设置上扫描后选 40（Table 4），同一 benchmark 的测试集上选的超参，之后全局沿用；Top-k k=16 时各 α 都很低（最高 7.32）。
- 消融：用 1−c_s 优于全局平均不确定性（20.12 vs 17.07）；最后一层注意力优于首层/中间层（Table 6）。均为单模型、单任务、单设置、单次运行。
- 理论（附录 B）只是局部一阶近似，自行假设 ‖J_is‖² ≈ L_i² A_is，不提供保证；论文自己说效用非 submodular，无近似保证。
- 对 KLASS/DAPD 的比较：超参未按任务调，只用了 4–5 组预设网格，且论文把二者的退化解释为"可能"原因，未验证。
- 稳健性区间是对操作点做 bootstrap，不是多 seed，也不是逐样本检验；操作点之间相关（作者自己说明）。
- 局限（作者陈述）：attention 只是依赖的代理；仅两个 base 模型、四个 benchmark；需要关掉最后一层的 FlashAttention 才能取注意力。

## Open Questions

- 注意力与真实联合依赖误差（multi-information）的相关性只在合成算术上量化过，在自然文本、代码上是否成立没有直接测量。
- 增益有多少来自"依赖感知"，多少来自把惩罚项变成通用的位置/不确定性偏置（例如对相邻位置或低 confidence 的抑制）？论文没有与非注意力的距离惩罚等简单替代对照。
- 在 instruct 版模型、更长生成、更大模型上，以及与学习式 unmasking policy 比较时，增益是否还在？
