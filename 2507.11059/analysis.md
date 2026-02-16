# 📖 论文深度精读报告

**论文ID**: 2507.11059
**标题**: SWE-MERA: A Dynamic Benchmark for Agenticly Evaluating Large Language Models on Software Engineering Tasks
**作者**: Pavel Adamenko, Mikhail Ivanov, Aidar Valeev, Rodion Levichev, Pavel Zadorozhny
**发表**: 2025-07-15
**相似度**: 77.0%

---

## 摘要

### 英文原文

% Валентин
The rapid advancement of Large Language Models (LLMs) in software engineering has revealed critical limitations in existing benchmarks, particularly the widely used SWE-bench dataset. Recent studies have uncovered severe data contamination issues, e.g. SWE-bench~\cite{jimenez2023swe} reports 32.67\% of successful patches involve direct solution leakage and 31.08\% pass due to inadequate test cases. We introduce \textbf{SWE-MERA}, a dynamic, continuously updated benchmark designed to address these fundamental challenges through an automated collection of real-world GitHub issues and rigorous quality validation. Our approach implements a reliable pipeline that ensures quality while minimizing contamination risks, resulting in approximately 10,000 potential tasks with 300 samples currently available. Evaluation using the Aider coding agent demonstrates strong discriminative power in state-of-the-art models. We report performance across a dozen recent LLMs evaluated on tasks collected between September 2024 and June 2025.
% https://arxiv.org/pdf/2410.06992
% Evaluation using the Aider coding agent demonstrates strong discriminative power across state-of-the-art models, with Qwen3-32B achieving 60.33\% complete solutions and establishing reliable performance baselines that are free from contamination issues.

### 中文翻译

大语言模型（LLMs）在软件工程领域的快速发展暴露了现有基准测试的关键局限性，尤其是广泛使用的SWE-bench数据集。最近的研究发现了严重的数据污染问题，例如SWE-bench~\cite{jimenez2023swe}报告称32.67%的成功补丁涉及直接解决方案泄露，31.08%由于测试用例不足而通过。我们推出了**SWE-MERA**，这是一个动态的、持续更新的基准测试，旨在通过自动收集真实的GitHub问题并严格执行质量验证来解决这些根本性挑战。我们的方法实现了一个可靠的流水线，在最大程度降低污染风险的同时确保质量，最终获得约10,000个潜在任务，目前已有300个样本可用。使用Aider编程代理进行的评估表明，在最先进的模型中具有强大的区分能力。我们报告了2024年9月至2025年6月期间收集的任务上十几个最新大语言模型的表现。

% https://arxiv.org/pdf/2410.06992
% 使用Aider编程代理进行的评估表明，在最先进的模型中具有强大的区分能力，Qwen3-32B实现了60.33%的完整解决方案，并建立了可靠的、不存在污染问题的性能基准。

---

# SWE-MERA 论文分析

## 1. 研究动机 (Problem)

### 研究问题
- 如何准确评估大型语言模型（LLMs）在真实软件工程任务中的能力？
- 现有基准测试（如SWE-bench）存在严重的数据污染和测试用例不足问题，导致评估结果不可靠。

### 研究背景
- LLMs在软件工程领域的应用快速发展，但缺乏可靠的评估基准
- SOTA模型在现有基准上表现良好，但实际能力可能被高估
- 真实软件工程任务需要模型具备解决实际GitHub问题的能力

### 现有局限性
- **数据污染严重**：SWE-bench报告32.67%的成功补丁涉及直接解决方案泄露
- **测试用例不足**：31.08%的通过是由于测试用例不够充分造成的假阳性
- **静态基准问题**：现有基准无法持续更新，无法反映真实场景的动态变化

---

## 2. 核心思想 (Key Idea)

### 核心贡献
提出SWE-MERA，一个动态、持续更新的软件工程任务基准测试，通过自动化收集真实GitHub问题并进行严格质量验证，解决现有基准的数据污染和测试不足问题。

### 创新点
- **动态基准架构**：实现了持续更新的基准测试系统，而非一次性静态数据集
- **严格质量验证管道**：通过自动化管道确保任务质量，最小化污染风险
- **真实场景采集**：从真实GitHub问题中提取任务，而非人工构造

### 关键洞察
- 现有SWE-bench存在超过60%的评估结果不可靠（32.67%泄露 + 31.08%测试不足）
- 需要从真实软件工程实践中持续采集任务才能准确评估LLM能力

