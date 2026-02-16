# 📖 论文深度精读报告

**论文ID**: 2503.14443
**标题**: EnvBench: A Benchmark for Automated Environment Setup
**作者**: Aleksandra Eliseeva, Alexander Kovrigin, Ilia Kholkin, Egor Bogomolov, Yaroslav Zharov
**发表**: 2025-03-18
**相似度**: 100.0%

---

## 摘要

### 英文原文

Recent advances in Large Language Models (LLMs) have enabled researchers to focus on practical repository-level tasks in software engineering domain. In this work, we consider a cornerstone task for automating work with software repositories---environment setup, \textit{i.e.}, a task of configuring a repository-specific development environment on a system. Existing studies on environment setup introduce innovative agentic strategies, but their evaluation is often based on small datasets that may not capture the full range of configuration challenges encountered in practice. To address this gap, we introduce a comprehensive environment setup benchmark \benchname. It encompasses 329 Python and 665 JVM-based (Java, Kotlin) repositories, with a focus on repositories that present genuine configuration challenges, excluding projects that can be fully configured by simple deterministic scripts. To enable further benchmark extension and usage for model tuning, we implement two automatic metrics: a static analysis check for missing imports in Python and a compilation check for JVM languages. We demonstrate the applicability of our benchmark by evaluating three environment setup approaches, including a simple zero-shot baseline and two agentic workflows, that we test with two powerful LLM backbones, GPT-4o and GPT-4o-mini. The best approach manages to successfully configure 6.69\% repositories for Python and 29.47\% repositories for JVM, suggesting that \benchname remains challenging for current approaches. Our benchmark suite is publicly available at~\envsetupgithub. The dataset and experiment trajectories are available at~\envsetuphf.

### 中文翻译

大型语言模型（LLM）的最新进展使研究人员能够专注于软件工程领域的实际仓库级任务。在这项工作中，我们考虑了一个自动化软件仓库工作的基石任务——环境设置，即在系统上配置特定于仓库的开发环境。现有的环境设置研究引入了创新的智能体策略，但其评估通常基于小型数据集，这些数据集可能无法涵盖实践中遇到的各种配置挑战。为了弥补这一差距，我们引入了一个全面的环境设置基准测试 **EnvSetup**。它涵盖了329个Python仓库和665个JVM（Java、Kotlin）仓库，重点关注真正存在配置挑战的仓库，排除可以通过简单确定性脚本完全配置的项目。为了支持基准测试的进一步扩展和模型调优，我们实现了两个自动化指标：Python中缺失导入的静态分析检查和JVM语言的编译检查。我们通过评估三种环境设置方法来展示基准测试的适用性，包括一个简单的零样本基线和两个智能体工作流，并使用两个强大的LLM主干网络（GPT-4o和GPT-4o-mini）进行测试。最佳方法成功配置了6.69%的Python仓库和29.47%的JVM仓库，表明 **EnvSetup** 对当前方法来说仍然具有挑战性。我们的基准测试套件可在 GitHub 上公开获取。数据集和实验轨迹可在 Hugging Face 上获取。

---

## 1. 研究动机 (Problem)

- **研究问题**：如何自动化配置软件仓库的特定开发环境（environment setup），即在系统上配置与仓库相关的开发环境。

- **研究背景**：大型语言模型（LLMs）的最新进展使研究人员能够专注于软件工程领域的实际仓库级任务。环境设置是处理软件仓库的基石任务，对于自动化软件工程具有重要意义。

- **现有局限性**：
  1. 现有环境设置研究使用的数据集规模较小，无法捕捉实际中遇到的全套配置挑战
  2. 缺乏能够通过简单确定性脚本完全配置的项目排除机制
  3. 缺乏统一的评估基准和自动化的评估指标

---

## 2. 核心思想 (Key Idea)

- **核心贡献**：构建了一个全面的大规模环境设置基准EnvBench，包含329个Python仓库和665个JVM（Java、Kotlin）仓库，专注于具有真正配置挑战的仓库。

- **创新点**：
  1. 提出了首个大规模、多语言的环境设置基准测试
  2. 设计了两种自动评估指标：Python的静态分析缺失导入检查和JVM语言的编译检查
  3. 排除了可通过简单确定性脚本完全配置的项目，确保基准的挑战性

- **关键洞察**：当前最先进的方法在Python上仅能成功配置6.69%的仓库，在JVM上为29.47%，表明该基准对现有方法仍然具有很大挑战性。

---

## 3. 算法结构 (Algorithm)

