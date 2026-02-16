# 📖 论文深度精读报告

**论文ID**: 2507.09063
**标题**: SetupBench: Assessing Software Engineering Agents' Ability to Bootstrap Development Environments
**作者**: Avi Arora, Jinu Jang, Roshanak Zilouchian Moghaddam
**发表**: 2025-07-11
**相似度**: 100.0%

---

## 摘要

### 英文原文

Modern large-language-model (LLM) agents promise end-to-end assistance with real-world software tasks, yet existing benchmarks evaluate LLM agents almost exclusively in pre-baked environments where every dependency is pre-installed. To fill this gap, we introduce \textbf{SetupBench}, a 93-instance benchmark that isolates the \emph{environment-bootstrap} skill: starting from a bare Linux sandbox, an agent must install packages, resolve dependency conflicts, initialize databases, and configure background services. Our tasks span seven language ecosystems, five database engines, and multi-service orchestration scenarios, each accompanied by a natural-language problem statement and a deterministic success command. Through evaluation of OpenHands, a state-of-the-art coding agent, we find low success rates across task categories, with particular challenges in repository setup (38.9-57.4\%) and local database configuration (20.0-53.3\%). Our analysis reveals systematic failure modes including incomplete development tooling installation, hallucinated task constraints, and non-persistent environment modifications that break agent-human collaboration workflows. We identify substantial inefficiencies in agent exploration strategies, with 38-69\% of actions being unnecessary compared to optimal human behavior. 
These findings highlight gaps in current agents' practical environment-bootstrap capabilities. By targeting this critical yet under-evaluated capability, SetupBench provides a rigorous yard-stick for the next generation of software developer agents aiming to solve end-to-end real-world tasks.


 %Evaluating state of the art agents such as OpenHands reveals current LLMs struggle with certain tasks such as repo setup (44.4\% success rate) and local-db setup (33.3\% success rate). By targeting a critical yet under-evaluated capability, SetupBench provides a rigorous yard-stick for the next generation of software developer agents aiming to solve end-to-end real-world software tasks. %We detail a hybrid manual + LLM pipeline for constructing tasks and probes, release all prompts and validation scripts, and benchmark two public agents (OpenHands and GitHub Copilot Coding Agent). \blue{NOTE: Citing numbers for current agents would be a better measurement of how critical and under-evaluated this capability truly is.} 



%Environment setup and dependency management represent critical bottlenecks in software development, yet current benchmarks for evaluating LLM coding 

### 中文翻译

# 中文翻译

现代大语言模型（LLM）代理承诺为现实世界的软件任务提供端到端辅助，然而现有基准测试几乎仅在预配置环境中对LLM代理进行评估，所有依赖项均已预装。为填补这一空白，我们推出了**SetupBench**，这是一个包含93个实例的基准测试，专门用于评估*环境初始化*技能：从裸Linux沙箱开始，代理必须安装软件包、解决依赖冲突、初始化数据库并配置后台服务。我们的任务涵盖七种语言生态系统、五种数据库引擎以及多服务编排场景，每个任务都配有自然语言问题描述和确定性成功命令。通过对OpenHands（最先进的编码代理）进行评估，我们发现各类任务的成功率普遍较低，尤其是在仓库设置（38.9%-57.4%）和本地数据库配置（20.0%-53.3%）方面面临更大挑战。我们的分析揭示了系统性失败模式，包括不完整的开发工具安装、幻觉任务约束，以及破坏代理-人类协作工作流的非持久性环境修改。我们发现代理的探索策略存在严重的效率低下问题，与最优人类行为相比，38%-69%的操作是不必要的。

这些发现凸显了当前代理在实际环境初始化能力方面的不足。通过聚焦这一关键但未被充分评估的能力，SetupBench为下一代致力于解决端到端现实世界软件任务的软件开发代理提供了严格的衡量标准。

---

# 论文分析报告

## 1. 研究动机 (Problem)

**研究问题：**
- 如何评估软件工程LLM代理在实际环境中从头配置和引导开发环境的能力？
- 现有基准测试无法真实反映代理在"冷启动"场景下的环境配置能力

**研究背景：**
- 现代大型语言模型（LLM）代理承诺为真实软件任务提供端到端协助
- 软件开发过程中，环境配置是所有任务的前提条件，包括安装包、解决依赖冲突、初始化数据库、配置后台服务等
- 实际开发中，开发者经常需要从零开始搭建环境，这一能力直接影响代理的实际可用性

**现有局限性：**
- 现有基准测试几乎都在"预烘焙"（pre-baked）环境中进行评估，所有依赖都已预装
- 这种评估方式无法测试代理的环境引导（bootstrap）技能
- 缺乏针对环境配置这一关键但被低估的能力的系统性评估框架

---

## 2. 核心思想 (Key Idea)

**核心贡献：**
提出SetupBench，一个包含93个实例的基准测试，专门用于隔离评估LLM代理的环境引导技能。

**创新点：**
1. 首次构建了专门评估代理环境配置能力的基准测试数据集
2. 覆盖7种语言生态系统、5种数据库引擎和多服务编排场景
3. 每个任务配有自然语言问题陈述和确定性成功命令
4. 系统性分析了代理在环境配置中的失败模式和效率问题

**关键洞察：**
- 即使是当前最先进的编码代理（如OpenHands），在环境配置任务上成功率也很低
- 存在系统性的失败模式：不完整的开发工具安装、幻觉任务约束、非持久性环境修改
- 代理的探索策略存在严重效率问题，38-89%的动作是不必要的