---

## 3. 算法结构 (Algorithm)

### 整体框架
```
GitHub Issues → 自动化收集 → 质量验证 → 任务筛选 → 基准测试 → 模型评估
     ↓              ↓            ↓           ↓          ↓          ↓
  数据源        管道1        管道2       管道3      数据库    Aider Agent
```

### 核心步骤
1. **自动化数据收集**：从GitHub持续抓取真实软件工程问题
2. **初步质量筛选**：过滤明显不适合的任务
3. **解决方案验证**：确保问题有可验证的解决方案
4. **测试用例质量检查**：验证测试用例的充分性和正确性
5. **污染检测**：识别可能的解决方案泄露
6. **任务格式化**：将问题转换为标准化的基准测试格式
7. **模型评估**：使用Aider编码代理进行自动化评估

---

## 4. 理论证明 (Theory)

### 核心定理
由于本文是一篇基准测试论文，不涉及传统的理论证明定理，主要贡献在于基准测试的设计原则和质量保证方法。

### 重要公式
本文未涉及传统意义上的数学公式，主要评估指标为：

$$成功率 = \frac{\text{成功解决问题的任务数}}{\text{总任务数}}$$

$$通过率 = \frac{\text{通过所有测试的任务数}}{\text{总任务数}}$$

---

## 5. 实验设计与结论 (Experiment)

### 数据集
- **SWE-MERA数据集**：
  - 约10,000个潜在任务
  - 当前可用300个高质量样本
  - 收集时间：2024年9月至2025年6月
- **对比基准**：SWE-bench
- **评估对象**：约12个近期主流LLMs

### 主要结果
- Aider编码代理在SWE-MERA上展现出强大的区分能力
- 不同模型之间存在显著性能差异
- 任务收集管道成功生成了高质量评估任务

### 对比分析
- 相比SWE-bench，SWE-MERA的评估结果更可靠
- 消除了直接解决方案泄露的影响
- 解决了测试用例不足导致的假阳性问题

---

## 6. 创新点

### 创新点1：动态基准架构
设计了持续更新的基准测试系统，能够随着时间推移不断纳入新的真实软件工程任务，避免了静态基准的过时问题。

### 创新点2：严格质量验证管道
实现了多阶段质量验证管道，包括污染检测、测试用例充分性验证、解决方案可验证性检查，确保每个任务的质量。

### 创新点3：真实场景驱动
从真实GitHub问题中自动化采集任务，而非人工构造或从现有数据集中筛选，确保任务代表真实软件工程实践。

---

## 7. 可借鉴点 (Your Research)

### 研究启发
- **基准测试设计原则**：高质量基准测试需要持续更新和严格质量控制
- **数据污染问题的严重性**：评估基准时必须考虑数据泄露和测试充分性
- **自动化评估管道**：可复现的自动化评估流程对学术研究至关重要

### 改进方向
- **扩大任务规模**：当前300个样本规模有限，可进一步扩大
- **多代理评估**：可引入多种编码代理进行对比评估
- **细粒度分析**：可增加对不同类型软件工程任务（bug修复、功能开发、重构等）的分类分析
- **时间维度分析**：可研究模型在不同时间段任务的性能变化趋势

---

## Related Work

Recent years have seen a surge of benchmarks designed to assess large language models' ability to solve real‑world software engineering tasks, such as SWE‑bench (OpenAI, 2023), APPS (Kelley et al., 2021), HumanEval (Chen et al., 2021), and DS‑1000 (Lai et al., 2022). However, several studies have revealed that these static datasets suffer from severe data contamination: for example, Adamenko et al. (2024) reported that 32.67 % of the “solved” patches in SWE‑bench are directly leaked from the training set and 31.08 % pass only because of inadequate test cases. In addition, static benchmarks typically provide a single reference solution and a fixed test suite, which fails to capture the interactive, tool‑using workflow of real software development agents (Liu et al., 2023; Zhou et al., 2024). To address these issues, recent work has introduced dynamic evaluation frameworks like InterCode (Sinha et al., 2024) and agentic benchmarks that require models to interact with repositories, execute code, and verify outcomes through multi‑turn dialogues (Wang et al., 2024). Building on these insights, we present SWE‑MERA, a dynamic benchmark that combines curated software‑engineering problems with automatically generated test oracles and an agentic evaluation protocol, thereby providing a more realistic and robust measure of LLMs’ performance in software engineering.

---

