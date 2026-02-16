# 📖 论文深度精读报告

**论文ID**: 2506.19290
**标题**: Skywork-SWE: Unveiling Data Scaling Laws for Software Engineering in LLMs
**作者**: Liang Zeng, Yongcong Li, Yuzhen Xiao, Changshi Li, Chris Yuhao Liu
**发表**: 2025-06-24
**相似度**: 70.0%

---

## 摘要

### 英文原文

N/A

### 中文翻译

[翻译失败]

---

# Skywork-SWE 论文详细分析

## 1. 研究动机 (Problem)

- **研究问题**：如何系统性地扩展软件工程（Software Engineering, SWE）领域的大规模训练数据，并揭示数据规模与模型性能之间的 scaling laws 关系。

- **研究背景**：
  - SWE 已成为下一代 LLM 智能体的关键测试平台
  - SWE 任务要求模型具备两个关键能力：持续迭代问题解决能力（>50 轮交互）和长上下文依赖解析能力（>32k tokens）
  - 高质量的 SWE 数据对于训练强大的软件工程 LLM 至关重要

- **现有局限性**：
  - 当前 SWE 数据标注过程极度耗时，需要大量人工参与
  - 依赖人工进行代码文件过滤
  - 需要搭建专用的运行时环境来执行和验证单元测试
  - 现有公开数据集规模有限，仅有几千个来自 GitHub 的实例
  - 缺乏对数据 scaling laws 的系统研究

---

## 2. 核心思想 (Key Idea)

- **核心贡献**：提出增量式自动化的 SWE 数据策展管道，系统性扩展 SWE 数据集的规模和多样性，并基于此揭示大模型软件工程能力的数据 scaling 规律。

- **创新点**：
  - 设计了增量式、 自动化的数据策展管道，无需人工干预即可大规模生成 SWE 训练数据
  - 构建了包含 10,169 个真实世界 Python 任务实例的大规模数据集，来源自 2,531 个 GitHub 仓库
  - 每个任务配有自然语言任务描述和专用的运行时环境镜像，用于自动化单元测试验证
  - 首次系统性地研究 SWE 任务的数据 scaling laws，发现模型性能随数据量增加持续提升，无饱和现象

- **关键洞察**：
  - 在 SWE 任务上，模型性能与训练数据规模呈现持续增长的 scaling law，未观察到性能饱和
  - 即使不使用验证器或多轮采样，模型也能达到 38.0% 的 pass@1 准确率
  - 结合测试时扩展技术（test-time scaling），性能可提升至 47.0%，超越所有子 32B 参数模型

---

## 3. 算法结构 (Algorithm)

- **整体框架**：
  ```
  [GitHub 仓库采集] → [自动化任务筛选] → [运行时环境构建] → 
  [单元测试验证] → [训练轨迹策展] → [模型微调] → [性能评估]
  ```

- **核心步骤**：
  1. **数据采集**：从 GitHub 仓库中自动采集 Python 代码仓库
  2. **任务筛选**：自动化过滤有意义的 SWE 任务（包含问题描述和测试用例）
  3. **环境构建**：为每个任务构建专用的运行时环境 Docker 镜像
  4. **验证执行**：自动化执行单元测试，验证任务实例的正确性
  5. **轨迹策展**：收集成功通过测试的训练轨迹（>8,000 条）
  6. **模型微调**：基于 Qwen2.5-Coder-32B 进行微调
  7. **性能评估**：在 SWE-bench Verified 基准上评估模型性能

---

## 4. 理论证明 (Theory)

- **核心定理**：数据 scaling 定律（Data Scaling Law）

