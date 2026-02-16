# 📖 论文深度精读报告

**论文ID**: 2509.14635
**标题**: SWE-QA: Can Language Models Answer Repository-level Code Questions?
**作者**: Weihan Peng, Yuling Shi, Yuhang Wang, Xinyun Zhang, Beijun Shen
**发表**: 2025-09-18
**相似度**: 61.0%

---

## 摘要

### 英文原文

Understanding and reasoning about entire software repositories is an essential capability for intelligent software engineering tools. While existing benchmarks such as CoSQA and CodeQA have advanced the field, they predominantly focus on small, self-contained code snippets. These setups fail to capture the complexity of real-world repositories, where effective understanding and reasoning often require navigating multiple files, understanding software architecture, and grounding answers in long-range code dependencies.
In this paper, we present \ourbench, a repository-level code question answering (QA) benchmark designed to facilitate research on automated QA systems in realistic code environments. \ourbench involves 576 high-quality question-answer pairs spanning diverse categories, including intention understanding, cross-file reasoning, and multi-hop dependency analysis. To construct \ourbench, we first crawled 77,100 GitHub issues from 11 popular repositories. Based on an analysis of naturally occurring developer questions extracted from these issues, we developed a two-level taxonomy of repository-level questions and constructed a set of seed questions for each category. For each category, we manually curated and validated questions and collected their corresponding answers. As a prototype application, we further develop \ourmethod, an agentic framework in which LLM agents reason and act to find answers automatically. 
We evaluate six advanced LLMs on \ourbench under various context augmentation strategies. Experimental results highlight the promise of LLMs, particularly our \ourmethod framework, in addressing repository-level QA, while also revealing open challenges and pointing to future research directions.
%Finally, we conduct detailed analyses across question taxonomies and repositories, highlighting models' strengths in conceptual questions and weaknesses in procedural tracing, as well as varying difficulty across different codebases. These insights reveal current limitations in repository-level QA and provide directions for future improvements.

### 中文翻译

理解并推理整个软件代码仓库是智能软件工程工具的一项关键能力。现有的基准测试（如CoSQA和CodeQA）虽然推动了该领域的发展，但主要聚焦于小型、自包含的代码片段。这些设置无法捕捉真实世界代码仓库的复杂性，因为在实际应用中，有效的理解和推理往往需要浏览多个文件、理解软件架构，并将答案建立在长距离代码依赖关系之上。

在本文中，我们提出了\ourbench，这是一个仓库级代码问答（QA）基准测试，旨在促进对真实代码环境中自动化QA系统的研究。\ourbench包含576个高质量问答对，涵盖多种类别，包括意图理解、跨文件推理和多跳依赖分析。为构建\ourbench，我们首先从11个热门代码仓库中抓取了77,100个GitHub议题。基于对这些议题中提取的自然发生的开发者问题的分析，我们开发了一个仓库级问题的两级分类体系，并为每个类别构建了一组种子问题。对于每个类别，我们人工整理并验证了问题及其对应的答案。作为原型应用，我们进一步开发了\ourmethod，这是一个智能体框架，其中大语言模型（LLM）智能体通过推理和行动来自动寻找答案。

我们在各种上下文增强策略下对六个先进的LLM进行了评估。实验结果凸显了LLM在解决仓库级QA问题上的潜力，特别是我们的\ourmethod框架，同时这也揭示了开放性挑战并指明了未来的研究方向。

---

# SWE-QA: 论文详细分析

## 1. 研究动机 (Problem)

- **研究问题**：如何使语言模型能够回答仓库级（repository-level）的代码问题，这类问题需要理解整个软件仓库的架构、跨文件依赖关系和长距离代码关联。

- **研究背景**：理解和推理整个软件仓库是智能软件工程工具的基本能力。随着软件系统规模日益复杂，能够在仓库级别进行代码理解和推理的能力变得尤为重要。

- **现有局限性**：
  - 现有基准测试如 CoSQA 和 CodeQA 主要关注小型、自包含的代码片段
  - 这些设置无法捕捉真实世界仓库的复杂性
  - 缺乏需要浏览多个文件、理解软件架构、以及将答案建立在长距离代码依赖关系上的基准测试

---

## 2. 核心思想 (Key Idea)

- **核心贡献**：提出 SWE-QA，第一个专门用于评估仓库级代码问答的基准测试，包含 576 个高质量问答对，涵盖意图理解、跨文件推理和多跳依赖分析等多种类别。

- **创新点**：
  - 创建了首个仓库级代码 QA 基准测试，填补了现有基准测试的空白
  - 开发了双层分类体系来系统化仓库级问题
  - 构建了 SWE-QA-Agent 代理框架，使 LLM 能够自主推理和行动来寻找答案

