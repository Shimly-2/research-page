# 📖 论文深度精读报告

**论文ID**: 2512.17419
**标题**: SWE-Bench++: A Framework for the Scalable Generation of Software Engineering Benchmarks from Open-Source Repositories
**作者**: Lilin Wang, Lucas Ramalho, Alan Celestino, Phuc Anthony Pham, Yu Liu
**发表**: 2025-12-19
**相似度**: 78.0%

---

## 摘要

### 英文原文

Benchmarks like SWE-bench have standardized the evaluation of Large Language Models (LLMs) on repository-level software engineering tasks. However, these efforts remain limited by manual curation, static datasets, and a focus on Python-based bug fixes. We introduce SWE-Bench++, an automated framework that generates repository-level coding tasks from open-source GitHub projects. Unlike synthetic approaches, our pipeline harvests live pull requests to cover both bug fixes and feature requests across 11 languages from open-source GitHub repositories. SWE-Bench++ turns GitHub pull requests (PRs) into reproducible, execution-based tasks via four stages: programmatic sourcing, environment synthesis, test oracle extraction, and quality assurance. A final hint-guided trajectory synthesis step converts instances that strong models fail to solve into training trajectories. Our initial benchmark consists of 11,133 instances from 3,971 repositories across 11 languages. On a subset of 1,782 instances of this benchmark, today's strongest models perform as follows: \texttt{claude-sonnet-4.5} achieves 36.20\% pass@10, \texttt{gpt-5-2025-08-07} 34.57\%, \texttt{gemini/gemini-2.5-pro} 24.92\%, and \texttt{gpt-4o} 16.89\%. We further demonstrate the utility of our dataset by showing that fine-tuning on SWE-Bench++ instances yields measurable improvements on the SWE-bench Multilingual benchmark. SWE-Bench++ provides a scalable, multilingual benchmark for evaluating and improving repository-level code generation.

### 中文翻译

像SWE-bench这样的基准测试已经将大型语言模型（LLMs）在仓库级软件工程任务上的评估标准化了。然而，这些工作仍然受到人工策划、静态数据集以及仅关注Python错误修复的限制。我们推出了SWE-Bench++，这是一个从开源GitHub项目生成仓库级编码任务的自动化框架。与合成方法不同，我们的管道从开源GitHub仓库获取真实的pull请求，涵盖11种语言的错误修复和功能请求。SWE-Bench++通过四个阶段将GitHub pull请求（PRs）转化为可复现的、基于执行的任务：程序化获取、环境合成、测试预言提取和质量保证。最后的提示引导轨迹合成步骤将强模型无法解决的实例转化为训练轨迹。我们的初始基准测试包含来自3,971个仓库的11,133个实例，涵盖11种语言。在该基准测试的1,782个实例子集上，当今最强的模型表现如下：\texttt{claude-sonnet-4.5}达到36.20\%的pass@10，\texttt{gpt-5-2025-08-07}为34.57\%，\texttt{gemini/gemini-2.5-pro}为24.92\%，\texttt{gpt-4o}为16.89\%。我们进一步展示了该数据集的实用性，表明在SWE-Bench++实例上进行微调可在SWE-bench多语言基准测试上带来可测量的提升。SWE-Bench++为评估和改进仓库级代码生成提供了一个可扩展的多语言基准测试。

---

## 1. 研究动机 (Problem)

- **研究问题**：如何自动化、大规模地生成高质量的软件工程基准测试数据集，以评估和改进大型语言模型（LLMs）在仓库级代码生成任务上的性能？

- **研究背景**：SWE-bench等基准测试为评估LLMs在软件工程任务上的表现提供了标准化方法，但当前数据主要依赖手动 curation（人工策划），且局限于Python语言的bug修复任务。随着LLMs能力的快速提升，静态、单一语言的基准测试已无法满足全面评估和提升模型性能的需求。

