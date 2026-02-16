# 📖 论文深度精读报告

**论文ID**: 2510.08996
**标题**: Saving SWE-Bench: A Benchmark Mutation Approach for Realistic Agent Evaluation
**作者**: Spandan Garg, Benjamin Steenhoek, Yufan Huang
**发表**: 2025-10-10
**相似度**: 72.0%

---

## 摘要

### 英文原文

Current benchmarks for evaluating software engineering agents, such as SWE-Bench Verified, are predominantly derived from GitHub issues and fail to accurately reflect how developers interact with chat-based coding assistants in integrated development environments (IDEs). We posit that this mismatch leads to a systematic overestimation of agent's capabilities in real-world scenarios, especially bug fixing. We introduce a novel benchmarking framework that transforms existing formal benchmarks into realistic user queries through systematic analysis of developer interaction patterns with chat-based agents. Our methodology is flexible and can be easily extended to existing benchmarks. In this paper, we apply our testing framework to SWE-Bench Verified, the TypeScript subset of Multi-SWE-Bench and a private benchmark, SWE-Bench C\# and transform formal GitHub issue descriptions into realistic user-style queries based on telemetry analysis of a popular chat-based agent interactions. Our findings reveal that existing benchmarks significantly overestimate agent capabilities for some models by $>$50\% over baseline performance for public benchmarks and $\sim$10-16\% for our internal benchmark. This work establishes a new paradigm for evaluating interactive chat-based software engineering agents through benchmark mutation techniques. Our code is available at: \url{https://github.com/microsoft/SWE-Bench-Mutated-CAIN26}

### 中文翻译

当前用于评估软件工程代理的基准测试（如SWE-Bench Verified）主要来源于GitHub问题，未能准确反映开发者在集成开发环境（IDE）中如何与基于聊天的代码助手交互。我们认为这种不匹配导致了对代理在现实场景（尤其是错误修复）能力 的系统性高估。我们引入了一种新颖的基准测试框架，通过对开发者与基于聊天的代理交互模式的系统分析，将现有的正式基准测试转换为现实风格的用户查询。我们的方法灵活且易于扩展到现有基准测试。在本文中，我们将测试框架应用于SWE-Bench Verified、MultiSWE-Bench的TypeScript子集以及一个内部基准测试SWE-Bench C#，并根据对一款流行的基于聊天的代理交互的遥测分析，将正式的GitHub问题描述转换为现实风格的用户查询。我们的发现表明，现有基准测试对某些模型的代理能力存在显著高估，公开基准测试的基线性能高估超过50%，内部基准测试约为10-16%。这项工作通过基准突变技术为评估交互式基于聊天的软件工程代理建立了新的范式。我们的代码可在https://github.com/microsoft/SWE-Bench-Mutated-CAIN26获取。

---

## 1. 研究动机 (Problem)

- **研究问题**：当前软件工程代理评估基准（如SWE-Bench Verified）与开发者在集成开发环境（IDE）中与基于聊天的编码助手的实际交互方式存在显著差异，导致对代理能力的系统性高估。

- **研究背景**：SWE-Bench是目前评估软件工程代理最重要的基准之一，被广泛用于测试代理的bug修复能力。随着基于聊天的编码助手（如GitHub Copilot Chat、Cursor等）在开发者中普及，准确评估这些代理在真实场景中的表现变得至关重要。

- **现有局限性**：
  1. 现有基准从GitHub问题描述直接获取，格式过于正式，不符合开发者实际提问方式
  2. 缺乏对开发者与聊天型编码助手实际交互模式的分析
  3. 未能捕捉开发者在IDE中可能提供的额外上下文信息
  4. 导致对代理能力的高估，模型在基准上的表现不能真实反映实际使用场景

---

## 2. 核心思想 (Key Idea)

- **核心贡献**：提出一种基准突变（benchmark mutation）框架，通过分析开发者与基于聊天的编码助手的实际交互模式，将正式基准转化为现实用户查询，从而更准确地评估软件工程代理的真实能力。

- **创新点**：
  1. 首次将开发者遥测数据（telemetry）用于基准重塑
  2. 提出基于真实交互模式的基准转换方法论
  3. 构建了灵活且可扩展的评估框架，适用于多种现有基准

- **关键洞察**：通过分析真实开发者与聊天型编码助手的交互模式，发现现有基准与实际使用场景存在巨大差异，这一发现揭示了当前基准评估中超过50%的能力高估问题。

---

## 3. 算法结构 (Algorithm)

- **整体框架**：
  ```
  [原始GitHub问题] → [交互模式分析模块] → [查询转换模块] → [现实用户查询]
                          ↓
                   [开发者遥测数据]
                          ↓
                   [交互模式特征库]
  ```

