---
title: "KLASS: KL-Guided Fast Inference in Masked Diffusion Models"
updated: 2026-10-09
---

# KLASS: KL-Guided Fast Inference in Masked Diffusion Models

阅读者：Claude Sonnet 子 agent（2026-10-09），依据归档 PDF 的抽取文本；未经人工复核。

阅读范围：全文 PDF 文本（正文、附录 A–G、参考文献）。Figure 1/2/3 为文本抽取，图中数值部分可读；附录中的 Algorithm 1 可读。未见公式或表格严重乱码。

## Summary

问题：masked diffusion LM 常用 Top-k / 置信度采样，每步只解开固定数量的 token，慢且易过早确定错误 token。

方法（§4）：KLASS（KL-Adaptive Stability Sampling），training-free。每步对每个 masked 位置算两个量：置信度 conf = max p_t(v)，以及与前 n 步预测分布的 KL（KL score）。若最近 n 个 KL 都低于 ε_KL 且 conf > τ，该位置判为 stable 并同时解开；若没有 stable token，则回退为解开 top-u 置信度 token（Eq. 7–8，Algorithm 1）。KL 复用已有 logits，不需额外前向，只缓存上一步分布。

理论（§5，附录 A）：若模型对条件分布是 δ-近似，且某 token 在当前上下文偏向次优值、在近最优上下文偏向最优值，则沿上下文路径的平均逐步 KL 有下界 2Δ²/M²（Pinsker + Cauchy-Schwarz）。据此论证"错误 token 不能保持稳定"。

主要结果：
- 推理（§6.1, Table 1）：LLaDA-8B-Instruct 与 Dream-7B-Instruct，生成长度 256。LLaDA 上 MATH 33.8（Top-1 31.4）、GSM8K 76.50（75.13）、HumanEval 40.85（39.63）、MBPP 47.86（46.69），步数约 92–129，对应 256 步基线。Dream 上 MATH 43.20（37.97），其余 GSM8K/HumanEval/MBPP 与 Top-1 基本持平或略高，步数约 75–156。
- 墙钟（附录 D.1.3, Table 8，单卡 RTX A5000）：相对 Top-1 加速 1.32×–2.78×，最高 2.78× 出现在 Dream HumanEval。
- 消融：只用 conf 或只用 KL 阈值均更差（Table 1、Fig. 3）；同一稳定集合内单 token 解开比并行差（Table 5，MATH 31.2/29.0 vs 33.8）。
- 其他模态：MDLM/OpenWebText 无条件生成，MAUVE 0.179 vs 0.115，LLaMA2 ppl 26.94 vs 30.88（Table 2）；MMaDA 图像 FID 30.48 vs 34.48（16 步，Table 3）；QM9 分子 NFE 32→18.8 且 QED 0.526→0.546（Table 4）。
- 开销（§6.6, Table 6）：每步额外时间约 0.0002s，显存约 250–300MB。

## Evidence and Limits

- 模型规模仅 7–8B 级；文中自述无更大的 diffusion LM，无法测 agent 类任务（附录 G.1）。
- 阈值按模型 × 数据集分别设定（Table 7），用约 100 个验证样本的三步搜索（附录 D.1.2）。基线中的 confidence>0.9、KL<0.001 阈值是固定的，没有同等调参，对比不完全对等。
- Fig. 3 的阈值网格直接在 MATH 上报告准确率，且 Dream 在 MATH 上的最优点附近有明显波动（如 0.001 行与 0.005 行差几个点）；"对超参不敏感"的结论只是定性判断。
- Dream 用温度 0.2，LLaDA 用温度 0。Dream 三次运行有 std（Table 9），其中若干项 std 为 0.00，GSM8K 上 KLASS 79.43±0.72 与 Top-1 79.55 无差别。LLaDA 为确定性，无显著性检验。MATH500、HumanEval、MBPP 样本量小，1 点以内的提升不宜过读。
- 步数收益在 Dream 的 GSM8K 上不存在（155.67 步 vs 256 步，准确率持平），且 KLASS 步数多于纯置信度阈值法（Table 1），后者更快但准确率明显更低。加速是相对 Top-1 而言，未与 Fast-dLLM、EB-Sampler、Prophet 等同期 training-free 方法做表格对比（只在相关工作中提及，附录 E 对比 Top-k Margin 与 Entropy）。Entropy 在 LLaDA 的 MATH、MBPP 上 256 步时准确率高于 KLASS（Table 18）。
- 文本/图像/分子实验为保持固定步数而限制每步最大解开数（附录 D.2、D.3），因此这三项主要体现质量而非加速；仅分子实验报告了 NFE 降低。图像 KLASS 配置为 ε_KL=0.3、τ=0.1，与推理任务差别极大。
- 数字不一致：Dream Top-1 在 MATH 上 Table 1 为 37.97，Table 17 为 38.10，Table 8 为 38.00；GSM8K 上为 79.55 与 79.75；Dream MATH 的 KL 阈值 Table 7 记 0.005，而 Table 16 中 43.2 对应 conf 0.9、KL 0.015、n=2。附录 D.1.3 文字中的"47.4%/16.1%"与 Table 8 数值也不易对上。
- 理论命题只说明"在路径上由错变对的 token 必然有较大平均 KL"，是必要条件而非"低 KL 即正确"；假设（δ-近似、路径存在、margin）较强，不涉及实际 stable 判据的误差。

## Open Questions

- 该判据在更大 diffusion LM 或更长生成长度、非 semi-autoregressive block 设置下是否仍有效，阈值能否跨任务迁移而不逐集调参？
- 与同期 Fast-dLLM、EB-Sampler、Prophet 等在相同模型、相同步数/墙钟预算下的直接对比如何？
- 准确率提升有多少来自"延迟解开不稳定 token"，有多少来自并行解开本身与阈值调参（尤其是 LLaDA 上确定性单次结果）？