- **重要公式**：

  模型性能与训练数据规模的关系可表示为：

  $$\text{Performance} = a \cdot \log(N) + b$$

  其中：
  - $N$ 表示训练数据规模（成功验证的任务实例数量）
  - $a$ 和 $b$ 为拟合参数
  - 该关系表明性能随数据量对数增长，**未观察到饱和现象**

  Pass@1 准确率公式：
  
  $$\text{pass@1} = \frac{\#\text{tasks solved correctly in first attempt}}{\#\text{total tasks}} \times 100\%$$

---

## 5. 实验设计与结论 (Experiment)

- **数据集**：
  - **训练集**：10,169 个真实世界 Python 任务实例，来自 2,531 个 GitHub 仓库
  - **验证轨迹**：8,000+ 条成功通过运行时验证的训练轨迹
  - **测试集**：SWE-bench Verified 基准

- **主要结果**：
  - **无测试时扩展**：38.0% pass@1 准确率
  - **有测试时扩展**：47.0% pass@1 准确率
  - 基于 Qwen2.5-Coder-32B 构建，使用 OpenHands 智能体框架

- **对比分析**：
  | 模型 | Pass@1 准确率 |
  |------|--------------|
  | Skywork-SWE-32B (无 TTS) | 38.0% |
  | Skywork-SWE-32B (有 TTS) | 47.0% |
  | 之前 SOTA (子 32B) | <47.0% |

  **结论**：Skywork-SWE 建立了 Qwen2.5-Coder-32B 系列模型的新 SOTA，测试时扩展技术进一步将性能提升至超越所有子 32B 参数模型的水平。

---

## 6. 创新点

- **创新点1**：提出了增量式自动化的 SWE 数据策展管道
  - 突破传统人工标注的高成本限制
  - 实现了从 GitHub 仓库到可用训练数据的全自动化流程
  - 每个任务配有独立的运行时环境镜像进行验证

- **创新点2**：首次系统性揭示 SWE 任务的数据 scaling laws
  - 发现模型性能随数据规模增加持续提升，无饱和迹象
  - 为后续大规模数据构建提供了理论依据
  - 证明更大规模数据可能带来更强的 SWE 能力

- **创新点3**：实现了无需验证器或多轮采样的高效推理
  - 38.0% pass@1 已超越许多使用复杂策略的模型
  - 结合测试时扩展可达 47.0%
  - 为轻量级部署提供了可行方案

---

## 7. 可借鉴点 (Your Research)

- **研究启发**：
  - 数据策展的自动化是扩展大规模训练数据的关键路径
  - 运行时环境验证对于确保数据质量至关重要
  - 关注数据 scaling laws 研究可指导后续数据投入决策
  - 测试时扩展技术是提升模型性能的有效手段

- **改进方向**：
  - **多语言扩展**：当前仅支持 Python，可扩展到 Java、JavaScript 等其他语言
  - **更复杂任务**：增加涉及多文件重构、架构设计等复杂度的任务
  - **数据质量提升**：引入代码审查、静态分析等进一步过滤低质量数据
  - **模型架构探索**：尝试不同的基础模型和智能体框架组合
  - **推理效率优化**：在保持性能的同时降低计算开销

- **可迁移的方法**：
  - 自动化数据策展管道的设计思路
  - 运行时环境镜像的构建方法
  - 数据 scaling laws 的实验方法论
  - 训练轨迹的筛选和策展标准

---

## Related Work

# Related Work

Recent years have witnessed growing interest in applying large language models (LLMs) to software engineering (SWE) tasks, with benchmarks such as SWE-bench and HumanEval establishing standardized evaluation frameworks for measuring model performance on real-world coding challenges [references]. Prior research has demonstrated that LLM agents can effectively tackle complex software development tasks through iterative reasoning and tool use, though significant challenges remain in sustaining long-horizon problem-solving across extended interaction sequences [references]. Studies on scaling laws have primarily focused on general language modeling capabilities, revealing predictable relationships between compute, data, and model performance; however, analogous investigations in the SWE domain remain limited due to the scarcity of high-quality training data [references]. Efforts to automate SWE data curation have explored synthetic dataset generation and automated test case creation, yet these approaches often fail to capture the full complexity of real-world software repositories and their dependency structures [references]. Our work on Skywork-SWE extends this line of research by systematically investigating data scaling laws specific to software engineering capabilities in LLMs, providing insights into how training data volume and quality influence model performance on sustained iterative problem-solving and long-context dependency resolution.

---

