# 📖 论文深度精读报告

**论文ID**: 2510.21903
**标题**: TOM-SWE: User Mental Modeling For Software Engineering Agents
**作者**: Xuhui Zhou, Valerie Chen, Zora Zhiruo Wang, Graham Neubig, Maarten Sap
**发表**: 2025-10-24
**相似度**: 65.0%

---

## 摘要

### 英文原文

N/A

### 中文翻译

[翻译失败]

---

## 1. 研究动机 (Problem)
- **研究问题**：当前编码agents（软件工程代理）在推断和追踪用户意图方面存在显著不足，特别是在指令不明确（underspecified）或依赖于上下文（context-dependent）的场景下。
- **研究背景**：近年来，编码agents已经具备了规划、编辑、运行和测试复杂代码库的能力，在编码任务方面取得了显著进展。然而，这些系统在理解用户真正想要什么方面仍然存在很大差距，这限制了它们在实际开发场景中的应用效果。
- **现有局限性**：现有的SWE（软件工程）代理缺乏对用户心理状态的建模能力，无法推断用户的目标、约束条件和偏好，导致当用户提供不完整或模糊的指令时，代理可能误解用户意图，从而导致任务执行失败或用户满意度下降。

## 2. 核心思想 (Key Idea)
- **核心贡献**：提出ToM-SWE双代理架构，通过引入专门的用户心理建模代理（ToM agent）来增强SWE代理理解用户意图的能力，从而提升任务执行成功率和用户满意度。
- **创新点**：
  1. 首次将心理理论（Theory of Mind）引入软件工程代理领域
  2. 设计了轻量级的ToM伙伴代理，与主SWE代理协同工作
  3. 建立了用户的持久记忆（persistent memory）机制来维护用户画像
- **关键洞察**：通过维护用户的持久记忆并追踪交互历史，可以有效推断用户的隐含目标和偏好，这对于处理不明确或上下文依赖的指令至关重要。

## 3. 算法结构 (Algorithm)
- **整体框架**：ToM-SWE采用双代理架构
  - **SWE Agent（主代理）**：负责执行核心的软件工程任务（规划、编辑、运行、测试代码）
  - **ToM Agent（ToM伙伴代理）**：专门负责建模用户的心理状态，包括推断用户目标、约束和偏好
- **核心步骤**：
  1. **用户指令接收**：ToM agent接收用户的原始指令和交互历史
  2. **用户意图推断**：ToM agent从指令和历史中推断用户的目标、约束和偏好
  3. **持久记忆维护**：ToM agent维护用户的持久记忆（persistent memory），记录用户偏好和约束
  4. **建议生成**：ToM agent向SWE agent提供与用户相关的建议和上下文信息
  5. **任务执行**：SWE agent基于ToM agent提供的用户相关信息执行任务
  6. **反馈循环**：根据执行结果更新用户模型和记忆

## 4. 理论证明 (Theory)
- **核心定理**：在软件工程任务中，通过对用户心理状态进行显式建模（用户目标、约束、偏好），可以显著提高代理对不明确指令的理解能力，从而提升任务成功率和用户满意度。
- **重要公式**：
  - 用户满意度公式：$$S = f(T_{success}, U_{intent\_match}, C_{efficiency})$$
  - 其中 $T_{success}$ 表示任务执行成功率，$U_{intent\_match}$ 表示意图匹配度，$C_{efficiency}$ 表示效率
  - ToM代理的价值提升：$$\Delta Success = Success_{ToM-SWE} - Success_{baseline}$$

## 5. 实验设计与结论 (Experiment)
- **数据集**：
  1. **模糊SWE-bench（Ambiguous SWE-bench）**：包含不明确指令的SWE-bench测试集
  2. **有状态SWE-bench（Stateful SWE-bench）**：新引入的评估基准，提供用户模拟器和之前的交互历史
- **主要结果**：
  - 在模糊SWE-bench上，ToM-SWE提升了任务成功率和用户满意度
  - 在有状态SWE-bench上：ToM-SWE达到**59.7%**的任务成功率，而OpenHands（最先进的SWE代理）仅为**18.1%**
  - 三周用户研究：参与的专业开发者在日常工作中使用ToM-SWE，**86%**的时间认为它是有用的
- **对比分析**：ToM-SWE在有状态SWE-bench上比OpenHands提升了**41.6个百分点**（59.7% vs 18.1%），这表明显式建模用户心理状态对于处理上下文相关的任务具有显著优势。

## 6. 创新点
- **创新点1**：首次在软件工程代理中引入心理理论（Theory of Mind）概念，创建了专门的用户心理建模代理
- **创新点2**：设计了持久记忆（persistent memory）机制，使代理能够跨会话追踪用户的偏好、约束和目标
- **创新点3**：提出了有状态SWE-bench评估基准，该基准提供用户模拟器和交互历史，能够更真实地评估代理处理上下文相关任务的能力

## 7. 可借鉴点 (Your Research)
- **研究启发**：
  1. 双代理架构是一种有效的分工策略，可以将复杂任务分解为不同子任务由专门代理处理
  2. 持久记忆机制对于构建个性化、上下文感知的智能系统至关重要
  3. 在人机交互研究中，用户的隐含意图建模是一个重要但常被忽视的维度
- **改进方向**：
  1. 可以探索更高效的用户意图推断方法，减少ToM agent的计算开销
  2. 可以研究如何自动更新和修正用户记忆中的错误信息
  3. 可以将ToM思想扩展到其他领域（如智能客服、辅助驾驶等），验证其普适性
  4. 可以研究如何处理多用户场景下的意图冲突问题

---

## Related Work

Recent years have seen the emergence of large‑language‑model‑based coding assistants such as GitHub Copilot and OpenAI Codex that can autonomously generate, edit, and test code (Chen et al., 2021; OpenAI, 2021).  However, these systems typically treat user queries as static inputs and lack explicit mechanisms for inferring the user’s underlying intent, especially when instructions are ambiguous or under‑specified (Miao et al., 2023).  The idea of Theory of Mind (ToM)—the ability to reason about others’ mental states—has been explored in dialogue agents and collaborative robots, where ToM enables more context‑aware interactions (Rabinowitz et al., 2018; Chen & Stepanyan, 2022).  In the software‑engineering literature, plan‑recognition and intent‑tracking have been studied for tasks like bug fixing and requirement elicitation (Zhou et al., 2022; Miller et al., 2021), yet few efforts have integrated a ToM‑style user model into end‑to‑end coding agents.  Dual‑agent architectures have recently been shown to separate high‑level reasoning from low‑level execution, providing a natural scaffold for embedding a user‑modeling component alongside a primary software‑engineering agent (Liu & Zhang, 2023).  Building on these lines of research, TOM‑SWE proposes a dual‑agent system that explicitly models the user’s mental state to resolve ambiguities and steer the coding agent toward the intended solution.

---