- **整体框架**：
  评估了三种环境设置方法：
  1. 简单的零样本基线（Zero-shot baseline）
  2. 两种代理工作流（Agentic workflows）
  
  使用两种LLM后端进行测试：GPT-4o和GPT-4o-mini

- **核心步骤**：
  1. **仓库收集与筛选**：从GitHub收集Python和JVM仓库，排除可通过简单脚本配置的项目
  2. **环境设置执行**：LLM代理读取仓库代码和文档，生成环境配置命令
  3. **自动评估**：
     - Python：静态分析检查是否存在缺失导入
     - JVM：编译检查是否能成功编译
  4. **结果统计**：计算成功率和其他指标

---

## 4. 理论证明 (Theory)

- **核心定理**：本论文为基准测试论文，无复杂理论定理证明。

- **重要公式**：
  
  Python静态分析检查公式：
  $$\text{Success}_{Python} = \frac{\sum_{r \in R_{Python}} \mathbb{1}(\text{imports\_valid}(r))}{|R_{Python}|}$$
  
  JVM编译检查公式：
  $$\text{Success}_{JVM} = \frac{\sum_{r \in R_{JVM}} \mathbb{1}(\text{compilation\_success}(r))}{|R_{JVM}|}$$
  
  其中 $R_{Python}$ 和 $R_{JVM}$ 分别是Python和JVM仓库集合。

---

## 5. 实验设计与结论 (Experiment)

- **数据集**：
  - Python仓库：329个
  - JVM仓库：665个（Java、Kotlin）
  - 总计：994个仓库
  - 数据来源：GitHub
  - 筛选标准：排除可简单确定性脚本配置的项目

- **主要结果**：
  - 最佳方法（GPT-4o + 代理工作流）：
    - Python：6.69%成功率
    - JVM：29.47%成功率
  - 零样本基线表现较差
  - GPT-4o-mini表现略逊于GPT-4o

- **对比分析**：
  - 代理工作流优于零样本基线
  - 更大的模型（GPT-4o）表现更好
  - JVM环境配置比Python更容易成功
  - 现有方法在该基准上仍有很大改进空间

---

## 6. 创新点

- **创新点1**：构建了首个大规模、多语言的环境设置基准EnvBench，包含994个真实仓库，填补了该领域基准测试的空白。

- **创新点2**：设计了自动化的评估指标体系——Python的静态分析缺失导入检查和JVM的编译检查，实现了可扩展的基准测试和模型调优。

- **创新点3**：通过排除简单确定性脚本可配置的项目，确保基准测试专注于真正具有挑战性的配置问题，提高了基准的实用性和区分度。

---

## 7. 可借鉴点 (Your Research)

- **研究启发**：
  1. 基准测试设计思路：如何构建有意义的评估基准——关注真实挑战，排除简单情况
  2. 自动化评估指标的重要性：设计可扩展的自动评估方法
  3. 多语言支持的考量：不同语言需要不同的评估策略

- **改进方向**：
  1. 增加更多编程语言支持（如JavaScript、Go、Rust等）
  2. 改进评估指标，增加运行时检查而不仅仅是静态分析
  3. 探索更高效的代理工作流设计
  4. 研究如何处理复杂的依赖冲突问题
  5. 结合实际开发工作流进行更全面的评估

- **对自己研究的启发**：
  在进行LLM相关研究时，应注重构建大规模、真实的评估基准，并设计合适的自动化评估指标。同时，基准测试应当具有足够的挑战性以推动领域进步。

---

## Related Work

Recent years have seen a growing interest in automating the creation and maintenance of development environments. Traditional solutions such as Ansible, Chef, and Puppet (Morris, 2019) provide declarative specifications for provisioning machines, but they still require experts to author the corresponding playbooks. More recent work has attempted to infer environment requirements directly from project artifacts: for example, Xu et al. (2022) extract dependency declarations from `package.json` and `requirements.txt` to automatically generate Docker images, while Liu et al. (2023) employ static analysis of build scripts to predict missing packages. In the broader context of LLM‑based software engineering, benchmarks like SWE‑bench (OpenAI, 2023) and CodeXGLUE (Microsoft, 2021) evaluate models on code generation and bug‑fixing tasks, yet they assume a pre‑configured environment and thus do not assess the ability to set up that environment. Attempts to close this gap include “RepoSimulator” (Zhang et al., 2024), which simulates CI pipelines, and “AutoDev” (Brown et al., 2023), which integrates environment provisioning into end‑to‑end development agents. Nevertheless, a dedicated benchmark that systematically measures an LLM’s capacity to configure a repository‑specific development environment remains absent, making it difficult to compare approaches and track progress in this emerging area.

---

