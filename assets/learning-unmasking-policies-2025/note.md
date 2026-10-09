---
title: "Learning Unmasking Policies for Diffusion Language Models"
updated: 2026-10-09
---

# Learning Unmasking Policies for Diffusion Language Models

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、参考文献、附录 A–I 及附录 J 之前部分）；图均为文本抽取，只能看到坐标轴与图注，曲线数值多无法读出；附录 J 的表格未细读。

## Summary

问题：dLLM（如 LLaDA、Dream）的 unmasking 采样多用置信度启发式（Fast-dLLM 阈值法、high-confidence top-K）。这类方法需手调，且在 semi-AR（block length BL=32）之外性能明显下降：BL=256 的全扩散设定下，甚至低于随机 unmasking，也不随 NFE 增加而变好（§2.2, Figure 1, Figure 10）。

方法：把采样形式化为 MDP（§3.1）。状态是 prompt 加部分 mask 的序列，动作是逐位置的 0/1 unmask 向量，dLLM 冻结并作为环境，奖励只在生成结束时给出。策略是约 300K 参数的单层 transformer（AdaLN 注入时间步），输入只有各位置最大 token 置信度、mask 向量和时间步，输出每个位置的 Bernoulli 概率（§3.2, Appendix H/I）。用 GRPO 训练：组大小 8，dLLM 温度 0，advantage 不除标准差，无 KL（§3.3）。奖励为乘法形式 r·(1−(T−T̂)/T)^α，α 控制速度与精度的权衡。训练数据约 15,000 条 GSM8K+MATH 混合，单 epoch。

主要结果（LLaDA-8B-Instruct，L=256，指标为 NFE-精度 Pareto 曲线）：
- BL=32：与 Fast-dLLM 基本持平，高于 random 和 high-confidence；作者推测该设定下 Fast-dLLM 已接近最优（§4.1, Figure 4）。α=10 的策略在约 10 NFE 的低端优于 Fast-dLLM（附录 C.2 报告 GSM8K 约 38% 对 18%），原因是在最后一个 block 生成数字答案时放慢。
- BL=256（全扩散）：策略降幅最小；GSM8K 约 12 NFE 时约 50%，启发式不超过 30%（§4.2）。加 expert steering（训练时每组混入一条 Fast-dLLM λ=0.9, BL=32 的轨迹）后，中高 NFE 下 GSM8K 约 80%、MATH 约 35%，接近 semi-AR 水平。
- 迁移（§4.3）：LLaDA 训练的策略可直接用于 Dream，α=10 的除外；数学到代码迁移不完全，在 KodCode 上重训后差距缩小；L=256 训练的策略在 L=512 表现基本不变，基线进一步变差。τ=0.8 的非贪心解码下，pass@k 优于 Fast-dLLM（k=1 约 +0.98%，k=32 约 +2.56%，数据集平均）。
- 消融（§4.4）：加法奖励会 reward hacking，退化成一步全部 unmask；Bernoulli 与 Plackett-Luce（DPLS）相近；输入 top-50 置信度或用 300M 参数的 hidden-state 头均不优于仅用最大置信度；去掉时间步或 mask 输入都会掉点，去 mask 掉得更多。
- 开销：策略相对 8B 模型可忽略，墙钟时间曲线与 NFE 曲线几乎一致（Figure 12，A100）。

## Evidence and Limits

- 实验只覆盖 LLaDA-8B-Instruct 和 Dream-7B-Instruct，任务为 GSM8K、MATH-500、HumanEval、MBPP，生成长度 256（一处 512）。训练只用数学数据；代码迁移失败后需在 KodCode 上重训。
- 对比基线：random、high-confidence、Fast-dLLM；附录 B.2 加入 margin 与 EB sampler。"超过启发式"只在全扩散设定成立；semi-AR 下只是持平，且结论由 Pareto 曲线肉眼比较得出，未见显著性检验。
- 每个 α 只选两个训练种子中的一个（按训练 loss），测试种子为 3 个。图 15 显示同一 α 的不同种子得到的策略在精度和速度上有差异。
- 可控性弱：训练时的 α 不能平滑地调节速度，α≥4 要么收敛到 α=3 的策略，要么收敛到 α=10 的策略（附录 B.5）；α=10 训练不稳定。测试时缩放 Bernoulli 概率（β）可得到更平滑的前沿，在 MATH-500 约 25 NFE 时 20% 对 10%（Figure 6）。
- expert steering 引入训练不稳定，多个 α 塌缩到近乎相同的策略（§4.2）。语义上它让策略学到接近 Fast-dLLM 的 semi-AR 行为，所以全扩散下的提升部分来自专家示范，而不仅是 RL 自行发现。
- 作者自述的局限：每个 α 需单独训练，成本高；只训数学混合数据；未做 remasking 和多模态。
- 定性分析（附录 C）是少量样本（N=100）的观察，如 LLaDA 对 padding token 的置信度虚高使 Fast-dLLM 在全扩散下近似从右向左生成，这一解释是作者的推断，未经干预实验验证。
- 引用的 GitHub 地址在文本中为 apple/ml-rl-dllm；BibTeX 记录里写的是 apple-aiml-research/ml-rl-dllm，两者不一致。

## Open Questions

- semi-AR 下策略与 Fast-dLLM 持平，是启发式已近最优，还是策略的输入（仅置信度）限制了上限？论文没有给出上界参照。
- 全扩散下的增益有多少来自 RL，多少来自 expert steering 所示范的 semi-AR 行为？缺少"仅 semi-AR 专家、无 RL 更新"之类的对照。
- 乘法奖励下 α 与最终速度的映射为何非单调、易塌缩到两个极端，是否与奖励形状或 GRPO 无 std 归一化有关，文中未分析。