- **现有局限性**：
  1. **手动 curation 成本高**：传统方法依赖人工选择和标注pull requests，耗时耗力难以扩展
  2. **静态数据集**：现有数据集是固定的，无法持续更新以反映真实软件开发实践
  3. **语言局限性**：主要聚焦于Python语言的bug修复，缺乏多语言支持
  4. **任务类型单一**：仅覆盖bug修复，未涵盖功能请求等更广泛的软件工程任务

## 2. 核心思想 (Key Idea)

- **核心贡献**：提出SWE-Bench++框架，通过自动化四阶段流水线从GitHub开源项目中 harvesting（获取）pull requests，生成涵盖11种语言、包含bug修复和功能请求的大规模可执行软件工程基准测试。

- **创新点**：
  1. **全自动化pipeline**：无需人工干预，自动从GitHub仓库中提取、组织、验证任务实例
  2. **多语言覆盖**：支持11种编程语言的代码生成任务，打破Python单一语言局限
  3. **任务多样性**：同时覆盖bug修复和feature requests两种任务类型
  4. **hint-guided trajectory synthesis**：将强模型无法解决的实例转化为训练轨迹，实现数据闭环

- **关键洞察**：真实的GitHub pull requests本身就包含了丰富的代码变更、测试用例和上下文信息，可以作为高质量软件工程基准测试的天然来源，通过自动化工具链可以将其转化为可执行、可评估的任务实例。

## 3. 算法结构 (Algorithm)

- **整体框架**：SWE-Bench++采用四阶段流水线架构，外加一个可选的hint-guided trajectory synthesis步骤。

```
GitHub仓库 → [阶段1: Programmatic Sourcing] → 候选PRs
                                      ↓
                              [阶段2: Environment Synthesis]
                                      ↓
                              [阶段3: Test Oracle Extraction]
                                      ↓
                              [阶段4: Quality Assurance]
                                      ↓
                              可执行任务实例
                                      ↓
                    [Hint-guided Trajectory Synthesis]
                                      ↓
                              训练轨迹
```

- **核心步骤**：
  
  **阶段1：Programmatic Sourcing（程序化采集）**
  - 使用GitHub API程序化搜索符合标准的pull requests
  - 筛选标准：包含代码变更、有测试文件关联、满足最小代码量要求
  - 采集metadata：提交历史、代码diff、PR描述等

  **阶段2：Environment Synthesis（环境合成）**
  - 从PR的base commit重建完整的代码仓库环境
  - 解析依赖关系，安装必要的包和库
  - 确保环境可复现，能够执行测试

  **阶段3：Test Oracle Extraction（测试预言提取）**
  - 从PR关联的测试文件中提取测试用例
  - 识别测试的输入、预期输出和验证逻辑
  - 将测试用例格式化为可执行的验证脚本

  **阶段4：Quality Assurance（质量保证）**
  - 过滤掉无法通过基础测试的PR（保证ground truth正确性）
  - 验证环境可复现性
  - 评估任务难度和多样性

  **Hint-guided Trajectory Synthesis（提示引导轨迹合成）**
  - 对于强模型无法解决的实例
  - 使用hint机制引导模型接近正确答案
  - 收集成功的推理轨迹作为训练数据

## 4. 理论证明 (Theory)

本文主要贡献为工程框架，无复杂的理论推导和证明。主要涉及的关键概念和公式：

- **Pass@k 评估指标**：
$$Pass@k = \mathbb{E}[min(1, \sum_{i=1}^{k} \mathbb{1}\{c_i \in S\})]$$

其中 $c_i$ 是第i个生成结果，$S$ 是包含正确解决方案的集合。

- **数据规模统计**：
$$\text{Total Instances} = 11,133$$
$$\text{Repositories} = 3,971$$
$$\text{Languages} = 11$$

## 5. 实验设计与结论 (Experiment)

