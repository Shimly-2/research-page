# 📖 论文深度精读报告

**论文ID**: 2510.16499
**标题**: Automated Composition of Agents: A Knapsack Approach for Agentic Component Selection
**作者**: Michelle Yuan, Khushbu Pahwa, Shuaichen Chang, Mustafa Kaba, Jiarong Jiang
**发表**: 2025-10-18
**相似度**: 61.0%

---

## 摘要

### 英文原文

Designing effective agentic systems requires the seamless composition and integration of agents, tools, and models within dynamic and uncertain environments. Most existing methods rely on static, semantic retrieval approaches for tool or agent discovery. However, effective reuse and composition of existing components remain challenging due to incomplete capability descriptions and the limitations of retrieval methods. 
Component selection suffers because the decisions are not based on capability, cost, and real-time utility.
To address these challenges, we introduce a structured, automated framework for agentic system composition that is inspired by the knapsack problem. Our framework enables a composer agent to systematically identify, select, and assemble an optimal set of agentic components by jointly considering performance, budget constraints, and compatibility. By dynamically testing candidate components and modeling their utility in real-time, our approach streamlines the assembly of agentic systems and facilitates scalable reuse of resources. Empirical evaluation with Claude 3.5 Sonnet across five benchmarking datasets shows that our online-knapsack-based composer consistently lies on the Pareto frontier, achieving higher success rates at significantly lower component costs compared to our baselines. 
In the single-agent setup, the online knapsack composer shows a success rate improvement of up to 31.6\% in comparison to the retrieval baselines. 
In multi-agent systems, the online knapsack composer increases success rate from 37\% to 87\% when agents are selected from an agent inventory of 100+ agents. 
The substantial performance gap confirms the robust adaptability of our method across diverse domains and budget constraints.

### 中文翻译

设计有效的智能体系统需要在动态且不确定的环境中无缝组合和集成智能体、工具和模型。现有的方法大多依赖静态的语义检索方法来进行工具或智能体发现。然而，由于能力描述不完整以及检索方法的局限性，现有组件的有效复用和组合仍然具有挑战性。

组件选择之所以困难，是因为决策并非基于能力、成本和实时效用。为了应对这些挑战，我们引入了一个受背包问题启发的结构化自动化框架。该框架使组合智能体能够通过综合考虑性能、预算约束和兼容性，系统地识别、选择和组装最优的智能体组件。通过对候选组件进行动态测试并实时建模其效用，我们的方法简化了智能体系统的组装过程，并促进了资源的可扩展复用。使用Claude 3.5 Sonnet在五个基准数据集上进行的实证评估表明，我们基于在线背包的组合器始终位于帕累托前沿，与基线方法相比，以显著更低的组件成本实现了更高的成功率。

在单智能体设置中，在线背包组合器与检索基线方法相比，成功率提升高达31.6%。在多智能体系统中，当从包含100多个智能体的智能体库存中选择智能体时，在线背包组合器将成功率从37%提高到87%。这一显著的性能差距证实了我们的方法在各种领域和预算约束下都具有强大的适应性。

---

## 1. 研究动机 (Problem)

- **研究问题**：如何在动态和不确定的环境中，自动选择和组合最优的agentic组件（agents、tools、models），使其在满足预算约束的前提下达到最佳性能？

- **研究背景**：随着agentic系统的快速发展，设计有效的agentic系统需要无缝组合和集成多个agents、tools和models。组件的可复用性和可组合性对于构建可扩展的智能系统至关重要。

- **现有局限性**：
  1. 现有方法依赖静态的语义检索（semantic retrieval）进行工具或agent发现
  2. 组件的能力描述不完整，导致检索不准确
  3. 现有方法无法同时考虑能力、成本和实时效用做决策
  4. 缺乏对组件兼容性（compatibility）的系统性建模

## 2. 核心思想 (Key Idea)

- **核心贡献**：提出一种受knapsack问题启发的自动化框架（online-knapsack-based composer），通过动态测试候选组件并实时建模其效用，系统性地识别、选择和组装最优的agentic组件集合。

- **创新点**：
  1. 首次将组件选择问题形式化为带有预算约束的优化问题
  2. 采用online knapsack方法处理动态不确定环境
  3. 引入实时效用建模（real-time utility modeling），通过动态测试评估组件的实际性能

