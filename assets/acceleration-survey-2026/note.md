---
title: "Accelerating Masked Diffusion Large Language Models: A Survey of Efficient Inference Techniques"
updated: 2026-10-09
---

# Accelerating Masked Diffusion Large Language Models: A Survey of Efficient Inference Techniques

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、结论、参考文献）；无附录。文中图 1 仅有文字标签残留，表 1、表 2 可读。全文没有实验数据表。

## Summary

这是一篇关于 masked / discrete diffusion LLM（dLLM）推理加速的综述（arXiv 2607.12829，KAIST / Yonsei）。出发点是：并行生成不等于实际加速，而现有对比常把算法、架构、系统三类因素混在一起，难以看出收益来自哪里。

核心工具是一个延迟分解式 (Eq. 1)：
Latency(L_in, B) ≈ Σ_t [ G_t · C_fwd(L_in, B) + C_policy(t; L) ] + C_sys(L_in, B)。
其中 T 为精炼步数，G_t 为第 t 步的前向次数（多遍解码时大于 1），N_fwd = Σ G_t 为总前向次数，C_fwd 为单次前向成本，C_policy 为调度/策略开销，C_sys 为系统开销（§3）。文章强调只报 T 是误导。

据此把加速方法分为三类，并在表 1 中标注每类主要影响哪几项：
- 算法（§4）：调度与策略（非均匀/dilated 调度、置信度/熵驱动 unmasking、早停、学习式 unmasking 策略）；解码算法（speculative draft-and-verify、block/set decoding、diffusion-AR 混合）；蒸馏与 consistency（目标是降低 T）。
- 架构与系统（§5）：稀疏注意力、轻量 denoiser、量化；diffusion-aware KV/activation 缓存（跨步复用，带选择性刷新与淘汰，代价是显存）；推理框架与生产服务。
- 推理时扩展（§6）：guidance 作为多遍解码，以及 particle/SMC、tree search、约束解码，主要抬高 G_t 和显存。

§7 给出四步实践流程（固定负载 -> 显式给出 T 与 G_t -> 针对主导项选方法 -> 在匹配质量的小前沿上比较）、表 2 的场景到方法对照（低延迟 / 吞吐 / 质量优先），以及报告清单：T 与 G_t、N_fwd、草稿与验证的接受率、更新稀疏度 |Δt|/L、p95 延迟、峰值显存。§8 列出开放问题：few-step 解码的鲁棒性、缓存有效性的保证、更新稀疏与 kernel 的协同设计、计算自适应扩展。

## Evidence and Limits

- 论文明确说明不引入新 benchmark，也不报告新测量（§7）。因此全文没有任何速度或质量数字，所有"权衡"结论（如缓存换显存、策略开销可能抵消收益）都是从被引文献和分析框架推出的定性陈述，没有在统一设置下验证。
- Eq. 1 是近似式，各项（尤其 C_sys）没有给出具体的测量或拟合方法。分类表中的箭头只是"典型方向"，未用数据支撑。
- 摘要和贡献列表中称提供"可复现 benchmarking 的指南"，但 §7.2 自己说是"不规定具体测量协议"，实际给的是披露项清单，而不是协议。
- 文献覆盖限于 masked/discrete dLLM，不含 continuous-embedding 路线（如 Diffusion-LM）；只讲推理效率，不讲训练效率。文章自述领域变化快，缺少标准化的 dLLM 推理效率 benchmark，跨论文比较受模型规模、评测协议、硬件差异干扰。
- 未说明文献检索与筛选方式，也没有给出纳入标准。被引工作大多是 2025 年的 arXiv 预印本。
- 全文没有给出官方代码或资源链接。

## Open Questions

1. Eq. 1 中 C_policy 与 C_sys 在实际系统里各占多大比例、随 batch 和长度如何变化，论文没有任何测量，分解框架能否真正区分各项收益仍未验证。
2. 更新稀疏度 |Δt|/L 被推荐为披露指标，并被缓存方法隐含依赖，但文中没有说明它在不同模型、任务和调度下的典型取值范围。
3. 缓存复用的"正确性保证"和失效判据只作为开放问题提出，没有给出现有方法在这一点上的比较。