- **数据集**：
  - **主数据集**：SWE-Bench++，包含11,133个实例，来自3,971个GitHub仓库，覆盖11种编程语言
  - **评估子集**：从主数据集中抽取1,782个实例用于评估
  - **语言分布**：涵盖Python、JavaScript、TypeScript、Java、Go、Rust、C++、C#、Ruby、PHP、Swift等11种语言

- **主要结果**：
  在1,782个实例的评估集上，各模型表现如下：

  | 模型 | Pass@10 |
  |------|---------|
  | claude-sonnet-4.5 | 36.20% |
  | gpt-5-2025-08-07 | 34.57% |
  | gemini/gemini-2.5-pro | 24.92% |
  | gpt-4o | 16.89% |

- **对比分析**：
  1. **与SWE-bench对比**：SWE-Bench++提供了多语言支持，任务类型更丰富（bug fixes + feature requests）
  2. **模型性能差异**：Claude Sonnet 4.5表现最佳，但最高pass@10仅为36.20%，说明当前LLMs在仓库级任务上仍有很大提升空间
  3. **fine-tuning效果**：在SWE-Bench++上fine-tuning的模型在SWE-bench Multilingual基准上取得可测量的提升，验证了数据集的有效性

## 6. 创新点

- **创新点1：全自动化大规模基准生成**
  首次提出完全自动化、可扩展的软件工程基准测试生成框架，摆脱了传统方法对人工 curation的依赖，能够持续从GitHub获取新数据。

- **创新点2：多语言多任务类型覆盖**
  突破了SWE-bench仅支持Python bug修复的限制，首次实现涵盖11种编程语言、同时包含bug修复和功能请求的综合性基准测试。

- **创新点3：数据闭环机制**
  提出hint-guided trajectory synthesis方法，将模型无法解决的困难实例转化为训练轨迹，实现了"评估-失败-学习-再评估"的数据闭环，为持续提升模型能力提供了可行路径。

## 7. 可借鉴点 (Your Research)

- **研究启发**：
  1. **真实数据源挖掘**：GitHub等开源平台是取之不尽的高质量数据源，关键在于设计有效的自动化提取和验证流程
  2. **评估-训练闭环**：本文的hint-guided trajectory synthesis为如何利用评估数据改进模型提供了很好的思路
  3. **可执行性验证**：强调执行-based评估而非仅靠匹配或文本相似度，更符合软件工程的实际需求

- **改进方向**：
  1. **任务难度分级**：可增加任务难度分级机制，帮助更细粒度地评估模型能力
  2. **更多语言支持**：虽然已有11种语言，但可继续扩展到更多主流语言如Kotlin、Rust等
  3. **时序动态性**：可考虑建立持续更新机制，定期从GitHub获取新数据保持基准时效性
  4. **领域专用任务**：可扩展到特定领域（如Web开发、系统编程、AI应用等）的专业任务集
  5. **成本效率优化**：当前框架可能存在资源消耗问题，可优化环境合成和测试执行流程降低计算成本

---

## Related Work

Over recent years, numerous benchmarks have been proposed to evaluate Large Language Models (LLMs) on software engineering tasks, ranging from function‑level code generation (HumanEval [1], MBPP [2]) to repository‑level bug fixing (SWE‑bench [3]). While these datasets have driven significant progress, they typically rely on manual curation, static snapshots, and a predominant focus on Python, limiting their scalability and language diversity. Subsequent efforts have attempted to automate the construction of coding benchmarks by extracting issue reports, commit messages, and pull requests from public repositories (e.g., GitHub Bug Dataset [4], RepoEval [5]), yet they often restrict extraction to single‑file changes or narrow domains. In parallel, frameworks such as CodeXGLUE [6] and MultiBLEU have introduced multi‑task benchmarks that combine classification, summarization, and code generation, but they still treat each task in isolation rather than requiring full repository‑level reasoning. In contrast, SWE‑Bench++ [7] introduces an automated, scalable pipeline that directly harvests complex software engineering problems from diverse open‑source projects, supporting multiple programming languages and preserving the inter‑file context necessary for repository‑level problem solving.

---