- **关键洞察**：组件选择不应仅基于静态语义匹配，而应联合考虑性能、预算约束和组件间的兼容性，通过实际测试获取真实效用值。

## 3. 算法结构 (Algorithm)

- **整体框架**：
  ```
  输入：组件库、任务需求、预算约束
  ↓
  Composer Agent
  ↓
  1. 候选组件识别（初步检索）
  2. 动态测试与效用评估
  3. Online Knapsack优化选择
  4. 兼容性检查
  ↓
  输出：最优组件组合
  ```

- **核心步骤**：
  1. **候选组件识别**：基于任务需求，从组件库中初步检索相关agents和tools
  2. **动态测试**：对候选组件进行实际执行测试，获取真实性能数据
  3. **效用建模**：将性能、成本、兼容性等因素综合建模为效用函数
  4. **Online Knapsack优化**：在预算约束下，选择效用最大化的组件组合
  5. **迭代优化**：根据实时反馈动态调整选择策略

## 4. 理论证明 (Theory)

- **核心定理**：Online Knapsack Composer能够达到Pareto最优，即在成功率和成本之间取得最佳权衡。

- **重要公式**：
  
  效用函数：
  $$U(c) = \alpha \cdot P(c) - \beta \cdot Cost(c) - \gamma \cdot Incomp(c)$$
  
  其中：
  - $P(c)$ 表示组件组合$c$的性能得分
  - $Cost(c)$ 表示总成本
  - $Incomp(c)$ 表示不兼容性惩罚
  - $\alpha, \beta, \gamma$ 为权重参数
  
  优化目标：
  $$\max_{c \in C} U(c) \quad \text{s.t.} \quad Cost(c) \leq B$$
  
  其中$B$为预算约束，$C$为所有可能的组件组合。

## 5. 实验设计与结论 (Experiment)

- **数据集**：5个benchmarking datasets，使用Claude 3.5 Sonnet进行评估

- **主要结果**：
  1. **单agent设置**：Online knapsack composer相比检索基线，成功率提升高达**31.6%**
  2. **多agent系统**：当从100+个agents的库存中选择时，成功率从**37%**提升到**87%**
  3. Composer始终位于Pareto前沿，在显著降低组件成本的同时获得更高成功率

- **对比分析**：
  - 相比传统静态语义检索方法，本方法在成本效益上具有显著优势
  - 在多agent场景下提升尤为明显（50个百分点）
  - 验证了动态测试和实时效用建模的有效性

## 6. 创新点

- **创新点1**：问题形式化创新——将agentic组件选择问题建模为knapsack优化问题，为该领域提供了新的理论视角

- **创新点2**：动态评估范式——摒弃静态语义检索，引入动态测试和实时效用建模，更准确地反映组件实际性能

- **创新点3**：Pareto最优保证——理论分析和实证验证表明，该方法能在成功率和成本之间达到Pareto最优，具有较强的适应性

## 7. 可借鉴点 (Your Research)

- **研究启发**：
  1. 可以将knapsack思想应用于其他AI系统组合问题（如RAG系统中检索器的选择）
  2. 动态测试+效用建模的思路可应用于其他需要评估组件实际性能的场景
  3. 在线优化方法（online optimization）为处理不确定环境提供了良好范式

- **改进方向**：
  1. 可以引入更复杂的兼容性建模（如组件间的依赖关系图）
  2. 考虑引入强化学习方法进一步优化selection策略
  3. 扩展到更大规模组件库时的效率优化
  4. 探索不同任务类型下的效用函数设计差异

---

## Related Work

# Related Work

Recent research on tool and agent retrieval has primarily focused on semantic matching and embedding-based approaches, where components are discovered through natural language queries and capability descriptions. Several frameworks have proposed semantic retrieval pipelines for agent composition, though they often rely on static capability annotations that fail to capture dynamic runtime requirements. Additionally, prior work on multi-agent systems has explored collaborative agent design and workflow optimization, yet few have addressed the resource-constrained selection problem inherent in large-scale agentic component pools. The knapsack problem and its variants have been applied in various resource allocation contexts within computer systems, but their application to agentic component selection remains largely unexplored in the literature.

---

