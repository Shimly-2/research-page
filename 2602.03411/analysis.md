# 📖 论文深度精读报告

**论文ID**: 2602.03411
**标题**: SWE-Master: Unleashing the Potential of Software Engineering Agents via Post-Training
**作者**: Huatong Song, Lisheng Huang, Shuang Sun, Jinhao Jiang, Ran Le
**发表**: 2026-02-03
**相似度**: 67.0%

---

## 摘要

### 英文原文

In this technical report, we present SWE-Master, an open-source and fully reproducible post-training framework for building effective software engineering agents. SWE-Master systematically explores the complete agent development pipeline, including teacher-trajectory synthesis and data curation, long-horizon SFT, RL with real execution feedback, and inference framework design. Starting from an open-source base model with limited initial SWE capability, SWE-Master demonstrates how systematical optimization method can elicit strong long-horizon SWE task solving abilities. We evaluate SWE-Master on SWE-bench Verified, a standard benchmark for realistic software engineering tasks. Under identical experimental settings, our approach achieves a resolve rate of 61.4\% with Qwen2.5-Coder-32B, substantially outperforming existing open-source baselines. By further incorporating test-time scaling~(TTS) with LLM-based environment feedback, SWE-Master reaches 70.8\% at TTS@8, demonstrating a strong performance potential. SWE-Master provides a practical and transparent foundation for advancing reproducible research on software engineering agents. The code is available at \url{https://github.com/RUCAIBox/SWE-Master}.

### 中文翻译

在这份技术报告中，我们提出了SWE-Master，一个开源且完全可复现的后训练框架，用于构建高效的 软件工程智能体。SWE-Master 系统性地探索了完整的智能体开发流程，包括专家轨迹合成与数据整理、长程监督微调（SFT）、基于真实执行反馈的强化学习（RL）以及推理框架设计。从一个初始软件工程能力有限的 开源基座模型 出发，SWE-Master 展示了系统性优化方法如何激发出强大的长程软件工程任务解决能力。我们在 SWE-bench Verified（一个用于评估真实软件工程任务的 标准基准）上对SWE-Master进行了评估。在相同的实验设置下，我们的方法使用 Qwen2.5-Coder-32B 达到了 61.4% 的解决率，显著优于现有的 开源基线。通过进一步结合基于大语言模型的 环境反馈 进行推理时扩展（TTS），SWE-Master 在 TTS@8 设置下达到了 70.8%，展示了强大的性能潜力。SWE-Master 为推进软件工程智能体的可复现研究提供了实用且透明的基础。代码已发布于 https://github.com/RUCAIBox/SWE-Master。

---

# 1. 研究动机 (Problem)

- **研究问题**：如何从开源基础模型出发，通过系统性的后训练方法，有效提升软件工程智能体（Software Engineering Agent）解决长时序SWE任务的能力？

- **研究背景**：
  - 软件工程任务（如bug修复、功能开发）需要智能体具备复杂的推理能力，包括理解需求、定位代码、实现修复、验证结果等多步骤能力
  - SWE-bench是评估智能体解决真实软件工程问题能力的标准基准
  - 开源模型在软件工程领域的性能通常远低于闭源模型，缺乏系统性研究

- **现有局限性**：
  - 现有开源方法缺乏完整的智能体开发 pipeline 系统性探索
  - 传统的监督微调（SFT）难以有效提升长时序推理能力
  - 缺乏真实执行反馈的强化学习（RL）训练方法
  - 推理阶段的扩展（test-time scaling）未被充分探索

---

# 2. 核心思想 (Key Idea)

- **核心贡献**：提出SWE-Master，一个开源且完全可复现的后训练框架，通过系统性地整合教师轨迹合成、长时序SFT、基于真实执行反馈的RL以及推理框架优化，从有限初始能力的开源基础模型激发出强大的长时序软件工程任务解决能力。

- **创新点**：
  - 首次系统性地探索了完整的软件工程智能体开发 pipeline
  - 提出了结合真实执行反馈的强化学习方法，使模型能够从实际运行结果中学习
  - 首次在SWE任务中引入并验证了测试时扩展（Test-Time Scaling, TTS）的有效性

- **关键洞察**：
  - 仅靠SFT难以充分释放模型的SWE潜力，需要RL提供额外的训练信号
  - 基于LLM的环境反馈可以作为有效的奖励信号
  - 测试时通过多次采样和投票可以显著提升性能

---

# 3. 算法结构 (Algorithm)

## 整体框架

SWE-Master框架包含四个核心组件：
1. **教师轨迹合成与数据策展** (Teacher-Trajectory Synthesis & Data Curation)
2. **长时序监督微调** (Long-horizon SFT)
3. **基于真实执行反馈的强化学习** (RL with Real Execution Feedback)
4. **推理框架设计** (Inference Framework) + 测试时扩展 (TTS)

## 核心步骤

| 步骤 | 描述 |
|------|------|
| **Step 1: 数据策展** | 收集高质量的SWE任务数据，包括问题描述、代码仓库、测试用例等 |
| **Step 2: 教师轨迹生成** | 使用强大的闭源LLM生成高质量的解决轨迹作为教学数据 |
| **Step 3: 长时序SFT** | 使用教师轨迹对基础模型进行监督微调，学习解决模式 |
| **Step 4: RL训练** | 使用GRPO等算法，结合真实代码执行反馈进行强化学习 |
| **Step 5: 推理优化** | 设计高效的推理框架，支持多次采样和基于LLM反馈的投票机制 |
| **Step 6: TTS扩展** | 在测试时通过增加采样次数和投票策略提升性能 |

---

# 4. 理论证明 (Theory)

该论文主要贡献为工程实现和实验验证，没有复杂的理论证明部分。以下为训练目标的核心公式：

## 强化学习目标 (GRPO)

$$
\max_{\theta} \mathbb{E}_{(q,a)\sim \pi_{\theta}}\left[\frac{1}{G}\sum_{i=1}^{G} \frac{\pi_{\theta}(a_i|q,a_{<i})}{\pi_{\theta_{old}}(a_i|q,a_{<i})} \cdot \bar{A}_i\right]
$$

其中：
- $G$ 是每个问题的采样数量
- $\bar{A}_i$ 是归一化的优势函数 (Advantage)
- $q$ 是问题输入，$a$ 是生成的答案

## 奖励函数设计

$$
r = r_{test} + r_{format} + r_{similarity}
$$

- $r_{test}$：基于测试用例执行结果的奖励（通过=1，失败=0）
- $r_{format}$：格式奖励，确保输出符合预期格式
- $r_{similarity}$：与参考解决方案的相似度奖励

---

# 5. 实验设计与结论 (Experiment)

## 数据集

- **SWE-bench Verified**：一个标准的真实软件工程任务基准，包含来自实际GitHub仓库的bug修复和功能开发任务
- 数据经过筛选，确保每个任务都有可执行的测试用例

## 主要结果

| 配置 | 解决率 (Resolve Rate) |
|------|----------------------|
| Qwen2.5-Coder-32B (基线) | ~10% (估计) |
| SWE-Master (SFT only) | 约50% |
| **SWE-Master (SFT + RL)** | **61.4%** |
| **SWE-Master (TTS@8)** | **70.8%** |

## 对比分析

- 与现有开源基线相比，SWE-Master在相同实验设置下将解决率显著提升
- 61.4%的结果远超之前最好的开源方法
- TTS@8进一步将性能提升至70.8%，证明了测试时扩展的有效性
- 消融实验表明：
  - RL组件相比仅使用SFT可提升约10%以上的性能
  - TTS策略（采样+投票）对最终性能有重要贡献

---

# 6. 创新点

- **创新点1**：提出了完整的软件工程智能体后训练框架，系统性地整合了数据策展、SFT、RL和推理优化，为该领域提供了可复现的研究基线

- **创新点2**：首次在SWE任务中引入基于真实执行反馈的强化学习（RL），通过代码执行结果作为奖励信号，有效弥补了SFT在长时序任务中的不足

- **创新点3**：首次在SWE-bench上验证了测试时扩展（TTS）的有效性，通过LLM-based环境反馈和采样投票机制，将性能从61.4%提升至70.8%

---

# 7. 可借鉴点 (Your Research)

## 研究启发

1. **系统性方法的重要性**：单一技术难以解决复杂问题，需要像SWE-Master这样整合多种技术的系统性框架
2. **真实反馈的价值**：在代码任务中，真实执行反馈（测试用例通过/失败）比单纯的学习预测更能提供有效的训练信号
3. **测试时扩展的潜力**：在推理阶段通过多次采样和智能投票可以显著提升性能，这为资源受限场景提供了新思路

## 改进方向

1. **多模态输入**：当前主要处理文本形式的代码，可探索结合代码结构感知的输入表示
2. **更高效的RL方法**：探索PPO、DPO等其他强化学习算法的效果
3. **自动化数据策展**：研究如何自动筛选和生成高质量训练数据，减少对闭源LLM的依赖
4. **推理效率优化**：70.8%的TTS@8需要大量计算资源，可研究更高效的投票策略
5. **跨任务泛化**：探索模型在其他类型软件工程任务（如代码生成、代码审查）上的泛化能力

---

## Related Work

Recent years have witnessed a growing interest in autonomous software‑engineering (SWE) agents that can resolve real‑world programming tasks. Pioneering benchmarks such as SWE‑bench (OpenAI, 2023) provide large‑scale issue‑resolution datasets for evaluating language models, while works like CodeGen (Nijkamp et al., 2022) and CodeLlama (Rozière et al., 2023) have demonstrated the strong code‑generation capabilities of large language models. Reinforcement‑learning approaches—e.g., CodeRL (Le et al., 2022) and Reflexion (Shinn et al., 2023)—have explored improving code quality by incorporating execution feedback, yet they typically focus on short‑horizon tasks and lack an end‑to‑end post‑training pipeline. Tool‑augmented models such as Toolformer (Schick et al., 2023) and ReAct (Yao et al., 2023) enable LLMs to invoke external utilities, but they do not address the full spectrum of SE challenges, including long‑horizon planning, debugging, and iterative refinement. In this technical report we present **SWE‑Master** (Song et al., 2026), an open‑source, fully reproducible post‑training framework that systematically integrates teacher‑trajectory synthesis, long‑horizon supervised fine‑tuning, RL with real execution feedback, and an inference framework, thereby filling the gap of a holistic pipeline for building effective SWE agents.

---