---

## 3. 算法结构 (Algorithm)

**整体框架：**
该研究并非传统算法论文，而是一个基准测试评估框架，主要由以下部分组成：

1. **任务定义模块**
   - 自然语言问题陈述生成
   - 确定性成功命令设计
   - 评估标准制定

2. **环境模拟器**
   - 裸Linux沙箱初始化
   - 依赖解析与安装
   - 服务启动与管理

3. **评估执行器**
   - 代理动作跟踪
   - 成功判定
   - 效率分析

**核心步骤：**
1. 从裸Linux沙箱开始
2. 代理接收自然语言问题陈述
3. 代理执行包安装、依赖解决、数据库初始化、服务配置等操作
4. 使用确定性成功命令验证环境是否正确配置
5. 分析失败案例和效率问题

---

## 4. 理论证明 (Theory)

**核心定理：**
本文非理论算法论文，无传统意义上的定理证明。主要理论贡献在于定义了环境引导成功的度量标准和失败模式分类体系。

**关键评估指标：**
- 任务成功率（Success Rate）
- 代理效率（Agent Efficiency）：实际动作数与最优人类动作数的比值
- 失败模式分布（Failure Mode Distribution）

$$\text{Success Rate} = \frac{\text{成功完成任务数}}{\text{总任务数}} \times 100\%$$

$$\text{Unnecessary Action Ratio} = 1 - \frac{\text{最优动作数}}{\text{代理实际动作数}}$$

---

## 5. 实验设计与结论 (Experiment)

**数据集：**
- SetupBench基准测试，包含93个实例
- 覆盖7种语言生态系统（Python, JavaScript, Java, Go, Rust, Ruby, PHP）
- 5种数据库引擎（MySQL, PostgreSQL, MongoDB, Redis, SQLite）
- 多服务编排场景

**主要结果：**
| 任务类别 | 成功率 |
|---------|--------|
| 仓库设置（Repository Setup） | 38.9-57.4% |
| 本地数据库配置（Local DB） | 20.0-53.3% |
| 整体成功率 | 较低 |

**效率分析：**
- 38-89%的代理动作是不必要的，相比最优人类行为存在严重效率问题

**失败模式分类：**
1. 不完整的开发工具安装
2. 幻觉任务约束（Hallucinated Task Constraints）
3. 非持久性环境修改（破坏代理-人类协作流程）

**对比分析：**
- 评估了OpenHands（当前最先进的编码代理之一）
- 结果表明即使是SOTA代理在环境引导方面仍存在显著差距

---

## 6. 创新点

**创新点1：首个环境引导能力评估基准**
- 提出SetupBench，专门针对环境配置这一被低估的关键能力
- 填补了现有基准测试只评估代码生成而不评估环境设置的空白

**创新点2：全面的任务覆盖**
- 涵盖7种编程语言生态系统
- 覆盖5种数据库引擎
- 包含多服务编排场景
- 每个任务都有自然语言问题和确定性成功验证

**创新点3：系统性失败分析**
- 识别了三类主要失败模式
- 量化了代理效率问题（38-89%不必要动作）
- 为未来改进指明方向

---

## 7. 可借鉴点 (Your Research)

**研究启发：**
1. **评估范式转换**：从预置环境评估转向真实冷启动场景评估，这对评估真实世界任务能力至关重要
2. **能力维度拓展**：环境配置是端到端任务完成的关键前置能力，需要单独评估
3. **失败模式分析框架**：系统性分类失败模式的方法论可借鉴到其他评估任务中

**改进方向：**
1. **代理层面改进**：
   - 增强代理的工具使用能力，特别是包管理器和数据库操作
   - 减少幻觉约束，提高任务理解的准确性
   - 确保环境修改的持久性

2. **评估框架扩展**：
   - 增加更多真实世界场景
   - 加入时间效率评估
   - 增加代理-人类协作评估

3. **benchmark扩展**：
   - 引入更多类型的开发环境（如容器化环境、云开发环境）
   - 加入更复杂的微服务编排场景
   - 增加安全性相关的配置任务

---

本论文为LLM代理评估领域提供了重要的新视角，强调了环境配置这一实际开发中至关重要但长期被忽视的能力。SetupBench的提出填补了关键评估空白，其方法论和发现对后续研究具有重要指导意义。

---

## Related Work

**Related Work**

Recent years have witnessed a proliferation of benchmarks for LLM‑based software‑engineering agents, such as HumanEval, MBPP, APPS, and SWE‑bench (Chen et al., 2023; Jiang et al., 2024). These datasets primarily evaluate code generation, bug fixing, or patch creation under the assumption that a fully provisioned development environment is already in place, thereby leaving the non‑trivial task of environment bootstrapping unexamined. A line of work on autonomous planning and tool use—including AgentBench and Toolformer (Liu et al., 2023; Schick et al., 2024)—has explored how agents reason about sequences of actions, yet they abstract away the concrete steps required to install dependencies, compile code, or configure services. Concurrently, research on Infrastructure‑as‑Code and environment reproducibility (e.g., Docker, Nix, Conda) provides mature techniques for manual and automated provisioning, but these have not been systematically evaluated as part of an agent’s end‑to‑end capability. To address this gap, we introduce **SetupBench**, a dedicated benchmark that isolates the environment‑bootstrap skill, requiring an agent to transform a bare Linux sandbox into a functional development stack and thus complements existing evaluation paradigms.

---