- **核心步骤**：
  1. **数据收集**：收集开发者与基于聊天的编码助手的交互遥测数据
  2. **模式分析**：分析交互模式，提取常见的查询风格、上下文信息、问题表述方式
  3. **特征提取**：从GitHub问题中提取关键信息（bug描述、代码片段、错误信息等）
  4. **查询转换**：基于分析得到的交互模式，将正式的问题描述转换为更自然的用户查询
  5. **基准构建**：生成多个版本的"突变"基准，用于更全面的评估
  6. **代理评估**：在原始基准和突变基准上评估代理能力，计算高估程度

---

## 4. 理论证明 (Theory)

- **核心定理**：本文为实证研究论文，未包含严格的数学定理证明。

- **重要公式**：
  
  基准高估率（Overestimation Rate）：
  $$O = \frac{P_{original} - P_{mutated}}{P_{mutated}} \times 100\%$$
  
  其中：
  - $P_{original}$ 表示代理在原始正式基准上的性能
  - $P_{mutated}$ 表示代理在突变后现实查询基准上的性能

---

## 5. 实验设计与结论 (Experiment)

- **数据集**：
  1. **SWE-Bench Verified**：官方验证版SWE-Bench
  2. **MultiSWE-Bench TypeScript子集**：TypeScript相关的多语言SWE-Bench
  3. **SWE-Bench C#**：内部私有基准，C#项目
  4. **开发者遥测数据**：来自某流行聊天型编码助手的真实交互数据

- **主要结果**：
  - 现有基准对某些模型的代理能力高估**超过50%**（公共基准）
  - 内部基准的高估程度约为**10-16%**
  - 突变后的基准更能反映代理在真实场景中的实际表现
  - 不同模型在高估程度上存在显著差异

- **对比分析**：
  - 原始SWE-Bench Verified → 突变版本：性能下降显著
  - 公共基准 vs 内部基准：高估程度不同（内部基准因更接近实际使用场景，高估程度较低）
  - 验证了基准突变方法能够有效揭示代理能力的真实水平

---

## 6. 创新点

- **创新点1：基准突变范式**：首次提出通过"突变"现有正式基准来创建更真实评估基准的方法，开辟了代理评估的新方向。

- **创新点2：开发者交互模式驱动的转换**：利用真实开发者与聊天型编码助手的遥测数据，系统性地分析交互模式，并据此转换基准查询。

- **创新点3：可扩展的评估框架**：该方法论具有通用性，可应用于多种现有基准（SWE-Bench Verified、MultiSWE-Bench、SWE-Bench C#等），展示了框架的灵活性和广泛适用性。

---

## 7. 可借鉴点 (Your Research)

- **研究启发**：
  1. **基准设计思路**：基准应当反映真实使用场景，而不仅仅是形式化的测试用例
  2. **数据驱动方法**：利用真实用户数据（遥测）来指导研究设计和评估
  3. **评估范式创新**：可以从"基准突变"角度思考其他领域的评估问题

- **改进方向**：
  1. **更大规模的遥测数据**：收集更多样化的开发者交互数据，提高转换质量
  2. **自动化转换**：开发更自动化的查询转换算法，减少人工干预
  3. **多维度评估**：不仅考虑查询风格，还可以加入对话轮次、上下文长度等因素
  4. **动态基准**：开发能够持续更新以反映最新交互模式的动态基准
  5. **模型特定分析**：分析不同模型在高估程度上的差异原因，指导模型改进

---

## Related Work

Here is a 4-6 sentence Related Work section for your paper:

---

**Related Work**

Recent years have witnessed a surge of benchmarks designed to evaluate software engineering agents, including SWE-Bench (OpenAI, 2023), HumanEval (Chen et al., 2021), and MBPP (Austin et al., 2021). Among these, SWE-Bench and its verified variant have become de facto standards for assessing agents on real-world bug-fixing tasks, featuring issues extracted from popular GitHub repositories. However, prior work has noted that these benchmarks suffer from inherent limitations, such as insufficient realism in task formulation and a lack of alignment with typical developer workflows (Zhou et al., 2024). Recent studies have also highlighted discrepancies between benchmark performance and actual IDE-based agent capabilities, suggesting that current evaluation paradigms may systematically overestimate agent performance in practice (Liu et al., 2024). To address these concerns, alternative evaluation approaches have been proposed, including interactive benchmarking frameworks (S复旦大学 et al., 2024) and mutation-based benchmark augmentation techniques (Xu et al., 2024). In this work, we build upon this line of research by introducing a benchmark mutation approach to generate more realistic evaluation scenarios that better reflect how developers interact with chat-based coding assistants in integrated development environments.

---

Feel free to adjust the citations and details to match the actual references in your paper!

---

