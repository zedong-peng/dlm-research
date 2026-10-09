---
title: "Discrete Diffusion in Large Language and Multimodal Models: A Survey"
updated: 2026-10-09
---

# Discrete Diffusion in Large Language and Multimodal Models: A Survey

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、参考文献、附录 A–F；附录公式部分仅浏览和抽查）。图 1–8 为文字抽取，图中内容靠图注理解，部分公式有乱码；论文没有汇总性的结果表。

## Summary

这是一篇关于离散扩散语言模型（dLLM）和离散扩散多模态模型（dMLLM）的综述（v5，2025-09），作者来自 NUS。出发点是自回归（AR）模型的三个局限：逐 token 解码难并行、难做结构化控制、因果注意力只能一次性感知输入。综述认为离散扩散提供了并行解码、更好的可控性（长度、格式、模板）和动态感知（双向注意力）三种性质（§I）。

结构如下：
- 数学基础（§II）：D3PM 的转移矩阵（uniform / absorbing）、简化的 masked diffusion（式 13–17，等价于只在 masked 位置的加权交叉熵）、连续时间 CTMC、concrete score、Discrete Flow Matching。
- 建模变体（§III）：Block Diffusion、FlexMDM（可变长度）、Partial Masking、DDOT。
- 代表模型（§IV，图 1 为时间线）：约 1B 以下的早期模型（D3PM、DiffusionBERT、SEDD、MDLM、MD4 等）；大规模 dLLM（DIFFUSION-LLMs 3B/10B、LLaDA、DiffuGPT/DiffuLLaMA 127M–7B、Dream 7B、DiffuCoder、Seed Diffusion 等）；dMLLM（Dimple、LaViDa、LLaDA-V）；统一模型（MMaDA、FUDOKI、Muddit）。
- 训练（§V）：指出语料利用率低、时间步随机采样导致的覆盖缺口；介绍 AR/BERT 初始化、complementary masking、masking schedule（linear / cosine / spindle / blockwise）、token 重加权、蒸馏（Di4C）、RL（UniGRPO、VRPO、SDPO、wd1、DCoLT）。
- 推理（§VI）：unmasking 策略（置信度/margin/熵、confident decoding、block-wise、DUS）、remasking、prefilling 与 KV-cache（dKV-Cache、dLLM-Cache、DualCache）、guidance、采样（时序自一致性、Particle Gibbs、early stopping）、上下文扩展、稀疏计算、长度控制（DAEDAL）。
- 其余：量化（§VII）、隐私与安全（§VIII，DIJA/PAD 越狱、MOSA）、应用（§IX：语言、推理、视觉、机器人/驾驶、图结构、生物分子）、未来方向（附录 F：基础设施、推理效率、安全与隐私）。

文中引用的主要数字：摘要称工业级与开源 d(M)LLM 性能与 AR 相当，推理最多快 10×；Mercury / Gemini Diffusion 约 1000 token/s（§I）；Seed Diffusion 在 H20 上 2,146 token/s（§IV.B.7）；缓存在更新间隔 2–8 时性能损失小、约 10× 加速，prefilling 对 dMLLM 约 2×–7× 加速（§VI.C）；Dimple 的 confident decoding 把迭代数降到约 response length 的三分之一（§IV.C.1）。

## Evidence and Limits

- 这是综述，没有自己的实验、基线或统一对比；所有数字都转引自原论文或厂商介绍，未在统一设置下复核。没有横向对比表，读者难以判断各方法之间的相对收益。
- 摘要和引言中的强结论（"性能与 AR 相当"、"最多 10× 加速"、代码/规划/Sudoku 上优于 AR、数据受限时 dLLM 优于 AR）主要基于少数来源：Mercury 和 Gemini Diffusion 是闭源，只有厂商报告；"data-constrained 下优于 AR"来自 [14]。综述没有讨论这些结论对设置的依赖，也没有说明 AR 对照模型的规模和训练预算是否匹配。
- 10× 加速的说法在缓存部分（§VI.C）也出现，但缓存对双向注意力本就不是无损的，文中自己指出了这一点；速度与质量的折中没有量化汇总。
- 覆盖面偏广，应用一节（§IX）多为一两句话的论文罗列，且混入大量与 LLM 关系不大的连续/图扩散工作，深度有限。分类有一定主观性（如 "DIFFUSION-LLMs" 被称为首个 scaled 工作）。
- 明确提到的局限：缓存非无损、训练监督稀疏（§V.A）、纯扩散训练不稳定需 AR 预热（Dimple）、固定画布长度、dLLM 难以做实时内容审核、基础设施不成熟（附录 F）。
- 论文趋势图（图 8）用 arXiv 关键词计数说明热度上升，其中含"predicted trend"，属于外推，不是证据。

## Open Questions

- 在相同数据、参数量与算力下，masked diffusion 与 AR 的差距究竟有多大？综述汇总的"相当/更好"结论没有给出受控对比。
- 并行解码的加速在批量服务、长上下文和 KV-cache 非无损的条件下能保留多少，质量代价如何度量？
- dLLM 的安全对齐（中间 token 脆弱、并行解码难以逐 token 拦截）是否有系统性的防御方案，文中只给出了攻击和一个 RL 对齐方法（MOSA）。