- **关键洞察**：真实世界的代码理解需要多文件导航、软件架构理解和长距离依赖推理，而现有方法无法有效评估这一能力。

---

## 3. 算法结构 (Algorithm)

- **整体框架**：
  ```
  GitHub Issues 爬取 → 问题分类体系构建 → 种子问题生成 → 人工验证 → QA对收集 
       ↓
  SWE-QA 数据集 (576 QA对)
       ↓
  SWE-QA-Agent 评估框架
       ↓
  六种LLM + 多种上下文增强策略 → 性能评估
  ```

- **核心步骤**：
  1. **数据收集**：从 11 个流行 GitHub 仓库爬取 77,100 个 GitHub issues
  2. **问题分析**：分析从这些 issues 中提取的开发者自然提问
  3. **分类体系构建**：开发仓库级问题的双层分类体系
  4. **种子问题生成**：为每个类别构建一组种子问题
  5. **质量控制**：人工策划和验证问题和答案
  6. **代理框架开发**：开发 SWE-QA-Agent 实现自动化问答
  7. **评估实验**：使用六种高级 LLM 在不同上下文增强策略下进行评估

---

## 4. 理论证明 (Theory)

- **核心定理**：本文为实证研究型论文，无形式化定理证明。

- **重要公式**：无数学公式。

> **注**：本文主要贡献在于构建基准测试和评估框架，属于应用研究而非理论研究范畴，因此不涉及传统意义上的理论证明部分。

---

## 5. 实验设计与结论 (Experiment)

- **数据集**：
  - **原始数据**：从 11 个流行 GitHub 仓库爬取的 77,100 个 GitHub issues
  - **基准测试**：SWE-QA 包含 576 个高质量问答对
  - **仓库类型**：涵盖多种编程语言和应用领域
  - **问题类别**：意图理解（intention understanding）、跨文件推理（cross-file reasoning）、多跳依赖分析（multi-hop dependency analysis）

- **主要结果**：
  - 六种高级 LLM 在 SWE-QA 上进行了评估
  - 使用了多种上下文增强策略
  - 实验结果表明 LLM 在仓库级 QA 任务上展现出潜力
  - SWE-QA-Agent 框架表现最佳
  - 仍存在显著挑战，需要进一步研究

- **对比分析**：
  - 现有基准测试（CoSQA、CodeQA）仅关注代码片段级别
  - SWE-QA 是首个专门针对仓库级别复杂推理的基准测试
  - 实验结果揭示了当前 LLM 在真实世界仓库理解方面的能力边界和不足

---

## 6. 创新点

- **创新点1**：创建了首个仓库级代码问答基准测试 SWE-QA，包含 576 个高质量问答对，涵盖 11 个真实 GitHub 仓库。

- **创新点2**：提出了仓库级问题的双层分类体系，系统化定义了意图理解、跨文件推理、多跳依赖分析等类别。

- **创新点3**：开发了 SWE-QA-Agent 代理框架，实现了基于 LLM 代理的自动化仓库级问答系统，能够自主推理、导航和查找信息。

---

## 7. 可借鉴点 (Your Research)

- **研究启发**：
  - 基准测试构建的方法论：如何从真实开发场景（GitHub issues）中系统化提取和构建高质量 QA 数据集
  - 问题分类体系的构建思路对于其他领域（如文档问答、系统级理解）具有参考价值
  - 代理框架的设计思路可应用于其他需要长程推理的软件工程任务

- **改进方向**：
  - 扩大仓库覆盖范围，增加更多编程语言和领域的仓库
  - 增加问题的复杂度和多样性，特别是架构级理解问题
  - 探索更有效的上下文检索和增强策略
  - 研究如何更好地利用代码结构信息（图结构、依赖关系）来增强理解
  - 开发专门的训练方法来提升模型在仓库级任务上的能力

---

*以上分析基于论文摘要内容，如需更详细分析，请提供论文全文。*

---

## Related Work

Recent years have seen a surge of benchmarks that target automatic code understanding, such as CoSQA, CodeQA, and CodeSearchNet, which provide large collections of natural‑language queries paired with code snippets. These datasets have driven notable advances in code question‑answering, yet they are largely confined to single‑function or isolated code fragments and therefore overlook the multi‑file dependencies, build configurations, and repository‑wide context typical of real‑world software projects. In response, a growing body of work has begun to address repository‑level tasks—including cross‑file code retrieval, bug localization, and automated code review—by leveraging graph‑based representations or pre‑trained models that ingest entire codebases. Nevertheless, a systematic benchmark that directly evaluates whether a language model can answer natural‑language questions requiring whole‑repository reasoning remains absent. To fill this gap, we introduce SWE‑QA, a new dataset and evaluation framework that comprises repository‑level questions derived from real‑world software projects, enabling a more holistic assessment of language models’ comprehension capabilities.

---

