# 论文原文中文翻译汇总

**更新时间**: 2026-02-17

---

## 论文 1

# EnvBench: A Benchmark for Automated Environment Setup

**作者**: Aleksandra Eliseeva, Alexander Kovrigin, Ilia Kholkin, Egor Bogomolov, Yaroslav Zharov

**arXiv**: https://arxiv.org/abs/2503.14443

---

# 中文翻译

**发表于 ICLR 2025 会议论文**

**ENVBENCH：自动化环境配置基准测试**

arXiv:2503.14443v1 [cs.LG] 2025年3月18日

Aleksandra Eliseeva、Alexander Kovrigin、Ilia Kholkin*、Egor Bogomolov、Yaroslav Zharov
JetBrains Research
联系方式：alexandra.eliseeva@jetbrains.com

## 摘要

大型语言模型（LLMs）的最新进展使研究人员能够专注于软件工程领域的实际仓库级任务。在本工作中，我们考虑自动化软件仓库操作的核心任务——环境配置，即在系统上配置特定于仓库的开发环境。现有的环境配置研究引入了创新的智能体策略，但其评估通常基于小型数据集，这些数据集可能无法涵盖实际中遇到的各种配置挑战。为弥补这一差距，我们引入了一个综合性的环境配置基准测试 ENVBENCH。该基准测试包含329个Python仓库和665个JVM-based仓库（Java、Kotlin），重点关注真正具有配置挑战的仓库，排除了可以通过简单确定性脚本完全配置的项目。为了支持基准测试的扩展和模型调优，我们实现了两种自动指标：Python缺失导入的静态分析检查和JVM语言的编译检查。我们通过评估三种环境配置方法（包括一个简单的零样本基线和两个智能体工作流）来展示基准测试的实用性，并使用两个强大的LLM骨干模型GPT-4o和GPT-4o-mini进行测试。最佳方法成功配置了6.69%的Python仓库和29.47%的JVM仓库，表明ENVBENCH对当前方法仍具有挑战性。我们的基准测试套件公开可用的地址为 https://github.com/JetBrains-Research/EnvBench。数据集和实验轨迹可访问 https://jb.gg/envbench。

## 1 引言

大型语言模型（LLMs）的最新进展使其能够应用于包括软件工程在内的多个领域（Fan et al., 2023）。它们在推理和与外部环境交互方面的能力（Liu et al., 2024b; Wang et al., 2024b），以及高效处理大量信息的能力（Wang et al., 2024a），使研究人员能够解决实际的仓库级软件工程任务，如代码生成（Liu et al.; Zhao et al., 2024）、代码编辑（Jimenez et al., 2024）和代码理解（Ma et al., 2024; Luo et al., 2024; Liu et al., 2024a）。

在本工作中，我们关注程序员经常面临的另一个仓库级任务——环境配置，即配置系统以使用任意软件项目，例如新克隆的GitHub仓库。这通常需要安装依赖项，但可能包括任意特定于项目的步骤，如安装额外的系统包、设置正确的环境变量等。一个维护良好的项目应该可以 straightforward 设置，但实际上并不总是如此。例如，根据Stokers等人（2023）的研究，设置仓库被认为是重现自然语言处理（NLP）研究结果最具挑战性的部分，可能需要长达数小时。同样，Aghajani等人（2020）进行的调查显示，68%的开发者认为安装、部署和发布过程的不完整文档是一个重大问题，63%的开发者报告不适当的安装说明是普遍关注的问题。此外，自动化环境配置

* 在JetBrains Research实习期间完成的工作。

# 翻译结果

## 安装命令

```
v global 3.13.1
pip install -e .[all]
pip install -r reqs-dev.txt
```

## 评估结果

脚本执行无错误完成

Pyright输出：
缺失包：distutils、torch、numpy

## 图1：E NV B ENCH工作流程概述

该流程始于克隆目标仓库。随后，仓库作为输入传递给环境配置方法，该方法生成一个shell脚本来完成仓库的环境配置。在内部实现上，例如可以是单个LLM请求或由AI Agent动态构建的脚本。最后，在我们的评估套件中，执行生成的脚本，并通过静态分析和编译检查来验证环境是否正确配置。

这种方案有望扩展基于执行的基准测试的规模，而目前此类基准测试通常需要大量人工工作来选择一组可执行的仓库（Jimenez等，2024）。

迄今为止，很少有研究将环境配置作为独立任务来考虑。已有许多研究将环境配置作为更大任务的一部分——例如，科学复现（Siegel等，2024；Bogin等，2024）或解决机器学习问题（Tang等，2024）。然而，据我们所知，只有两项工作与我们的研究同时专注于环境配置（Milliken等，2024；Bouzenia & Pradel，2024）。虽然这些工作代表了环境配置自动化方面的重要进展，但它们主要关注新颖的Agent策略，而非全面的基准测试。这导致相应数据集中涵盖的软件项目和技术数量有限。例如，Milliken等（2024）的数据集包含40个Python仓库，Bouzenia & Pradel（2024）则为每种所考虑的语言（Python、Java、C、C++和JavaScript）各包含10个仓库。

基于此，我们引入了一个新颖的环境配置基准测试——E NV B ENCH。它包含多样化的项目集，涵盖Python（329个仓库）以及JVM语言如Java和Kotlin（共665个仓库）。我们实现了两种自动度量指标来验证环境配置的正确性——静态分析用于获取Python的缺失导入数量（即由于相应包未安装而无法通过静态分析解析的代码库中的导入语句数量），以及JVM语言的编译检查。我们通过实现简单的确定性shell脚本并排除仅靠这些脚本即可正确配置的仓库，确保基准测试中的项目具有真正的配置挑战。最后，我们使用两个强大的LLM（GPT-4o和GPT-4o-mini）评估了三种环境配置方法。我们的基线包括简单的零样本设置、具有Bash终端访问权限的ReAct（Yao等，2023）Agent工作流（类似于Bouzenia & Pradel，2024）（Bash Agent），以及遵循Milliken等（2024）的Agent设置（Installamatic Agent）。

我们的研究结果表明，使用GPT-4o的Bash Agent获得了最高的成功率，正确配置了29.47%的JVM仓库和6.69%的Python仓库。尽管Python的环境配置仍然具有挑战性，但与确定性脚本相比，LLM方法仍能减少许多仓库的缺失导入数量，展示了它们的潜力。此外，我们观察到未明确获得错误反馈的LLM方法通常会产生错误的环境配置脚本。这与之前的研究发现一致（Milliken等，2024），即多次带错误反馈的生成尝试能显著提高环境配置能力。

我们的基准测试套件可公开访问：https://github.com/JetBrains-Research/EnvBench。数据集和实验轨迹可访问：https://jb.gg/envbench。

---

## 相关工作

环境配置，即创建一个可运行的软件环境的过程...

# 中文翻译

软件仓库开发环境的搭建是软件开发中的一项关键任务。不同编程语言之间乃至同一技术栈的不同仓库之间，所涉及的步骤差异很大，这使得该任务的自动化变得复杂。此外，从大规模数据集中获取可执行仓库的子集是基于执行基准测试的常见步骤。在最新的基准测试中，仓库是否可执行的检查通常采用半自动方式，需要大量人工操作（Jimenez等人，2024；Jain等人，2024；Tang等人，2024；Zhao等人，2024）。

**方法**。鉴于任意仓库环境配置的内在变异性，非机器学习的自动方法受到限制，通常仅限于特定生态系统。例如，有些工具可以从Python仓库的源代码中收集外部依赖（Gruber & Fraser，2023；Vadim Kravcenko，2014），但配置系统以确保成功安装不在其职责范围内。最近，有两个用于环境搭建的AI智能体被提出。Milliken等人（2024）提出的INSTALLAMATIC能够成功搭建40个目标Python仓库中的21个。Bouzenia & Pradel（2024）提出的EXECUTIONAGENT能够正确配置50个目标仓库中的33个，涵盖5种编程语言（Python、Java、C、C++和JavaScript）。

**基准测试**。近期有多项研究致力于科学复现（Siegel等人，2024；Bogin等人，2024）或解决机器学习问题（Tang等人，2024）——尽管这两项任务都可能涉及环境搭建，但评估是以端到端方式进行的，对于当前大语言模型在环境搭建过程中面临的挑战缺乏深入了解。据我们所知，目前有两个专门针对环境搭建任务的基准测试——我们沿用其伴随方法的名称，分别称为INSTALLAMATICbench（Milliken等人，2024）和EXECUTIONAGENTbench（Bouzenia & Pradel，2024）。INSTALLAMATICbench包含40个Python仓库。Milliken等人（2024）手动检查了这些仓库，并提供了与安装相关的真实上下文信息以及每个仓库的示例Dockerfile。预期输出为Dockerfile，成功指标为至少有一个测试用例能够通过生成的Dockerfile运行。EXECUTIONAGENTbench涵盖5种编程语言（Python、Java、C、C++和JavaScript），每种语言10个仓库。 Bouzenia & Pradel（2024）选择了提供CI日志的仓库，从而为每个仓库提供了测试套件执行的真实结果。预期输出包括指定系统配置的Dockerfile以及用于搭建环境并运行测试的Shell脚本。在评估方面，作者考虑了三个指标：成功构建率、成功测试率，以及与真实结果在通过、失败和跳过测试数量上的偏差。前两个指标需要人工检查。此外，这两项研究都聚焦于相对流行的项目：Milliken等人（2024）考虑了GitHub上至少1000星的仓库，而Bouzenia & Pradel（2024）则考虑了至少100星的仓库。

**我们的贡献**。与现有基准测试相比，我们的基准测试覆盖了更广泛的994个仓库，涵盖三种编程语言（Python、Java、Kotlin）和两个不同的生态系统（Python和JVM）。我们采用了更宽松的星标过滤器（最低10星），并排除了可通过简单确定性脚本配置的仓库，以确保真正的环境搭建挑战。与依赖测试执行的基准测试不同，我们使用静态分析（Python）和编译检查（JVM）来验证搭建成功。与Bouzenia & Pradel（2024）类似，我们采用Shell脚本输出格式作为环境搭建方法的输出，但我们提供了预定义的Dockerfile模板。

以确保基准系统配置在所有方法中保持一致。

3 环境基准测试

在本节中，我们描述环境基准测试（EnvBench）——我们用于环境设置任务的基准测试。我们考虑用Python或基于JVM的语言（具体为Java和Kotlin）编写的代码库，代表了两个流行但根本不同的技术栈。关于基准测试中代码库的更多信息，请参见附录A.1。环境基准测试可通过https://jb.gg/envbench获取。我们的评估套件及其他相关代码可通过https://github.com/JetBrains-Research/EnvBench获取。

1 例如，根据GitHub于2024年10月发布的Octoverse报告，Python是GitHub上使用最多的语言，Java排名第四：https://github.blog/news-insights/octoverse/octoverse-2024/

3.1 任务定义

图1展示了我们基准测试中预期工作流程的概述。输入与输出。在我们的基准测试中，环境设置方法的输入是完整的代码库内容，预期的输出是一个配置代码库的Shell脚本。代码库内容的处理和脚本生成方法都是该方法的组成部分。例如，该过程可能涉及单一的大语言模型请求，其中使用预定义算法收集代码库上下文，或者一个大语言模型代理，该代理动态探索代码库并通过提供的工具执行Shell命令以生成脚本。评估指标。鉴于Python和JVM语言之间的差异，我们实现了两个不同的指标来评估代码库环境是否配置正确。对于Python，我们运行流行的静态分析工具pyright（Microsoft, 2019），并统计与缺失依赖项相关的报错数量（具体为reportMissingImports类型）。对于JVM语言，我们尝试通过Gradle（gradle build命令）或Maven（mvn compile命令）构建代码库，检查构建是否成功（对于两者），并报告构建工具输出中的错误数量（Maven）。在基准测试构建过程中，我们验证所包含的基于JVM的代码库是根据配置文件的存在使用Gradle或Maven构建工具。如果配置代码库的脚本不正确，这两个指标都可以以非零退出码结束执行。在大多数考虑的配置中，我们的指标既允许二进制成功指标（零退出码和零报错），也允许基于每个代码库报错数量的连续度量，这可以缓解那些客观上无法成功设置的情况。与我们的方法相反，以往关于环境设置的研究采用了基于测试套件的指标（Milliken et al., 2024; Bouzenia & Pradel, 2024），这些指标可能更接近实际用例。参照Bouzenia & Pradel（2024），环境设置的基于执行的指标可能包括三个标准：成功构建（或安装）项目、能够运行测试套件，以及测试套件按预期工作。每一步都依赖于之前所有步骤的成功完成。在我们的表述中，我们涵盖了构建（安装）步骤，发现了环境设置方法可能面临的大多数问题，除了那些仅在运行时出现的问题。我们的指标比基于测试套件的指标更为轻量，使我们能够扩展基准测试，而我们的实验表明，即使是强大的基于大语言模型的环境设置方法，环境基准测试仍然具有挑战性（参见第5节我们的实验结果）。随着环境设置方法的发展，环境基准测试可以扩展基于测试套件的指标，确保其保持挑战性并与实际用例紧密相关。

3.2 评估套件

环境设置本质上意味着执行修改系统配置的操作。

配置。为防止自动化方法可能破坏主机系统，我们实现了一个评估套件，每个代码仓库的解决方案都在Docker容器（Docker, Inc., 2013）中运行。总体评估流程如下。评估套件接收以下输入：代码仓库名称、需要考虑的代码仓库版本，以及由环境配置方法预先生成的代码仓库环境设置Shell脚本。我们将代码仓库克隆到Docker容器中，执行提供的Shell脚本，如果脚本执行成功，则执行相应语言的指标。我们发布了两个基础Docker镜像，分别用于Python和JVM语言，它们提供了最少的相关工具集。关于评估套件Docker配置的更多细节见附录A.2。

**数据收集与筛选**

数据收集。为了构建ENVBENCH，我们首先使用专用工具GitHub Search（Dabic et al., 2021）获取GitHub代码仓库的多样化列表。我们的选择标准包括：代码仓库的主要语言为Python、Java或Kotlin；代码仓库具有宽松许可证；以及一组质量过滤器，用于排除可能引入偏见的项目（Kalliamvakou et al., 2014）：至少1000次提交；至少10个Issue；至少10位贡献者；至少10个星标；最后一次提交不早于2024年1月1日。通过这种方式，我们将重点放在成熟且活跃的项目上，同时兼顾流行项目和在知名度上相对较低的项目。随后，我们克隆了截至2024年7月仍可访问的代码仓库源代码，最终获得Python代码仓库2590个和JVM语言代码仓库1688个。

内容筛选。我们研究了代码仓库中的配置文件，发现大多数Python项目使用pip（Python Packaging Authority, 2008）或Poetry（Python Poetry, 2018）来管理依赖，而其他依赖管理工具的使用情况可以忽略不计。类似地，大多数JVM项目使用Gradle（Gradle, Inc., 2010）或Maven（Apache Software Foundation, 2004）构建工具。因此，下一步我们过滤掉在根目录中不包含相应依赖管理器关联的配置文件或包含多个配置文件的代码仓库。这样做是为了避免将单体仓库（monorepo）纳入我们的样本——即从配置角度来看包含多个不同项目的代码仓库，因为配置单体仓库是一项更具挑战性的任务，评估结果可能存在较大歧义。此步骤后，我们保留了Python代码仓库743个和JVM语言代码仓库1487个。此外，我们还过滤掉包含Docker相关配置文件的代码仓库（Docker, Inc., 2013）（如Dockerfile或docker-compose.yml）。我们依赖Docker来沙箱化评估和实验（详见第3.2节和第4节），而在Docker容器内运行Docker会带来重大技术挑战。此外，Docker常被用于简化和封装环境配置，这可能会绕过关键挑战。经过此筛选步骤后，我们获得了Python代码仓库391个和JVM语言代码仓库977个。

基线筛选。直观地说，对于许多代码仓库而言，环境配置可能简单到只需运行`pip install -r requirements.txt`。为确保我们的基准测试保持一定的难度，我们实现两个完全确定性的Shell脚本，分别用于Python和JVM语言（详见附录A.3）。其核心算法类似：（1）分析配置文件以确定所需的依赖管理器及Python/Java版本；（2）验证是否已安装正确的Python/Java版本，必要时进行安装；（3）使用相

**翻译结果：**

ified依赖管理器（用于Python；JVM的项目构建被排除，因为它们在评估期间执行）。
我们运行基线脚本并使用第3.2节中概述的评估套件来评估其性能。对于Python，脚本成功（如第3.1节所定义）配置了62个仓库（15.9%），而对于JVM语言——309个仓库（31.6%）。从上一个步骤获取的样本中，我们无法处理3个仓库。在过滤掉这些无法处理的仓库以及基线脚本成功配置的仓库后，我们得到了一个包含329个Python仓库和665个JVM仓库的最终数据集。

4.1

**实验设置**

**数据集与指标**

我们的数据集EnvBench包含329个Python仓库和665个JVM仓库。其构建详情在第3节中描述。我们实现了两种语言特定的指标，分别依赖于静态分析（用于Python）或编译检查（用于JVM语言）来确认仓库是否配置正确（参见第3.1节）。这些指标输出观察到的错误数量，然而，根据环境设置方法生成的shell脚本，它们也可能以非零退出代码提前退出。我们考虑两种指标：pass@1，一个二元成功度量——其中成功定义为退出代码和报告的错误均为零——以及avgErrs——每个仓库平均报告的错误数量——用于量化设置完成的程度。请注意，avgErrs只能针对环境设置脚本以零退出代码完成执行的仓库计算。

4.2

**基线方法**

我们在实验中考虑了三种基于LLM的基线方法。对于每种基线方法，我们使用两种专有LLM进行实验：GPT-4o和GPT-4o-mini。各基线方法的实现细节参见附录B。

**零样本LLM。** 我们构建了一个包含仓库信息（包括目录结构、README内容以及配置文件内容）的提示，以及我们评估套件的系统配置信息（第3.2节），并向LLM发送单个请求，要求其生成一个能够正确配置给定仓库环境的shell脚本。

**Installamatic代理。** 我们考虑由Milliken等人（2024）介绍的环境设置代理Installamatic。Installamatic包含两个阶段：（1）搜索阶段，代理在此阶段探索仓库内容；以及（2）构建/修复阶段，代理在此阶段生成并测试Dockerfile，允许在失败时进行多次重新生成。我们稍微调整了Installamatic以匹配我们的评估设置（第3.2节详情），要求代理生成shell脚本而非Dockerfile，向代理提供关于我们评估套件系统配置的额外信息，并为JVM仓库制作了单独的提示。我们也不允许重新生成尝试，并报告代理生成的脚本第一个版本的结果，这是Milliken等人（2024）考虑的设置之一。在这种 formulation中，Installamatic可以被视为零样本LLM的扩展，它使用代理进行上下文收集而非预定义的提示模板；然而，shell脚本生成仍然作为单个LLM请求执行。

**Bash代理。** 我们考虑一个ReAct（Yao等人，2023）代理，它以迭代方式处理任务，基于先前的观察产生想法和动作。作为可用的动作，我们提供了一个execute bash命令工具，允许通过shell命令与系统交互。出于安全考虑，我们在Docker容器内执行代理发出的命令。与零样本LLM和Installamatic相比，这种基线方法将上下文收集和shell脚本生成结合在一个由LLM代理完全控制的迭代和动态过程中。

此外，Bash Agent可以被视为Bouzenia & Pradel（2024）所引入的EXECUTIONAGENT的简化版本，二者具有相似性，但Bash Agent缺少一些组件，如元提示（meta-prompting）和Shell命令输出的摘要功能。

**结果与讨论**

**JVM**

| 基准模型 | pass@1 ↑ | avgErrs ↓ (Maven) | pass@1 ↑ (Python) | avgErrs ↓ (overlap) |
|---------|---------|-------------------|-------------------|---------------------|
| Zero-shot LLM (GPT-4o) | 8.57% | 480.50 | 5.47% | 54.89 |
| Zero-shot LLM (GPT-4o-mini) | 11.13% | 202.97 | 4.56% | 151.30 |
| Installamatic Agent (GPT-4o) | 1.35% | 21.43 | 4.86% | 108.93 |
| Installamatic Agent (GPT-4o-mini) | 3.01% | 33.53 | 2.74% | 83.57 |
| Bash Agent (GPT-4o) | 29.47% | 26.84 | 6.69% | 52.00 |
| Bash Agent (GPT-4o-mini) | 26.77% | 24.77 | 5.47% | 79.89 |

**表1：主要实验结果。** pass@1——正确设置仓库的百分比，即我们的指标返回零退出代码且报告零问题的仓库。avgErrs——每个仓库平均报告的错误数量。对于JVM，我们仅报告使用Maven构建工具的仓库的avgErrs。需要注意的是，每个基准模型的avgErrs只能基于相应基准模型的环境设置脚本以零退出代码完成执行的仓库进行计算。对于Python，我们报告在所有基准模型都能生成以零退出代码完成执行的脚本的44个仓库上计算的avgErrs。对于JVM，不存在此类仓库，因此我们为每个基准模型指定计算avgErrs所涉及的仓库数量。符号↑表示当前列中数值越高越好，而↓表示数值越低越好。

**发表于ICLR 2025会议论文**

我们在表1中展示了实验的主要结果。在pass@1指标上，以GPT-4o为骨干的Bash Agent是JVM和Python环境设置表现最佳的方法，分别成功设置了29.47%和6.69%的所考虑仓库（使用GPT-4o）。Zero-shot LLM在JVM仓库上表现明显较差（使用GPT-4o-mini时为11.13%），在Python上略差（使用GPT-4o时为5.47%）。尽管Installamatic Agent比Zero-shot LLM集成了更先进的上下文收集功能，但它在所考虑的基准模型中排名最低，JVM的pass@1为3.01%（使用GPT-4o-mini），Python为4.86%（使用GPT-4o）。我们假设其根本原因可能是Installamatic中的提示主要鼓励考虑自然语言文档，而这些文档在Milliken等人（2024）原始考虑的数据集中可能质量更高，这是由于流行度过滤器（平均星标数为19k，而我们的数据集中为1.9k）。

我们的第二个指标avgErrs表明，对于表现最佳的Python基准模型（Bash Agent与GPT-4o），每个仓库平均缺少52.00个导入；对于使用Maven构建工具的JVM仓库，Installamatic Agent与GPT-4o每个仓库平均有21.43个错误。然而，avgErrs只能在基准模型生成的环境设置脚本无错误完成执行的情况下计算。我们研究了为Python和JVM生成的Shell脚本的退出代码，并观察到所考虑的基准模型可能难以实现这一点，这使得通过avgErrs进行比较变得复杂（详见附录C.3）。这个问题在JVM上比在Python上更为突出。

**仓库**

| 基准模型 | 模型 | 更少 ↑ | 相同 | 更多 ↓ | 平均减少 ↑ | 平均增加 ↓ |
|---------|------|--------|------|--------|-----------|-----------|
| Zero-shot LLM | GPT-4o | 53% | 34% | 13% | 59% | 487% |
| Zero-shot LLM | GPT-4o-mini | 39% | 42% | 19% | 56% | 589% |
| Installamatic Agent | GPT-4o | 42% | 34% | 24% | 63% | 286% |
| Installamatic Agent | GPT-4o-mini | 31% | 46% | 23% | 54% | 303% |
| Bash Agent | GPT-4o | 31% | 52% | 17% | 53% | 206% |
| Bash Agent | GPT-4o-mini | 41% | 41% | 18% | 51% | 211% |

**表2：在Python样本上所考虑基准模型的每个仓库缺失导入数比较，相对于...**

确定性脚本在第3.3节中有所描述。较少、相同和较多分别表示基线方法产生较少、相同或较多缺失导入的代码仓库数量。平均减少和平均增加列表示在基线优于（较少）或劣于（较多）确定性脚本的代码仓库中，缺失导入的平均百分比变化。统计数据仅针对基线和确定性脚本均以零退出码退出的代码仓库计算，每个基线对应的代码仓库集合可能有所不同。符号↑表示当前列中数值越高越好，而↓表示数值越低越好。

为了进一步分解Python环境配置基线的性能，我们将它们与专家生成的脚本（作为上限）以及我们在基准测试构建过程中实现的简单确定性脚本（作为下限）进行比较。对于前者，我们随机抽取30个代码仓库进行观察，发现专家生成的脚本与所有考虑的基线之间存在显著差距（更多详情见附录D）。我们的专家生成脚本在该样本上达到了66.7%的pass@1，而最佳基线仅达到10.0%的pass@1。对于后者，我们在表2中报告了比较结果。对于一小部分代码仓库（从使用GPT-4o的Installamatic Agent的24%到使用GPT-4o的零样本LLM的13%），基线方法生成的脚本产生的缺失导入比确定性脚本更多。然而，在这种情况下，相对于确定性脚本，每个代码仓库的缺失导入数量平均增加至少200%，对于所有考虑的基线都是如此。这些代码仓库可以为当前方法的局限性提供有价值的见解，并作为未来开发更鲁棒的环境配置策略的基础。

另一方面，在所有考虑的基线中，基线生成的脚本优于确定性脚本的代码仓库比例更高（从使用GPT-4o的Bash Agent的31%到使用GPT-4o的零样本LLM的53%）。在这种情况下，相对于确定性脚本，每个代码仓库的缺失导入数量平均减少约50%-60%，对于所有考虑的基线都是如此。总体而言，尽管Python的完全正确环境配置仍然具有挑战性，但与简单的确定性脚本相比，所考虑的基线显示出潜力。

局限性

我们的工作存在几个重要的局限性，列举如下。

Docker支持。我们明确将需要Docker进行配置的项目排除在基准测试之外。虽然Docker在现代开发工作流程中越来越常见，但包含此类项目会增加我们评估设置和指标的复杂性。未来的工作可以探索将我们的方法扩展到处理更广泛的项目范围。

数据污染是另一个潜在问题，因为我们使用的大语言模型是在可能与我们的基准测试数据集重叠的公共代码仓库上训练的。这可能导致模型在预训练期间已经预先接触了某些代码仓库的配置说明。然而，考虑到环境配置指南通常没有明确的文档记录，即使存在，也需要高级推理能力来正确理解和遵循，我们认为这不会显著影响我们的发现。由于我们的基准测试构建不需要大量的人工工作，未来可以通过纳入更新的代码仓库来更新它，从而降低随着大语言模型训练数据集演变而带来的数据污染风险。

开源代码质量。开源代码和文档的质量在不同代码仓库之间差异显著。某些代码仓库的文档可能不完整或已过时。

这对环境配置构成了重大挑战，使得环境设置变得尤为困难。其他代码库可能由于缺少关键文件、依赖损坏或配置不兼容而完全无效或无法设置。我们的结果可能受到代码质量和仓库有效性的内在变异性的影响，尽管这反映了现实世界条件下自动化工具需要处理的情况。为缓解这一担忧，我们同时采用二进制成功指标和每个仓库报告的错误数量来量化环境设置完成的程度。作为未来工作的一部分，我们的基准测试可以进一步手动验证，以识别和移除无效样本。

静态分析与编译。我们的评估依赖于Python的静态分析和JVM语言的编译检查，而非实际执行指标（如测试套件运行）。虽然这为环境设置成功提供了合理的代理指标，但可能会遗漏仅在执行过程中出现的运行时问题。然而，这种方法使我们能够高效地评估更多仓库的环境设置，同时避免测试套件执行的复杂性，这使我们的基准测试更具代表性，并允许可能重用已构建的基础设施进行进一步研究，该研究可能不仅涉及评估，还涉及环境设置方法的训练。此外，我们通过为30个随机采样的Python仓库手动实施设置脚本并研究指标行为来验证所提出指标的稳健性（详见附录D）。

7

结 论

本工作提出了ENV BENCH——一个用于评估自动化环境设置方法的基准测试，通过覆盖329个Python仓库和665个JVM仓库，解决了以往环境设置数据集规模有限的问题。我们的评估套件基于Python的静态分析和JVM的编译检查，能够对环境设置策略进行系统评估。

我们评估了三种环境设置方法，包括两种AI代理，并使用了两种强大的大语言模型作为主干。我们的结果表明，环境设置对这些方法来说仍然具有挑战性，表现最好的方法仅成功配置了29.47%的JVM仓库和6.69%的Python仓库。一个关键挑战是错误脚本的生成，我们将其留待未来工作进行进一步研究和缓解。

我们的基准测试及相关代码已公开可用，为进一步研究提供了可扩展的平台。未来，它可以进一步手动验证并扩展，以纳入新的软件仓库、更多的编程语言以及基于运行时的评估指标。

参考献

Emad Aghajani, Csaba Nagy, Mario Linares-Vásquez, Laura Moreno, Gabriele Bavota, Michele Lanza和David C. Shepherd。软件文档：从业者的视角。收录于ACM/IEEE第42届国际软件工程会议论文集，ICSE '20，第590-601页，美国纽约，2020年10月。计算机协会。ISBN 978-1-4503-7121-6。doi: 10.1145/3377811.3380405。URL https://doi.org/10.1145/3377811.3380405。

Apache软件基金会。Apache Maven。https://github.com/apache/maven，2004。访问日期：2025-01-21。

Ben Bogin, Kejuan Yang, Shashank Gupta, Kyle Richardson, Erin Bransom, Peter Clark, Ashish Sabharwal和Tushar Khot。SUPER：在研究仓库中设置和执行任务上评估代理，2024年9月。URL http://arxiv.org/abs/2409.07440。arXiv:2409.07440 [cs]版本：1。

Islem Bouzenia和Michael Pradel。你命名它，我运行它：用于执行任意项目测试的大语言模型代理。arXiv预印本arXiv:2412.10133，2024。

Ozren Dabic, Emad Aghajani和Gabriele Bavota。在GitHub中为MSR研究采样项目。收录于2021年IEEE/ACM第18届软件仓库挖掘国际会议（MSR），第560-564页。IEEE，2021。

Docker公司。Docker：赋予...

Angular 应用开发面向开发者。 https://www.docker.com/, 2013. 访问日期：2025-01-21。

Angela Fan, Beliz Gokkaya, Mark Harman, Mitya Lyubarskiy, Shubho Sengupta, Shin Yoo, Jie M Zhang. 大型语言模型在软件工程中的应用：综述与开放问题。收录于 2023 年 IEEE/ACM 国际软件工程会议：软件工程的未来（ICSE‑FoSE），第 31–53 页。IEEE，2023 年。

Gradle, Inc. Gradle. https://github.com/gradle/gradle, 2010. 访问日期：2025-01-21。

Martin Gruber, Gordon Fraser. FlaPy：大规模挖掘不稳定的 Python 测试，2023 年 5 月。URL http://arxiv.org/abs/2305.04793。arXiv:2305.04793 [cs]。

Naman Jain, Manish Shetty, Tianjun Zhang, King Han, Koushik Sen, Ion Stoica. R2e：将任意 GitHub 仓库转变为编程智能体环境。收录于 ICML 2024。

Carlos E Jimenez, John Yang, Alexander Wettig, Shunyu Yao, Kexin Pei, Ofir Press, Karthik R Narasimhan. SWE‑bench：语言模型能否解决真实世界的 GitHub 问题？收录于第十二届国际学习表征会议（ICLR）2024。URL https://openreview.net/forum?id=VTF8yNQM66。

Eirini Kalliamvakou, Georgios Gousios, Kelly Blincoe, Leif Singer, Daniel M. German, Daniela Damian. 挖掘 GitHub 的前景与风险。收录于第十一届软件仓库挖掘工作会论文集（MSR 2014），第 92–101 页，纽约，纽约州，美国，2014 年。Association for Computing Machinery. ISBN 9781450328630. doi: 10.1145/2597073.2597074. URL https://doi.org/10.1145/2597073.2597074。

LangChain. Langgraph：构建可恢复的语言智能体图谱。https://github.com/langchain-ai/langgraph。访问日期：2025-02-08。

Jiawei Liu, Jia Le Tian, Vijay Daita, Yuxiang Wei, Yifeng Ding, Yuhan Katherine Wang, Jun Yang, Lingming Zhang. Repoqa：评估长上下文代码理解。arXiv 预印本 arXiv:2406.06025，2024a。

Junwei Liu, Kaixin Wang, Yixuan Chen, Xin Peng, Zhenpeng Chen, Lingming Zhang, Yiling Lou. 基于大型语言模型的软件工程智能体：综述。arXiv 预印本 arXiv:2409.02977，2024b。该论文在 ICLR 2025 会议上作为会议论文发表。

Tianyang Liu, Canwen Xu, Julian McAuley. Repobench：仓库级代码自动补全系统的基准测试。收录于第十二届国际学习表征会议（ICLR）。

Qinyu Luo, Yining Ye, Shihao Liang, Zhong Zhang, Yujia Qin, Yaxi Lu, Yesai Wu, Xin Cong, Yankai Lin, Yingli Zhang 等。Repoagent：一个基于大型语言模型的开源仓库级代码文档生成框架。arXiv 预印本 arXiv:2402.16667，2024。

Yingwei Ma, Qingping Yang, Rongyu Cao, Binhua Li, Fei Huang, Yongbin Li. 如何理解完整的软件仓库？arXiv 预印本 arXiv:2406.01422，2024。

Microsoft. Pyright：Python 静态类型检查器。https://github.com/microsoft/pyright, 2019. 访问日期：2025-01-21。

Louis Milliken, Sungmin Kang, Shin Yoo. 超越 pip install：评估用于自动化安装 Python 项目的 LLM 智能体。arXiv 预印本 arXiv:2412.06294，2024。

Python Packaging Authority. pip。https://github.com/pypa/pip, 2008. 访问日期：2025-01-21。

Python Poetry. Poetry。https://github.com/python-poetry/poetry, 2018. 访问日期：2025-01-21。

Zachary S. Siegel, Sayash Kapoor, Nitya Nagdir, Benedikt Stroebl, Arvind Narayanan. COREBench：通过计算可复现性智能体基准提升已发表研究的可信度，2024 年 9 月。URL http://arxiv.org/abs/2409.11363。arXiv:2409.11363 [cs]。

Shane Storks, Keunwoo Yu, Ziqiao Ma, Joyce Chai. 面向所有人的 NLP 可复现性：理解初学者的体验。收录于第 61 届计算语言学年会论文集（第 1 卷：长论文），第 10199–10219 页，2023 年。

Xiangru Tang, Yuliang Liu, Zefan Cai, Yanjun Shao, Junjie Lu, Yichi Zhang, Zexuan Deng, Helan Hu, Kaikai An, Ruijun Huang, Shuzheng Si, Sheng Chen, Haozhe Zhao, Liang Chen, Yan Wang, Tianyu Liu, Zhiwei Jiang, Baobao Chang, Yin Fang, Yujia Qin, Wangchunshu Zhou, Yilun Zhao, Arman Coh。

以下是该段落的学术中文翻译：

---

**an**, 和 **Mark Gerstein**。ML-Bench：针对代码库级机器学习任务的大型语言模型及智能体评估，2024年8月。URL http://arxiv.org/abs/2311.09835。arXiv:2311.09835 [cs]。

**Vadim Kravcenko**。pipreqs：根据任意项目的导入生成 pip requirements.txt 文件。https://github.com/bndr/pipreqs，2014。访问日期：2025-02-06。

**Xindi Wang**, **Mahsa Salmani**, **Parra Omidi**, **Xiangyu Ren**, **Mehdi Rezagholizadeh**, 和 **Armaghan Eshaghi**。超越极限：大型语言模型中扩展上下文长度技术的综述。arXiv 预印本 arXiv:2402.02244，2024a。

**Yanlin Wang**, **Wanjun Zhong**, **Yanxian Huang**, **Ensheng Shi**, **Min Yang**, **Jiachi Chen**, **Hui Li**, **Yuchi Ma**, **Qianxiang Wang**, 和 **Zibin Zheng**。软件工程中的智能体：综述、 landscape 与展望。arXiv 预印本 arXiv:2409.09030，2024b。

**Shunyu Yao**, **Jeffrey Zhao**, **Dian Yu**, **Nan Du**, **Izhak Shafran**, **Karthik Narasimhan**, 和 **Yuan Cao**。ReAct：在语言模型中协同推理与行动。发表于国际学习表征会议（ICLR），2023。

**Wenting Zhao**, **Nan Jiang**, **Celine Lee**, **Justin T Chiu**, **Claire Cardie**, **Matthias Gallé**, 和 **Alexander M Rush**。Commit0：从零开始生成代码库。arXiv 预印本 arXiv:2412.01769，2024。

---

**A 基准测试**

在本节中，我们提供关于 **EnvBench** 评估套件以及用于构建基准测试的确定性脚本的更多细节。

**A.1 基准测试统计信息**

表3展示了关于我们基准测试中代码库的更多信息。

**表3：基准测试代码库的统计信息。**

| 语言 | 平均星标数 | 平均文件数 | 依赖管理器分布 |
|------|-----------|-----------|---------------|
| Python | 1469 | 779 | Pip: 82.06% / Poetry: 17.94% (270/329) |
| JVM | 2079 | 2647 | Gradle: 59.70% / Maven: 40.30% (397/665) |

**A.2 Docker 环境**

我们使用 Docker（Docker, Inc., 2013）来构建评估套件和实验环境，以安全地将 LLM 生成的脚本与主机系统隔离。我们实现了两个 Docker 环境，一个用于 Python，一个用于 JVM 语言，并在其中预安装了常用的工具。我们使用预配置的统一 Docker 镜像，因为这样可以节省时间（标准工具已预安装），确保所有方法在相同的可复现环境中运行，并能够缓解常见问题（例如，我们初步实验表明，如果系统中未预先安装 Android SDK，所有被评估的方法都难以完成安装）。我们的 Docker 镜像基于 ubuntu:22.04，并对所有工具使用非交互模式。Dockerfile 文件可在我们的 GitHub 仓库中获取：https://github.com/JetBrains-Research/EnvBench。

**Python 环境**：我们预安装了：
- pyenv（Python 3.8-3.13）
- Poetry（依赖管理）
- uv（包安装）
- pyright（静态分析）
- pipenv
- conda/miniconda

**JVM 环境**：我们预安装了：
- sdkman（Java 11.0.20-tem）
- Maven
- Gradle
- Node.js 和 npm
- Android SDK

**A.3 确定性脚本**

我们实现了两个确定性脚本，用于处理最简单的环境配置场景。这些脚本依赖于代码库内容来确定所需的依赖管理器和 Python/Java 版本要求。具体脚本可在我们的 GitHub 仓库中获取：https://github.com/JetBrains-Research/EnvBench。

**Python 脚本**：
- 通过检查 environment.yml（Conda）、uv.lock（uv）或 poetry.lock（Poetry）来检测环境类型
- 创建并激活相应的虚拟环境
- 通过搜索 requirements.txt、setup.py、pyproject.toml、setup.cfg 或 Pipfile 来安装依赖
- 如未找到识别的配置文件，则以错误代码退出

**JVM 脚本**：
- 通过检查 pom.xml（Maven）或 build.gradle（Gradle）来检测构建系统
- 从构建文件中确定 Java 版本，默认为 Java 11
- 运行相应的构建命令（mvn install 或 gradle build）

以下是中文翻译：

**跳过测试或离线模式等常见构建标志**

12

---

**LLM基线实现细节**

在本节中，我们分享了所考虑的基于LLM的环境设置基线的实现细节。这些实现可在我们的仓库中获取：https://github.com/JetBrains-Research/EnvBench。

对于零样本LLM，我们首先按照预定义的步骤为每个仓库收集相关上下文。具体而言，对于两种语言，我们提供以下信息：目录结构（tree、ls -R）、文档内容（README、安装指南、Markdown文件）、环境信息（Dockerfile）、确定性脚本（见附录A.3）作为对应语言的示例。对于Python，我们提供：常见配置文件（setup.py、pyproject.toml）、依赖规范（requirements.txt）、Python版本要求（若存在于配置文件中）、init .py文件的内容。对于JVM，我们提供：构建文件（pom.xml、build.gradle、settings.gradle）、依赖和锁定文件、Java版本要求（若存在于构建文件中）、构建工具包装脚本、module-info.java文件。

在收集上下文后，我们利用它来提示LLM生成环境设置Shell脚本。

Bash Agent是一个ReAct（Yao et al., 2023）代理，被授予访问Docker容器终端的权限以执行环境设置（有关Docker配置的详细信息见附录A.2）。该代理配备了一个执行bash命令的工具，该工具接受命令并在命令执行后返回stdout内容和stderr内容。我们使用LangGraph（LangChain）框架来实现Bash Agent。代理最多允许30次迭代，若LLM在其响应中未使用工具，则执行提前终止。我们为每个发出的命令设置360秒的超时时间。我们允许每个命令的输出最多5000个字符，以避免过长且无信息量的上下文，若超过此值则返回输出的前半部分和后半部分。为了获得 resulting shell script，我们将所有以退出码零完成的已执行命令包含在内。

对于Installamatic Agent，我们遵循原始设置（Milliken et al., 2024）但排除修复阶段：代理允许进行一次完整的文档收集阶段迭代和一次完整的Dockerfile构建阶段迭代。在文档收集阶段，Installamatic Agent通过给定工具探索仓库，直至调用finish search tool，预期输出被视为与安装相关的文件列表。在Dockerfile构建阶段，代理允许通过同一组工具探索与安装相关的文件，直至调用submit summary tool；通过该工具，代理生成关于设置当前仓库所需信息的自然语言摘要，之后代理生成Dockerfile。我们重用原始提示，但将任务重新表述为生成shell脚本，并用有关我们评估套件Docker配置的信息扩展提示。我们使用LangGraph（LangChain）框架来实现Installamatic Agent。

---

**评估结果**

在本节中，我们提供实验的额外结果。

**案例研究**

在本节中，我们展示代理和零样本LLM生成的脚本示例。在图2中，我们比较了Python项目tablib（一种格式无关的表格数据集库）的基线性能。

a jazzband/tablib
¬ Bash Agent
Exit: 0

eval "$(pyenv init -)"
ls -R
cat pyproject.toml
cat tests/requirements.txt
pyenv versions
pyenv global 3.10.13
pip install -e .[all]
python -c 'import tablib'

Issues: 0

2121267

Á 零样本LLM

« Installamatic

PYTHON_VERSION="3.13.1"
pyenv install -f $PYTHON_VERSION
pyenv global $PYT

## 中文翻译

### 环境配置命令

```bash
python -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -e .
if [ -f "tests/requirements.txt" ]; then
pip install -r tests/requirements.txt
fi

PYTHON_VERSION="3.8.18"
pyenv install -f $PYTHON_VERSION
pyenv global $PYTHON_VERSION
pip install -e .
apt-get update -y && apt-get install -y \
odfpy \
openpyxl \
pandas \
pyyaml \
tabulate \
xlrd \
xlwt
```

退出码：0

问题数：2

退出码：100

问题数：不适用

**图2：Tablib仓库的基线对比。** 所有基线模型均使用GPT-4o-mini。为简洁起见，已删除注释行。

Tablib仓库代表了一个典型的Python项目配置场景，其特点如下：

- README中未提供明确的安装说明
- 不存在独立的setup.py或安装脚本
- 依赖项和项目元数据通过pyproject.toml管理
- 可选依赖项定义为"extras"，可通过.[组名]方式安装

这种常见配置要求安装工具正确解析pyproject.toml文件，以确定Python版本要求和依赖项。

**Bash Agent成功安装包的方法：**

- 读取pyproject.toml以确定Python版本要求（≥3.9）
- 通过.[all]安装所有可选依赖项，包括所有文件格式支持
- 使用最少的必要步骤，无多余操作

**Zero-shot LLM方法虽完成但遗漏了可选依赖项：**

- 使用Python 3.13.1，符合兼容性要求（≥3.9）
- 通过创建虚拟环境和升级pip增加了不必要的复杂性
- 定位并安装了本例中不需要的测试依赖项

**Installamatic Agent失败（退出码100）的原因：**

- 使用Python 3.18，违反了包要求的≥3.9版本
- 通过apt而非pip安装依赖项，导致非零退出码

本例说明了Python项目自动化环境配置中的常见挑战。即使使用现代工具（pyproject.toml）构建了相对标准的项目结构，仍存在多个故障点，涉及版本兼容性、依赖项解析和安装方法的选择。缺乏跨Python项目的标准化安装流程，加上依赖项管理方法的多样性，使得自动化配置成为一项需要仔细考虑项目特定需求和配置的复杂任务。

#### C.2 成本

**表4**展示了实验的令牌使用量和成本统计，基于2025年2月6日的OpenAI API价格。分析这些统计数据揭示了两个关键模式。

**Agent与Zero-shot的令牌使用量对比。** 基于Agent的方法消耗的令牌比Zero-shot LLM方法多5-10倍，这是因为前者需要推理环境状态并处理命令输出。

**语言特定差异。** JVM项目在所有方法中所需的令牌都显著多于Python项目。这种差异主要源于JVM环境产生的更冗长的构建和依赖项解析日志。

尽管令牌消耗存在这些差异，总体成本仍然合理——即使是令牌消耗最高的基于Agent的方法，每个仓库的平均成本也仅为0.25美元。

|  | Python |  |  |  | JVM |  |  |  |
|---|---|---|---|---|---|---|---|---|
| 基线 | 模型 | 平均令牌数 | 总令牌数 | 成本 | 平均令牌数 | 总令牌数 | 成本 |
| Zero-shot LLM | GPT-4o | 15.6k | 10.1M | $0.042 | 11.0k | 3.6M | $0.030 |
| | GPT-4o-mini | 15.5k | 10.2M | $0.002 | 10.8k | 3.6M | $0.002 |
| Bash Agent | GPT-4o | 77k | 51M | $0.20 | 59k | 18M | $0.15 |
| | GPT-4o-mini | 135k | 90M | $0.02 | 97k | 32M | $0.01 |
| Installamatic Agent | GPT-4o | 98k | 65M | $0.25 | 57k | 19M | $0.15 |
| | GPT-4o-mini | 56k | 37M | $0.01 | 37k | 12M | $0.01 |

**表4：使用统计。** Avg.表示每个仓库的平均数量。

#### C.3 退出码

我们报告...

表5展示了所考虑基线方法在环境配置脚本退出码方面的结果。在此，我们还纳入了第3.3节中描述的简单确定性脚本的结果以供比较。我们观察到，脚本以非零退出码提前终止运行对于所考虑的基线方法来说是一个相对常见的问题。对于JVM平台，确定性脚本仅在1.35%的代码仓库中失败，而基于大语言模型的脚本则表现出更高的失败率，范围从17.74%（使用GPT-4o的Bash代理）到81.20%（使用GPT-4o的零样本大语言模型）不等。对于Python平台，确定性脚本在28.57%的代码仓库中失败，但使用GPT-4o的Bash代理将这一比例降低至5.78%，优于确定性脚本22.79个百分点。

我们对生成的環境配置脚本进行了定性分析，以确定可能的根本原因。对于Python平台，失败的原因有时是缺少系统依赖项或Python版本不匹配（例如，如果项目依赖的Python版本比系统中使用的版本更旧或相反）。类似地，Java版本不匹配是JVM环境配置脚本产生非零退出码的常见原因。对于这两种语言，零样本大语言模型和Installamatic代理生成的脚本最常见的失败原因是使用了系统中不可用的工具。与这两种方法相比，Bash代理能够立即获得错误反馈，并可以选择安装所需工具或考虑使用其他工具。然而，我们仍然观察到Bash代理有时无法恢复，会持续重复尝试相同的失败操作。

**C.4 Shell脚本分析**

| 基线 | 模型 | 平均行数 | 平均执行时间 |
|------|------|----------|--------------|
|      |      | JVM | Python | JVM | Python |
| 零样本大语言模型 | GPT-4o | 53.36 | 56.78 | 176.22 | 302.35 |
|      | GPT-4o-mini | 33.59 | 40.46 | 221.09 | 294.32 |
| Installamatic代理 | GPT-4o | 26.83 | 29.08 | 97.22 | 270.95 |
|      | GPT-4o-mini | 33.71 | 22.13 | 221.08 | 225.94 |
| Bash代理 | GPT-4o | 13.09 | 9.64 | 200.55 | 180.11 |
|      | GPT-4o-mini | 18.30 | 14.64 | 242.84 | 203.35 |

表6：环境配置基线方法生成的環境配置shell脚本的统计数据。平均执行时间以秒为单位报告。

在表6中，我们提供了关于所考虑方法生成的环境配置脚本的额外统计数据。表现最佳的Bash代理在所考虑的基线方法中生成的脚本最短。此外，对于使用GPT-4o的Bash代理，我们提供了该代理在我们的数据集中所有代码仓库（Python环境如图3所示，JVM环境如图4所示）中最频繁执行的Bash命令列表。对于这两种语言，代理都积极使用文件系统探索功能。

## 命令执行与脚本分析

不同类型的代理使用不同的命令：Python代理经常使用pyenv、pip、python和poetry等命令，而JVM代理则运行sdk、./gradlew和maven命令。

![图3：GPT-4o在Python数据集上运行的Bash代理最常执行的Bash命令]

![图4：GPT-4o在JVM数据集上运行的Bash代理最常执行的Bash命令]

## 专家编写的脚本

我们手动调查了所提出的环境配置指标在Python上的鲁棒性。具体而言，两名具有专业Python软件开发经验的作者为从Python样本中随机选择的30个仓库编写了精选脚本，并使用这些脚本运行了评估套件（第3.2节）。结果如表7所示，每个仓库的详细结果见表8。对于所有被考虑的仓库，专家脚本均以零退出码完成，并实现了66.7%的pass@1和9.8的平均错误数（avgErrs），优于所有被考虑的环境配置基线方法。最后，我们采用重抽样法（10,000次迭代）来缓解手动处理样本量较小的问题，并在图5中分享了专家编写脚本和GPT-4o-mini的Bash代理脚本的avgErrs分布直方图，这进一步证实了手动精选脚本与自动环境配置方法生成的脚本之间存在显著差距。

导致设置成功的问题包括：存在过时的依赖项（例如Python 3.x代码库中的遗留Python 2.x模块）以及无法通过静态类型检查器正确处理动态解析的导入。

![表7：环境配置基线、确定性脚本以及30个随机采样的Python仓库的专家编写环境配置脚本的结果]

![图5：avgErrs的直方图——每个仓库的平均缺失错误数——通过重抽样法（10,000次迭代）获得]

![表8：各仓库的详细结果]

表8：针对专家生成的脚本和环境设置基线，对30个随机抽取的Python仓库中缺失导入数量的统计。



---

## 论文 2

# Multi-Docker-Eval: A `Shovel of the Gold Rush' Benchmark on Automatic Environment Building for Software Engineering

**作者**: Kelin Fu, Tianyu Liu, Zeyu Shang, Yingwei Ma, Jian Yang

**arXiv**: https://arxiv.org/abs/2512.06915

---

Multi-Docker-Eval：软件工程自动化环境构建的“淘金铲”基准测试

**摘要**

自动化环境配置是扩大软件工程（SWE）自动化规模的关键瓶颈。为给这一任务提供可靠的评估标准，我们提出了Multi-Docker-Eval基准测试。该基准包含40个真实世界仓库，涵盖9种编程语言，既衡量可执行状态达成的成功性，也衡量现实约束下的效率。我们对当前最先进的LLM和智能体框架进行了广泛评估，得出以下关键洞察：（1）当前模型的整体成功率较低（F2P最高仅为37.7%），环境构建是主要瓶颈；（2）模型规模和推理长度并非决定性因素，DeepSeek-V3.1和Kimi-K2等开源模型在效率和效果上均具有竞争力；（3）智能体框架和编程语言也对成功率有显著影响。这些发现为构建可扩展的、全自动化SWE流水线提供了可操作的指导方针。

**1 引言**

GitHub等平台上的软件仓库支撑着现代软件工程，实现大规模协作和开源开发（Jimenez et al., 2023; Fan et al., 2023）。在这一空间中，仓库级任务——如错误修复、优化和添加新功能——尤为重要。解决这些任务需要对代码库、依赖项和构建环境有深入理解。近年来，大语言模型（LLM）在处理此类问题方面展现出良好能力，推动了自动问题解决和仓库级代码生成的前沿发展。

一个典型的仓库级问题可以形式化为：给定一个仓库R和一个测试函数T(·)，目标是生成一个补丁P，使得T(R)最初失败，而在应用P后T(R⊕P)通过。成功的关键在于构建能够准确反映补丁前后仓库状态的可执行环境。然而，由于依赖项多样、语言版本差异和构建配置差异，构建此类环境仍然具有挑战性。先前的工作如SWE-Gym（Pan et al., 2024）和swe-rebench（Badertdinard et al., 2025）采用手动或基于规则的方式进行设置，而SWE-Smith（Yang et al., 2025）和R2E-Gym（Jain et al., 2025）则使用合成数据，但扩展环境配置仍然困难。最近的智能体系统（如SWEFactory（Guo et al., 2025）、RepoLaunch（Zhang et al., 2025））通过自动化基于Docker的环境设置来解决这一问题，从而实现了可扩展的持续数据生成用于训练和评估。

我们将这一过程比作现代“淘金热”：仓库是金矿，自动修复是黄金，环境配置智能体是淘金铲。可靠的“铲子”对于高效开采至关重要。为了评估这些工具，我们引入了Multi-Docker-Eval，一个用于评估跨语言生态系统的自动化环境配置的基准测试。现有的基准测试如EnvBench（Eliseeva et al., 2025）和INSTALLAMATIC-bench（Milliken et al., 2025）存在局限性：它们通常仅衡量设置成功与否，而未验证...

• 框架比较：对SWE-Builder和RepoLaunch进行分析，总结其优势与劣势，为框架设计提供参考。
• 迈向全自动化：关于软件工程基准测试和数据集构建中可扩展、经济高效且全自动化流水线的设计洞察。

大多数研究仅关注测试有效性或任务特定正确性。这些方法大多针对特定编程语言（如Python或JVM），且采用单一的评估指标，忽视了配置时间、资源消耗以及测试鲁棒性等因素——而这些恰恰是实际应用中的关键考量。为解决上述问题，我们提出了MultiDocker-Eval，一个多语言、多维度的自动环境构建评估框架。该框架不仅评估达成可执行状态的成功率，还考察在真实资源约束下配置过程的效率和稳定性。我们将MultiDocker-Eval定位为基准测试的“铲土试验”——这是推动下一代数据驱动软件智能走向实用化和规范化的重要一步。我们的贡献包括：

- **MultiDocker-Eval基准测试**：首个多语言基准测试，用于评估大语言模型智能体在真实代码仓库环境中配置可执行环境及测试脚本的能力。
- **全面的模型评估**：针对开源和闭源大语言模型开展大规模实验，评估其能力与效率，为未来自动化数据生成提供指导。

**2 相关工作**

**仓库级编码任务。** 近期基准测试评估大语言模型解决真实GitHub问题的能力。SWE-bench（Jimenez等，2023）是目前使用最广泛的基准测试，提供了Python问题及其对应的测试套件。R2E-Gym（Jain等，2025）和SWESmith（Yang等，2025）等工作扩展了任务范围，采用大语言模型合成数据；swebench-multilingual（Yang等，2025）和Multi-SWEbench（Zan等，2025）将评估扩展到多语言环境。除缺陷修复外，近期基准测试还评估更广泛的能力：nocodebench（Deng等，2025a）针对功能添加任务，GSO-bench（Shetty等，2025）聚焦代码优化，SWTbench（Mündler等，2024）评估单元测试生成，而长期任务如代码库生成在Commit0（Zhao等，2024）和SWE-bench Pro（Deng等，2025b）中得到探索。这些工作共同推动了使用大语言模型实现自动化问题解决和仓库级代码生成的进程（Xia等，2024；Yang等，2024；Ma等，2025b,a）。

**用于自动化仓库设置的智能体框架。** 为自动化复杂的设置过程，多种智能体框架应运而生。ExecutionAgent（Bouzenia和Pradel，2025）生成用于构建和测试项目的脚本。RepoLaunch（Zhang等，2025）和SetUpAgent（Vergopoulos等，2025）采用bash交互式智能体进行环境配置。进一步地，SWE-Factory（Guo等，2025）引入了一个多智能体协作框架，实现了完全自动化的多语言环境配置。

**环境配置基准测试。** 评估自动化配置的研究包括EXECUTIONAGENTbench（Bouzenia和Pradel，2025），该基准测试评估了10个代码仓库（涵盖5种语言）的构建和测试成功率，但需要人工评分。INSTALLAMATIC-bench（Milliken等，2025）专注于Python语言，EnvBench（Eliseeva等，2025）扩展到JVM语言。然而，这些基准测试未评估针对特定仓库问题的配置能力，也未能有效测试智能体生成相应测试脚本的能力。

---

**图1：MultiDocker-Eval工作流程概述**

（原文图注已提供足够信息，故保留英文标记）

**数据集构建**。如图2a和图2d所示，我们从GitHub上9种流行编程语言的40个开源仓库中收集了334个issue（具体仓库信息见附录B.1）。这些语言涵盖了主要编程范式，且在谷歌搜索量排名前20（PYPL指数，2025年）。

**筛选**。为确保质量并避免过于热门的仓库，我们选择满足以下条件的仓库：（i）1000 ≤ star数 ≤ 1500（ii）fork数 ≥ 20（iii）贡献者 ≥ 10（iv）仓库大小 ≤ 100 MB。对于每个仓库，我们最多选取8个于2025年7月31日之后（UTC时间）创建的pull request。

**难度验证**。对于每个仓库版本，我们验证是否能够轻松配置可运行环境（例如通过pip install -r requirements.txt）。能够通过此方式配置的被标记为“简单”；其余的为“困难”。在我们的数据集中，20.06%（67个实例）为“简单”。Multi-Docker-Eval的具体难度分布如图2b和图2c所示。具体命令见附录B.2。

### Multi-Docker-Eval基准测试

本章我们介绍Multi-Docker-Eval，这是一个为自动可执行环境配置而设计的基准测试。

#### 3.1 数据收集与筛选

**任务定义**。如图1所示，Multi-Docker-Eval基准测试评估模型自动化为仓库级代码任务创建和测试环境的能力。过程从输入三元组⟨R, D, P*⟩开始：源代码仓库R、自然语言问题描述D、以及正确的解决方案补丁P*。

#### 3.3 指标

我们在Multi-Docker-Eval中引入两类指标：结果指标和过程指标。

结果指标评估环境和测试的正确性：

- **失败转通过率（Fail-to-Pass, F2P）**：配置前失败但配置后通过的测试比例。这是主要指标。
- **提交率（Commit rate）**：智能体提交其输出用于评估的比例。

过程指标评估效率和资源使用：

- **Token消耗**：配置过程中LLM使用的总token数。
- **实际耗时（Wall time）**：从配置开始到完成的总实际时间。
- **CPU时间**：使用的总CPU时间。
- **最大内存占用（Max RSS）**：峰值内存使用量。
- **平均镜像大小**：最终Docker镜像的平均大小，表示存储效率。

**表1**略

#### 4 实验设置

**基线方法**。我们评估两种范式：
- **RepoLaunch（单智能体）**：一个智能体顺序选择基础镜像并在容器内发出原始bash命令；无自动重试或复用。

前者以灵活性换取稳定性；后者提供开放的命令空间但方差较高。

**LLM模型**。7个开源模型（DeepSeek-v3.1、DeepSeek-R1、Qwen3-235B-A22B、GPT-OSS-20/120B、Kimi-K2-0905、Kimi-K2-thinking）和3个闭源模型（Claude-Sonnet-4、GPT-5-Mini、Gemini-2.5-Flash）。

### 5 结果

#### 5.1 RQ1：当前最先进的LLM在配置任务上的效率

首先，我们评估当前LLM在自动环境配置任务上的表现——即它们将仓库转换为具有有效测试脚本的可执行状态的能力（RQ1）。

**总体表现**。如表1所示，主要指标失败转通过率（F2P）在不同模型间从17%到38%不等，表明不到一半的仓库能够成功配置。

## 实验设置

**Agent框架**。我们评估了两种用于自动化环境配置的Agent框架。详细介绍见附录A：

- **SWE-Builder（多Agent）**：四个专业Agent协同搜索代码库、编写Dockerfile、安装依赖并生成测试；内置循环在测试失败时重新调用Agent，记忆池复用先前验证过的构建方案。

## 结果指标

| 模型 | F2P (%) | 提交率 (%) |
|------|---------|------------|
| DeepSeek-v3.1 | 37.72 | 52.89 |
| DeepSeek-R1 | 26.65 | 41.72 |
| GPT-OSS-20B | 17.17 | 29.44 |
| GPT-OSS-120B | 27.00 | 37.72 |
| Kimi-K2-0905 | 37.62 | 55.49 |
| Kimi-K2-thinking | 36.53 | 52.69 |
| Qwen3-235B-A22B | 23.65 | 34.53 |
| Claude-Sonnet-4 | 35.53 | 47.41 |
| GPT-5-Mini | 34.13 | 49.60 |
| Gemini-2.5-Flash | 29.44 | 40.62 |

| 过程指标 | 平均输入tokens (K) | 平均输出tokens (K) | 挂钟时间 (Ks) | CPU时间 (Ks) | 最大内存 (GB) | 平均Docker大小 (GB) |
|----------|-------------------|-------------------|--------------|--------------|--------------|-------------------|
| 开源模型 | | | | | | |
| DeepSeek-v3.1 | 158.11 | 17.15 | 138.05 | 60.10 | 7.45 | 1.02 |
| DeepSeek-R1 | 184.23 | 58.37 | 128.31 | 30.17 | 7.47 | 1.02 |
| GPT-OSS-20B | 113.02 | 7.92 | 162.12 | 59.40 | 7.38 | 0.87 |
| GPT-OSS-120B | 101.46 | 38.87 | 122.80 | 143.37 | 8.70 | 0.83 |
| Kimi-K2-0905 | 127.91 | 104.29 | 101.81 | 114.61 | 7.60 | 1.01 |
| Kimi-K2-thinking | 138.64 | 0.613 | 7.66 | 7.66 | 7.66 | 1.05 |
| Qwen3-235B-A22B | 0.749 | 0.534 | 0.626 | 0.478 | 7.38 | 1.00 |
| 闭源模型 | | | | | | |
| Claude-Sonnet-4 | 182.85 | 15.01 | 339.94 | 103.32 | 7.30 | 1.17 |
| GPT-5-Mini | 153.43 | 32.60 | 110.54 | 158.61 | 7.34 | 0.95 |
| Gemini-2.5-Flash | 88.00 | 0.698 | 7.58 | 7.58 | 7.58 | 0.97 |

表1：不同模型在SWE-Builder上的Multi-Docker-Eval整体表现。

### 自我评估可靠性

提交率（Commit Rate）反映Agent的自信程度，但往往与实际表现不一致。Claude-Sonnet-4在47.41%的运行中提交，但F2P仅为35.53%；Qwen3-235B-A22B同样表现出过度自信。这表明当前LLM Agent在复杂软件任务中缺乏可靠的自评能力。

### 资源与效率模式

所有模型都需要大量挂钟时间（每次运行约24-44小时），凸显了该任务的工程复杂性。相比之下，CPU时间、最大内存和Docker大小保持稳定——通常为600-750 CPU秒、7.4-7.7 GB内存和0.9-1.2 GB镜像大小——表明SWE-Builder框架提供了可预测、资源受限的执行环境。这种稳定性便于大规模部署的容量规划。

总之，环境配置表现与模型规模或推理长度无直接相关性。DeepSeek-v3.1和Kimi-K2-0905等开源模型的表现相当或超过规模更大的闭源模型。这一性能差距也表明该能力尚未在预训练或指令微调数据中得到广泛覆盖。

### 模型比较

开源模型DeepSeek-v3.1（37.72%）取得最高F2P，超越Claude-Sonnet-4（35.53%）和GPT-5-Mini（34.13%）。就效率而言，如图3所示，Kimi-K2-0905以显著更少的tokens（~120K）和更短的挂钟时间（114.61秒）达到37.62% F2P，而表现更好的模型往往消耗更多资源。例如，GPT-5-Mini消耗~443.26K tokens以获得34.13%的F2P。

## 5.2

[此处图表数据已在上文表格中呈现]

# RQ2：智能体框架架构的影响

**首次通过率（%）** | **提交率（%）**

54.5% | 50

41.4% | 40.9%
40 | 37.0%
30 | 27.6%
20 | 27.2%
10 | 15.9%
0 | 11.4% | 10.8%
GO | JS | RUBY | PY | PHP | C | RUST | CPP | JAVA

**编程语言**

**图4：** SWE-Builder与RepoLaunch框架在不同模型上的首次通过率差异

**图5：** 柱状图展示了不同编程语言下的首次通过率，折线图则显示了提交率（框架：SWE-Builder）。

提高配置质量还需要选择合适的智能体架构。因此，我们接下来分析不同的智能体框架架构如何影响环境配置的性能（RQ2）。我们对两种代表性框架——SWE-Builder和RepoLaunch（已在第4节介绍）进行了比较。表2中10个模型的结果清晰显示了成功率、交互效率和资源使用模式的差异。更多关于RepoLaunch实验的详细信息见附录D.2。

**成功率。**如图4所示，RepoLaunch在所有模型上的首次通过率均显著低于SWE-Builder。这一差距源于架构差异：SWE-Builder使用四个专业智能体协同进行探索、设置和测试，并具备迭代错误修复的重新激活能力。相比之下，RepoLaunch依赖单智能体顺序推理，反馈或纠正能力有限。此外，RepoLaunch的提交率（22.35%）明显高于其首次通过率（8.85%）。这是由于其内部评判机制较弱：该框架通常将实例标记为完成，即使生成的设置或测试命令需要人工检查和重写才能使用。这突出了其原始工作流程中缺乏可靠的自动化验证。

**运行时和资源模式的差异。**过程指标显示了进一步的对比：
- **Token消耗：** RepoLaunch使用了3.5倍更多的输入Token（504.05K vs. 165.95K），原因是累积的bash历史记录，但其输出Token减少了4.2倍（10.03K vs. 42.15K），反映了反应式命令执行而非结构化规划。
- **依赖效率：** RepoLaunch生成的Docker镜像大41%（1.41 GB vs. 1.01 GB），表明依赖解析优化不足。
- **计算特征：** RepoLaunch的墙钟时间快3.9倍（31.72Ks vs. 122.80Ks），但CPU时间多10.08倍（6.25Ks vs. 0.62Ks）且方差较大（±13.78Ks），表明计算密集但不稳定。

**资源方差对比。** RepoLaunch在Token和CPU指标上表现出高方差，反映了使用低级bash命令推理时的不稳定性。相比之下，SWE-Builder使用声明式操作（如仓库检查、配置生成），导致更可预测和可复现的资源使用。

总之，框架架构严重影响自动化可靠性。SWE-Builder中的多智能体协作与修复循环支持更高的成功率，而单智能体顺序设计则会放大错误而无法自纠正。更为有效的...

研究结果明确了改进配置系统的方向。拥有统一依赖管理和测试工作流程的编程语言与当前大语言模型能力契合良好。相比之下，C/C++、Java、Rust 和 PHP 等生态系统在处理系统依赖、编译器配置和碎片化测试规范方面存在结构性弱点，这些应成为未来智能体优化的优先方向。

**RQ3：编程语言配置成功的差异性**

编程语言生态系统是决定自动化环境配置难度的主要因素。因此，我们研究了不同编程语言在配置难度和模型成功率方面的差异（RQ3）。如图 5 和附录 D.3 所示，可以观察到以下几个总体趋势：

采用标准化声明式构建系统的语言（如 Go、Python 和 JavaScript）取得了最佳结果。Go 表现最优（提交率 64.25%，首次通过率 54.50%），这归功于其可复现的模块系统和极少的系统级依赖。Python 和 JavaScript 同样表现出色，得益于成熟的包管理工具（pip、npm）以及为大型语言模型提供正确配置指导的既定规范。相比之下，C/C++、Java 和 Rust 的失败率较高，因为它们的构建通常依赖于系统库、编译器工具链以及版本相关的配置，这些难以推断。

**RQ4：瓶颈分析**

为理解当前系统的根本限制，我们研究了在配置任务中环境构建还是测试脚本生成是主要瓶颈（RQ4）。图 6 揭示了不同模型间失败模式的清晰层次结构。

**GPT-OSS-20B**  
**Qwen3-235B-A22B**  
**DeepSeek-R1**  
**GPT-OSS-120B**  
**Gemini-2.5-Flash**  
**GPT-5-Mini**  
**Claude-Sonnet-4**  
**Kimi-K2-thinking**  
**Kimi-K2-0905**  
**DeepSeek-v3.1**

| 框架 | 指标 | 简单（%） | 困难（%） | 困难/简单 |
|------|------|-----------|-----------|-----------|
| SWE-Builder | 提交率 | 59.35 | 42.55 | 0.72 |
|      | 解决率 | 39.10 | 28.40 | 0.73 |
| RepoLaunch | 提交率 | 47.63 | 16.00 | 0.34 |
|      | 解决率 | 21.71 | 5.31 | 0.24 |

表 3："简单"与"困难"子集上的性能对比

这些模式在不同框架间保持一致，使用"简单"和"困难"数据集划分（第 3.2 节）。如表 3 所示，两个框架在"简单"案例上都取得了更高的成功率，但仍未达到完美——这证实了即使依赖关系简单，智能体在脚本生成方面仍经常遇到困难。在"困难"条件下，RepoLaunch 表现出比 SWE-Builder 更明显的性能下降，突显了对依赖复杂性的不同应对能力。

这些发现共同表明，环境构建（尤其是依赖解析）仍是最不稳定的组件——使其成为未来基于智能体的配置系统的关键目标。

100

图 6：各模型失败模式细分（框架：SWE-Builder）。每个条形图总和为 100%。从左至右：（i）Docker 构建失败，（ii）测试脚本生成失败，（iii）无法复现报告的问题（即脚本在未打补丁的代码上错误地通过），以及（iv）由于 Docker 环境中缺少依赖而无法运行的测试脚本。前两类失败的智能体不会提交答案，而后两类失败发生在提交之后。

失败模式

结果显示，Docker构建错误占据主导地位（平均36.09%），表明环境构建是失败率最高的步骤。脚本相关问题——缺失或不可执行脚本（18.12%）以及静默假通过（12.70%）——构成第二大故障类别。一旦Docker镜像成功构建，下游评估失败的情况便很少见（2.63%），这凸显了SWE-Builder自检机制的鲁棒性。

模型级分析进一步强化了这一模式：更强的模型能够减少脚本错误，但在Docker错误方面几乎没有改善，这表明大型语言模型缺乏系统性依赖推理能力。“思维增强”型模型（如DeepSeek-R1、GPT-5-Mini、Kimi-K2-thinking）正是典型例证——它们生成的脚本质量更高，但Docker构建失败却更多，表明思维链推理虽有助于代码合成，却无法解决系统级依赖问题。

我们进一步探讨这些瓶颈是否可追溯至特定代码库特征、模型规模或训练方法，这或许能为未来改进提供参考。

结论

在本研究中，我们推出了Multi-Docker-Eval，这是首个多语言基准测试，用于共同评估大型语言模型智能体为真实代码库生成可运行Docker环境和有效测试脚本的能力。通过在多样化的模型和框架上开展实验，我们揭示了当前方法在效率、鲁棒性和可扩展性方面的关键见解，突出了框架架构和生态系统标准化在决定成功中的重要性，强调了需要记忆增强、反馈驱动和语言感知的设计。我们希望Multi-Docker-Eval能够作为在数据驱动软件工程时代推进全自动化、资源高效流水线的基石。

参考文献

gym：用于扩展开源权重SWE智能体的程序化环境和混合验证器。arXiv预印本 arXiv:2504.07164。

Ibragim Badertdinov、Alexander Golubev、Maksim Nekrashevich、Anton Shevtsov、Simon Karasik、Andrei Andriushchenko、Maria Trofimova、Daria Litvintseva、Boris Yangel。2025。SWE-REBench：软件工程智能体任务收集与去污染评估的自动化流水线。arXiv预印本 arXiv:2505.20411。

Carlos E Jimenez、John Yang、Alexander Wettig、Shunyu Yao、Kexin Pei、Ofir Press、Karthik Narasimhan。2023。SWE-Bench：语言模型能否解决真实世界的GitHub问题？arXiv预印本 arXiv:2310.06770。

Yingwei Ma、Rongyu Cao、Yongchang Cao、Yue Zhang、Jue Chen、Yibo Liu、Yuchen Liu、Binhua Li、Fei Huang、Yongbin Li。2025a。SWE-GPT：以流程为中心的自动化软件改进语言模型。ACM软件工程汇刊，2(ISSTA):2362-2383。

Islem Bouzenia、Michael Pradel。2025。你命名，我运行：用于执行任意项目测试的大型语言模型智能体。ACM软件工程汇刊，2(ISSTA):1054-1076。

Le Deng、Zhonghao Jiang、Jialun Cao、Michael Pradel、Zhongxin Liu。2025a。NoCodeBench：评估自然语言驱动功能添加的基准。arXiv预印本 arXiv:2507.18130。

Yingwei Ma、Qingping Yang、Rongyu Cao、Binhua Li、Fei Huang、Yongbin Li。2025b。阿里云Lingma智能体：通过全面代码库探索提升自动化问题解决能力。第33届ACM软件工程基础国际会议论文集，238-249页。

Xiang Deng、Jeff Da、Edwin Pan、Yannis Yiming He、Charles Ide、Kanak Garg、Niklas Lauffer、Andrew Park、Nitin Pasari、Chetan Rane等。2025b。SWE-Bench Pro：人工智能智能体能否解决长期软件工程任务？arXiv预印本 arXiv:2509.16941。

Louis Milliken、Sungmin Kang、Shin Yoo。2025。超越pip install：评估大型语言模型智能体自动化安装Python项目的能力。2025年IEEE软件分析、演化与再工程国际会议(SANER)，1-11页。IEEE。

Aleksandra Eliseeva、Alexander Kovrigin、Ilia Kholkin、Egor Bogomolov、Yaroslav Zharov。2025。EnvBench：自动化环境设置的基准。arXiv预印本 arXiv:2503.14443。

Niels Mündler、Mark。

以下是中文翻译：

---

Müller, Jingxuan He, 和 Martin Vechev。2024。Swt-bench：使用代码代理测试和验证真实世界缺陷修复。《神经信息处理系统进展》，37：81857–81887。

Angela Fan, Beliz Gokkaya, Mark Harman, Mitya Lyubarskiy, Shubho Sengupta, Shin Yoo, 和 Jie M Zhang。2023。用于软件工程的大型语言模型：综述与开放问题。收录于2023年IEEE/ACM软件工程国际会议：软件工程未来研讨会（ICSE-FoSE），第31–53页。IEEE。

Jiayi Pan, Xingyao Wang, Graham Neubig, Navdeep Jaitly, Heng Ji, Alane Suhr, 和 Yizhe Zhang。2024。使用swe-gym训练软件工程代理和验证器。《arXiv预印本》，arXiv:2412.21139。

Lianghong Guo, Yanlin Wang, Caihua Li, Pengyu Yang, Jiachi Chen, Wei Tao, Yingtian Zou, Duyu Tang, 和 Zibin Zheng。2025。Swe-factory：您的缺陷解决训练数据和评估基准自动化工厂。《arXiv预印本》，arXiv:2506.10954。

PYPL索引。2025。PYPL编程语言流行度。

Manish Shetty, Naman Jain, Jinjian Liu, Vijay Kethanaboyina, Koushik Sen, 和 Ion Stoica。2025。Gso：用于评估SWE代理的挑战性软件优化任务。《arXiv预印本》，arXiv:2505.23671。

Naman Jain, Jaskirat Singh, Manish Shetty, Liang Zheng, Koushik Sen, 和 Ion Stoica。2025。R2e-

9

---

Konstantinos Vergopoulos, Mark Niklas Müller, 和 Martin Vechev。2025。仓库级编码任务的自动化基准生成。《arXiv预印本》，arXiv:2503.07701。

Chunqiu Steven Xia, Yinlin Deng, Soren Dunn, 和 Lingming Zhang。2024。Agentless：揭示基于LLM的软件工程代理的神秘面纱。《arXiv预印本》，arXiv:2407.01489。

John Yang, Carlos E Jimenez, Alexander Wettig, Kilian Lieret, Shunyu Yao, Karthik Narasimhan, 和 Ofir Press。2024。Swe-agent：代理-计算机接口实现自动化软件工程。《神经信息处理系统进展》，37：50528–50652。

John Yang, Kilian Lieret, Carlos E Jimenez, Alexander Wettig, Kabir Khandpur, Yanzhe Zhang, Binyuan Hui, Ofir Press, Ludwig Schmidt, 和 Diyi Yang。2025。Swe-smith：软件工程代理的数据规模化。《arXiv预印本》，arXiv:2504.21798。

Daoguang Zan, Zhirong Huang, Wei Liu, Hanwu Chen, Linhao Zhang, Shulin Xin, Lu Chen, Qi Liu, Xiaojian Zhong, Aoyan Li, 等一人。2025。Multi-swe-bench：缺陷解决的多语言基准。《arXiv预印本》，arXiv:2504.02605。

Linghao Zhang, Shilin He, Chaoyun Zhang, Yu Kang, Bowen Li, Chengxing Xie, Junhao Wang, Maoquan Wang, Yufan Huang, Shengyu Fu, 等一人。2025。Swe-bench上线了！《arXiv预印本》，arXiv:2505.23419。

Wenting Zhao, Nan Jiang, Celine Lee, Justin T Chiu, Claire Cardie, Matthias Gallé, 和 Alexander M Rush。2024。Commit0：从零开始生成库。《arXiv预印本》，arXiv:2412.01769。

10

---

A

SWE-Builder和RepoLaunch的示意图

**（a）SWE-Builder框架概述**

| 输入 | 处理 | 输出 |
|------|------|------|
| 缺陷信息 | 仓库探索器 | Dockerfile |
| 原始仓库 | 分析器 | 测试脚本 |
| | 收集器 | |
| | 记忆池 | |
| | 环境管理器 | |
| | 测试分析师 | |
| | 测试管理器 | |
| | 判定器 | |

- 仓库信息（仓库名、版本等）
- 构建Docker容器
- 运行测试并获取输出
- 判定并计划修复
- 修复Dockerfile
- 收集更多信息
- 修复测试脚本

**（b）RepoLaunch框架概述**

| 输入 | 处理 | 输出 |
|------|------|------|
| 缺陷信息 | 查找相关文件 | Dockerfile |
| 原始仓库 | 选择基础镜像 | 执行命令及相应输出 |
| README.md | 分析器 | 测试脚本 |
| requirements.txt | 设置 | |
| ... | 验证 | |
| | 判定器 | |

- Python: 3.11
- Go: 1.22
- 测试命令、安装、构建等

图7：Multi-Docker-Eval数据构成概览。下图展示了SWE-Builder和RepoLaunch的系统框架图。两个系统均以源代码仓库和缺陷信息作为输入，并生成Dockerfile和测试脚本作为输出。这些输出共同为该仓库提供了一个可执行环境，并能够对给定缺陷进行相应测试。

（Guo等人，2025）（图7a）：一个多智能体迭代框架，将环境配置划分为四个专门角色：仓库探索器、环境管理器、测试管理器和测试分析器。这些智能体协作收集依赖项、生成Dockerfile和测试脚本、分析失败原因并迭代优化配置。值得注意的是，如果最终的测试分析器阶段结果不理想，系统会重新激活其他三个智能体进行错误修正，形成闭环迭代工作流。为进一步提高效率，SWE-Builder还引入了评估环境内存池，可重用来自相似仓库版本的先前验证配置。这种多智能体、记忆增强的设计使其能够在大规模多样化的语言和仓库中保持稳健性能，使其因其高可靠性和可扩展性成为我们主要的实验框架。

• RepoLaunch（Zhang等人，2025）（图7b）：一种单智能体顺序工作流。它扫描仓库结构，识别配置文件，选择基础镜像，然后启动Docker容器执行迭代bash命令以完成依赖安装和测试执行。虽然设置和验证阶段之间存在本地循环，但整体过程按阶段顺序推进。在我们的扩展版本中，我们将两个原本手动步骤自动化：使智能体能够审查执行历史以提取最小化安装和测试命令，并使用特定语言的日志解析器执行自动化金标准补丁验证。RepoLaunch在广阔的bash命令动作空间中运行，需要与Docker容器进行动态交互，这带来了更高的不确定性。

B 基准测试详情

B.1 Multi-Docker-Eval中的仓库

Go
Python
• pallets-eco/flask-wtf
• uber-go/atomic
• rigetti/pyquil
• warrensbox/terraform-switcher
• marcelotduarte/cx-Freeze
• polarsignals/frostdb
• getlogbook/logbook
• stephenafamo/bob
• pytest-dev/pytest-django
• Altinity/clickhouse-backup

Java
• HandmadeMath/HandmadeMath
• OpenAEV-Platform/openaev
• libssh2/libssh2
• java-diff-utils/java-diff-utils
• nginx/njs
• kagkarlsson/db-scheduler
• profanity-im/profanity
• dadoonet/fscrawler

C++
JavaScript
• cpputest/cpputest
• pinojs/pino-pretty
• GothenburgBitFactory/timewarrior
• prettier/plugin-ruby
• nfrechette/acl
• vercel/nft
• LiteLDev/LeviLamina
• opencomponents/oc
• vimeo/player.js

图8：Multi-Docker-Eval中演化的仓库。

B.2 难度验证

语言
基础镜像
关键配置命令

Python
python:3.10slim
RUN if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
RUN if [ -f pyproject.toml ]; then pip install poetry && poetry install || pip install -e .; fi
RUN if [ -f setup.py ]; then pip install -e . || true; fi
RUN if [ -f Pipfile ]; then pip install pipenv && pipenv install --system; fi

C
gcc:11
RUN if [ -f Makefile ]; then make -j2 || true; else gcc -Wall -O2 $(ls *.c 2>/dev/null) -o main 2>/dev/null || true; fi

C++
gcc:11
RUN if [ -f Makefile ]; then make -j2 || true; else g++ -Wall -O2 $(ls *.cpp 2>/dev/null) -o main 2>/dev/null || true; fi

Go
golang:1.20
RUN go install gotest.tools/gotestsum@latest || true
RUN if [ -f go.mod ]; then go mod tidy; fi
RUN go build -v ./... || true

JAVA
maven:3.9.9 eclipsetemurin-17
RUN if [ -f pom.xml ]; then mvn -DskipTests package -q; fi
RUN if [ -f build.gradle ]; then gradle build -x test || true; fi

JavaScript
node:18bullseye-slim
RUN if [ -f package.json ]; then npm install --silent; fi

PHP
php:8.1-cli
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
&& php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
&& rm composer-setup.php
RUN composer global require "phpunit/phpunit:^9" --prefer-dist --no-progress --no-suggest

Rust
ruby:3.1-slim

# 中文翻译

## 环境配置命令

```
ENV PATH="/root/.composer/vendor/bin:$PATH"
RUN if [ -f composer.json ]; then composer install --nointeraction || true;
RUN if [ -f Cargo.toml ]; fi then cargo build --release || true; fi
install bundler rspec rake minitest test-unit || true
RUN gem
RUN if [ -f Gemfile ]; then bundle install || true; fi
```

表4：用于尝试构建环境的基础镜像及关键配置命令。如果能够通过这些命令成功构建可测试环境，则该实例被定义为"简单"；否则，标记为"困难"。

---

## C 实验细节

### C.1 实验平台配置

所有实验均在虚拟机上执行，该虚拟机配备32核CPU（Intel® Xeon® Platinum 8457C）和128 GB内存。系统运行Linux 5.15.0-130-generic版本，配备1000 GB虚拟磁盘存储。每个实验均在隔离的容器化环境中执行，以确保可重复性并防止并发运行之间的资源干扰。所有评估均未使用GPU加速。

### C.2 实验参数

未明确提及的参数均使用其框架默认值。

**SWE-Builder配置**
- 并发数：每轮并发数分别为5、8和12

**RepoLaunch配置**
- 并发数：所有实验固定为8个并发进程
- 设置代理最大步数：环境设置任务最多允许30步
- 验证代理最大步数：验证任务最多允许30步

**评估配置**
- 最大工作进程数：16个工作进程用于并行评估
- Docker构建超时：Docker镜像构建最多允许1800秒（30分钟）
- 测试超时：测试执行最多允许2700秒（45分钟）
- 测试运行次数（稳定F2P）：重复执行3次测试以衡量测试稳定性并考虑 flaky 测试

---

## D 详细结果

### D.1 F2P得分（%）

[此处为结果图表，详细数据见原文图9和图10]

### D.2 RepoLaunch框架详细结果

| 模型 | 提交率 (%) | F2P (%) | Wall时间 (Ks) | CPU时间 (Ks) | 最大RSS (GB) | 平均docker大小 (GB) | 平均输入 tokens (K) | 平均输出 tokens (K) |
|------|-----------|---------|---------------|--------------|--------------|---------------------|---------------------|---------------------|
| **开源模型** | | | | | | | | |
| DeepSeek-v3.1 | 35.63 | 11.68 | 197.20 | 174.35 | 22.39 | 5.91 | 1.55 |
| DeepSeek-R1 | 21.56 | 9.88 | 202.73 | 1690.38 | 46.33 | 3.78 | 1.39 |
| GPT-OSS-20B | 6.29 | 2.99 | 16.02 | 5.42 | 22.35 | 6.01 | 1.19 |
| GPT-OSS-120B | 18.26 | 6.59 | 137.27 | 434.50 | 26.87 | 6.18 | 1.36 |
| Kimi-K2-0905 | 34.13 | 10.18 | 111.33 | 13.63 | 21.20 | 3.71 | 1.54 |
| Kimi-K2-thinking | 18.26 | 8.68 | - | - | 24.14 | 3.66 | 1.44 |
| Qwen3-235B-A22B | 6.59 | 3.59 | - | - | 26.22 | 7.54 | 1.42 |
| **闭源模型** | | | | | | | | |
| Claude-Sonnet-4 | 31.74 | 9.88 | 860.02 | 694.93 | 52.21 | 7.81 | 1.49 |
| GPT-5-Mini | 23.05 | 10.18 | 859.84 | 859.84 | 39.02 | 4.02 | 1.43 |
| Gemini-2.5-Flash | 22.75 | 9.88 | - | - | 27.66 | 6.98 | 1.437 |

表5：不同模型在RepoLaunch上的整体多Docker评估性能。

### D.2 编程语言F2P率

[此处为结果图表，详细数据见原文]

表6：SWE-Builder上各模型在不同编程语言中的F2P比率（%）

| 编程语言 | DeepSeek-v3.1 | DeepSeek-R1 | GPT-OSS-20B | GPT-OSS-120B | Kimi-K2-0905 | Kimi-K2-thinking | Qwen3-235B-A22B | Claude-Sonnet-4 | GPT-5-Mini | Gemini-2.5-Flash |
|---------|--------------|-------------|-------------|--------------|-------------|-----------------|-----------------|-----------------|-----------|-----------------|
| Python | 47.86 | 32.48 | 15.38 | 35.90 | 47.01 | 49.57 | 21.37 | 41.88 | 43.59 | 35.04 |
| JavaScript | 45.83 | 40.83 | 17.50 | 36.67 | 49.17 | 46.67 | 37.50 | 48.33 | 49.17 | 42.50 |
| Java | 14.29 | 9.52 | 8.57 | 10.48 | 12.38 | 11.43 | 9.52 | 8.57 | 14.29 | 8.57 |
| C++ | 18.89 | 10.00 | 2.22 | 10.00 | 12.22 | 11.11 | 5.56 | 18.89 | 15.56 | 10.00 |
| C | 33.33 | 22.50 | 15.00 | 23.33 | 34.17 | 37.50 | 17.50 | 32.50 | 29.17 | 26.67 |
| Go | 57.50 | 46.67 | 46.67 | 53.33 | 61.67 | 63.33 | 51.67 | 66.67 | 48.33 | 49.17 |
| Ruby | 51.67 | 35.83 | 25.00 | 32.50 | 51.67 | 48.33 | 30.00 | 43.33 | 49.17 | 41.67 |
| Rust | 20.83 | 10.00 | 9.17 | 14.17 | 22.50 | 17.50 | 15.83 | 18.33 | 16.67 | 14.17 |
| PHP | 42.22 | 25.56 | 7.78 | 22.22 | 36.67 | 32.22 | 15.56 | 30.00 | 34.44 | 28.89 |



---

## 论文 3

# SetupBench: Assessing Software Engineering Agents' Ability to Bootstrap Development Environments

**作者**: Avi Arora, Jinu Jang, Roshanak Zilouchian Moghaddam

**arXiv**: https://arxiv.org/abs/2507.09063

---

# SetupBench：评估软件工程智能体引导开发环境的能力

arXiv:2507.09063v1 [cs.SE] 2025年7月11日

Avi Arora∗
微软

Jinu Jang∗
微软

Roshanak Zilouchian Moghaddam
微软

## 摘要

现代大语言模型（LLM）智能体承诺为真实世界的软件任务提供端到端辅助，但现有基准测试几乎仅在预先配置好的环境中评估LLM智能体，所有依赖项均已预装。为填补这一空白，我们推出了SetupBench，这是一个包含93个实例的基准测试，用于专门评估环境引导能力：从空的Linux沙箱开始，智能体必须安装软件包、解决依赖冲突、初始化数据库并配置后台服务。我们的任务涵盖七种语言生态系统、五种数据库引擎以及多服务编排场景，每个任务都配有自然语言问题描述和确定性成功命令。通过对OpenHands这一先进编码智能体的评估，我们发现各任务类别的成功率普遍较低，其中仓库设置（38.9%-57.4%）和本地数据库配置（20.0%-53.3%）面临尤为突出的挑战。我们的分析揭示了系统性失败模式，包括不完整的开发工具安装、虚构的任务约束以及非持久性的环境修改，这些问题会破坏智能体与人类的协作工作流程。我们还发现智能体探索策略存在严重的低效问题，与最优人类行为相比，38%-69%的操作是不必要的。这些发现凸显了当前智能体在实际环境引导能力方面的差距。通过聚焦这一关键但未被充分评估的能力，SetupBench为下一代致力于解决端到端真实世界任务的软件开发智能体提供了严格的衡量标准。

## 1 引言

随着大语言模型（LLMs）在代码生成质量方面的持续提升[Anthropic, 2025]，我们正在见证范式转变：LLMs从充当代码助手[GitHub, 2022]向实现自主开发[GitHub, 2025]演进。在这一新范式中，人类的主要角色从编写代码转变为定义需求、提供指导和验证结果。这一转变的典型例证是面向端到端软件工程任务的LLM智能体现已作为云服务部署，包括OpenAI Codex [OpenAI, 2025]、GitHub Copilot Coding Agent [GitHub, 2025]和Devin [CognitionAI, 2024]。

这些智能体在安全的沙箱中运行代码，配备固定工具链以支持广泛使用的语言、软件包和依赖项，而将许多任务特定的环境设置工作留给智能体本身。环境设置和依赖项管理是一项关键但常被忽视的能力：最近的实证研究一致表明，安装、依赖项解析和构建/配置工作是开发者挫败感的主要来源之一[Mu et al., 2025], [Obi et al., 2024], [Nazário et al., 2025]。尽管其重要性不言而喻，但用于量化智能体能力的基准测试套件并未对环境引导技能进行测试。软件修复数据集（如SWE-Bench [Jimenez et al., 2024]和DevBench [Li et al., 2024a]）或通用智能体评估（如AgentBench [Liu et al., 2024]）均以预构建的Docker镜像形式分发每个任务，所有必需的库、服务和配置文件均已安装。因此，智能体可能在排行榜上表现斐然，却仍无法跨越开发者面临的第一个障碍：“无法运行我的代码”。

SetupBench通过聚焦于评估智能体的项目设置和环境引导能力来弥补这一评估空白。SetupBench是一个包含93个环境引导任务的精选套件，这些任务从空沙箱开始，仅当智能体已安装或重建缺失的系统及语言软件包、初始化数据库、配置后台服务或解决依赖冲突时结束。每个实例提供（i）自然语言问题描述...

语言问题描述、（ii）工作区快照（例如新克隆的仓库），以及（iii）一个确定性的一行验证命令（success_command），如果环境变更生效则打印"Setup successful"，否则打印"Setup failed"。通过对SetupBench上最先进智能体OpenHands的评估，我们发现成功率较低（各模型为34.4%-62.4%），并识别出三种关键失败模式：不完整的开发工具安装、幻觉任务约束，以及破坏智能体-人类协作工作流程的非持久性环境修改。我们还通过与最优人类行为的比较来量化智能体的低效性，发现各模型存在38%-69%的步骤浪费，识别出三个主要低效来源：冗余文件读取、较差的指令遵循，以及检查设置相邻但不包含设置信息的文件的偏离目标探索。我们的发现揭示了可操作的智能体架构改进见解，包括持久性环境状态管理、上下文感知的设置完成策略，以及与人类开发工作流程更契合的效率导向探索机制。我们提供了完整的评估框架，包含确定性验证命令，并发布提示词和脚本以支持方法论的扩展和复现（见附录）。

SetupBench是一个包含93个实例的基准测试，涵盖了开发者在实际工作中面临的四类不同环境引导任务，如表1所示。每个实例提供自然语言问题描述、工作区快照，以及基于环境变更是否生效而打印"Setup successful"或"Setup failed"的确定性验证命令。

类别 | 实例数量 | 生态系统/引擎
---|---|---
仓库设置 | 54 | Py、TS、JS、Go、Rust、Java、C++
依赖解析 | 16 | npm、pip/Poetry、Bundler
数据库设置 | 15 | Postgres、MySQL、SQLite、Redis、MongoDB
后台服务设置 | 8 | Gunicorn、Celery、NGINX、file-watchers、autossh

表1：SetupBench组成。

任务构建

我们的基准测试涵盖了真实开发工作流中遇到的四类环境引导任务：

**仓库设置**：我们选取了跨7种语言（Python、TypeScript、JavaScript、Go、Rust、Java、C++）的热门仓库，这些仓库具有复杂的设置要求。对于每个仓库，我们：（1）通过遵循项目文档手动记录所有设置步骤，（2）使用大语言模型结合从抓取的Markdown文件中获取的仓库上下文生成验证命令，（3）在全新沙盒中验证端到端功能，以确保仅在设置成功时获得"Setup successful"，否则获得"Setup failed"。例如，prometheus/prometheus的验证命令检查服务器是否暴露了指标页面：

```
curl -s http://localhost:9090/metrics | grep -q 'prometheus_build_info' && echo 'Setup successful' || echo 'Setup failed'
```

**依赖解析**：我们从GitHub问题中挖掘了包含解析器错误消息（"code ERESOLVE"、"peer dep conflict"、"could not find compatible versions"）的真实世界依赖冲突。然后仅保留存在锁文件（package-lock.json或Gemfile.lock）的实例。对于每个实例，我们为每个包管理器生态系统定义了一个验证命令，以可靠地显示依赖错误：npm使用`npm ci --ignore-scripts`，Bundler使用`bundle install --jobs=1 --retry=2 --without development test`。随后我们在全新环境中复现这些冲突，并手动解决它们以验证任务可行性。我们仅将经过验证的实例纳入最终基准测试。最终集合包含9个npm和7个Bundler依赖冲突，每个都捕获了真实世界的版本约束破损问题，这些问题由人类开发者报告。这些实例需要智能体读取错误日志、追踪版本约束并更新清单文件，捕获了真实的调试工作流程。

数据库配置：我们精心设计了三级难度，涵盖五种数据库引擎（PostgreSQL、MySQL、SQLite、Redis、MongoDB），以评估智能体是否能安装、配置并填充本地数据库。第一级涵盖基础安装和数据填充，第二级引入配置和迁移管理，第三级模拟生产环境故障排除，包含人为设置的障碍（端口阻塞、迁移文件损坏、严格SQL模式），智能体必须通过错误信息分析进行诊断和修复。根据任务规范为每个实例添加验证命令。例如，对于一个需要智能体修复文件权限错误和损坏的初始化脚本以在目标位置创建可工作SQLite数据库的实例，我们创建以下验证命令来验证成功：

sqlite3 /data/test.db "SELECT COUNT(*) FROM logs;" | grep -q '[1-9]' && echo "Setup successful" || echo "Setup failed"

后台服务编排：我们设计了需要通过supervisord协调长时间运行服务的场景，包括Gunicorn服务器、使用Redis后端的Celery工作者、NGINX反向代理、文件监控守护进程、autossh隧道以及生产者-消费者管道。验证命令用于验证可观察的副作用，如HTTP响应、Redis键或日志消息。这些任务模拟了开发者必须配置和启动后台长时间运行服务的常见生产场景。

指标与评估

我们使用三个指标来评估智能体性能：

- **成功率**：智能体正确完成配置的百分比，由任务特定的验证命令确定，该命令输出"Setup successful"或"Setup failed"。
- **Token消耗量**：任务期间消耗的语言模型token总数。
- **步骤数**：智能体采取的操作数量（例如，shell命令、文件编辑等）。

这些指标同时捕捉了正确性和效率。成功率衡量智能体是否能完成配置任务，而token消耗量和步骤数揭示它们实现成功的效率——这在实际部署中至关重要，因为过度的资源消耗会增加成本、降低响应速度，并在多步骤工作流中存在上下文窗口溢出的风险。综合这三个指标，使我们能够区分通过精准、经济推理实现成功的智能体与仅在大量且可能浪费的探索之后才成功的智能体。

基准特征

确定性评估：与使用LLM作为评判方法的基准（GitGoodBench Lindenbauer等[2025]、DevBench Li等[2024a]）或可能不稳定的测试套件（SWE-bench Jimenez等[2024]）不同，SetupBench提供单行验证命令，生成字面的成功/失败字符串，消除了主观解释并确保了可重复的结果。

难度分级与领域广度：与SWE-bench仅关注Python Jimenez等[2024]或Aider的单语言代码编辑不同，SetupBench涵盖七种语言、五种数据库引擎和多种包管理器，涵盖范围从简单到复杂的任务。

从简单安装到复杂的多服务编排，这种多样性暴露了仅代码评估中看不到的故障模式，例如包管理器冲突和跨服务通信。这种广度使该基准更能代表开发者在实际工作中遇到的各种技术栈。

具有商业相关性的最小沙箱：与SWE-bench的预配置Docker容器不同（Jimenes等人，2024），SetupBench在全新的最小Linux容器中执行，代理必须显式安装包、配置数据库并从头处理依赖冲突。这反映了现代AI编码代理（OpenAI Codex、GitHub Copilot Chat、Cursor、Devin）在云沙箱中面临的真实部署场景。SetupBench衡量了实现从编写代码到在生产环境中运行之间差距缩小所需的实际系统管理和DevOps能力。

3

分析与结果

下文我们对OpenHands代理在SetupBench上的表现进行了全面评估，分析了其性能和行为模式。

3.1 实验设置

我们使用基于GitHub Workflows构建的自定义计算编排服务运行所有实验。对于每个基准实例，我们构建了包含任务环境和评估基础设施的标准Docker镜像，并在运行时注入代理代码以便于跨评估重用。每个容器以root权限和出站网络访问运行，启动一个黑盒入口点供代理使用，随后运行我们的自动化评估套件。代理完成其最终操作后，套件会在新的终端子进程中执行任务特定的验证命令——确保结果反映实际系统状态而非缓存输出。套件解析命令输出，根据"Setup successful"或"Setup failed"响应来确定设置是否成功。

我们对每次运行强制执行两小时的实际时间超时。容器在以下规格的虚拟机上启动：CPU：16核；内存：62GiB；磁盘：695GB。

3.2 性能结果

表2报告了各OpenHands变体的通过率、令牌使用量和工具调用步数。Claude 4 Sonnet具有最高的解决率（62.4%），但比表现第二好的基础模型Claude 3.7多使用约30%的令牌和多32%的步数。

3.3 故障模式分析

为了更好地理解通用编码代理在实际软件设置任务中的能力和局限性，我们对OpenHands代理在SetupBench上的失败轨迹进行了手动分析。我们的目标是识别可为指导未来代理设计和评估的重复性故障模式。我们的分析产生了三种故障模式：

忽略测试工具：代理成功安装了运行时依赖，但忽略了测试框架。例如，在nedbat-coveragepy-9d0eb02实例中，代理忽略了tox.ini文件，因此错过了安装测试工具：

```
apt-get install -y python3 python3-dev python3-pip build-essential gcc
cd /testbed && python3 -m pip install -e .
```

这导致验证命令因缺少测试运行器而失败：

```
/bin/sh: 1: tox: not found
Setup failed
```

在repo-setup类别中，我们将此故障模式归因于Claude 4中的5个实例、Claude 3.7中的7个实例、Claude 3.5中的7个实例、GPT-4o中的6个实例，以及GPT-4.1中的5个实例（基于对评估日志的手动检查）。当按每个模型不成功的repo-setup进行标准化时，这些约占失败的17-26%，证实忽略测试工具安装是端到端环境准备中的一个反复出现的高影响力障碍。

虚构的任务约束：代理推断出不存在的约束，导致它们做出有害的更改。例如，在danwahlin-angular-jumpstart-12fa4e4实例中，代理因根据虚构的指令修改服务器端口而失败：

任务描述中指定的端口（53012 或 56507）。让我们停止当前服务器并修改 server.js 文件。

越来越多的实证研究表明这一更广泛的失败模式在规模化场景中的表现。例如，Agarwal 等人 [2024] 报告称，24% 的 GPT-4 输出会注入虚假配置值。同样，Jiang 等人 [2024] 发现，超过 30% 与幻觉相关的故障源于虚构的标志位、包名或端口。

非持久化环境配置：智能体在全球范围内安装工具，但无法在 shell 会话之间保持这些更改。例如，在 dishait-tov-template-39c0898 实例中，pnpm 已安装，但在新 shell 中对评估工具不可用。

我们的观察与 EnvBench 的发现一致，该研究表明许多智能体故障可归因于工具“在新的 shell 中消失”[Eliseeva 等人，2025]。同样，对 Installamatic 的智能体评估表明，45% 的运行失败是因为使用 --user 安装的可执行文件在后续会话中不可见 [Milliken 等人，2025]。这些结果共同证实了人机协作中的一个更广泛挑战：为了实现人类开发者和智能体之间的无缝交接，智能体必须在不同上下文中明确引入连续性。

**表 3：S ETUP B ENCH（10 个实例子集）中每个仓库的步骤数和输入。**

| GitHub 仓库 | 语言 | 目录 | 文件 | 链接 | Bash | 总计 | 最优操作 | 智能体步骤 |
|------------|------|------|------|------|------|------|---------|-----------|
| openai/whisper | PY | 1 | 1 | 0 | 4 | 7 | 16 | 18 |
| madmaze/pytesseract | PY | 2 | 2 | 2 | 5 | 14 | 14 | 9 |
| TA-Lib/ta-lib-python | PY | 1 | 2 | 1 | 14 | 19 | 18 | 16 |
| spring-projects/spring-petclinic | JAVA | 1 | 1 | 0 | 4 | 7 | 6 | 16 |
| apache/cassandra | JAVA | 1 | 1 | 0 | 3 | 8 | 44 | 44 |
| habitat-sh/habitat | RUST | 1 | 3 | 0 | 9 | 16 | 13 | 19 |
| servo/servo | RUST | 1 | 2 | 1 | 11 | 16 | 14 | 17 |
| monero-project/monero | C++ | 2 | 2 | 0 | 6 | 12 | 17 | 17 |
| prometheus/prometheus | GO | 1 | 2 | 0 | 10 | 14 | 38 | 13 |
| caddyserver/caddy | GO | 1 | 1 | 0 | 8 | 11 | 13 | 17 |

**效率分析**

LLM 智能体在设置开发环境方面的效率直接影响下游性能，因为配置效率低下会增加 token 使用量、时间消耗以及偏离原始任务的风险 [Li 等人，2024c,b]。为了量化这种效率低下，我们通过分析人类配置行为并将其映射到等效的智能体动作来建立基准线。

我们分析了 10 个 SetupBench 实例中的人类轨迹，以识别每个配置任务的最小必要操作。人类通过结合目录遍历和内容列表的 UI 交互来浏览仓库，而 LLM 主要使用四个 bash 命令：head、cat、cd 和 ls。我们将每个人类文件夹探索翻译为 2 个 LLM 步骤，并将每个人类和智能体的文件读取作为单一操作。最小必要操作取决于定位设置命令和验证完整性所需的文件总数。例如，README 可能包含 pip install -e .，但设置测试框架可能需要遵循 docs/contributing.rst 中的说明，还可能需要通过扫描包含的 Dockerfile 进行额外验证。

**表 4：S ETUP B ENCH（10 个实例子集）中各模型的浪费步骤。**

| 模型 | 总步骤数 | 最优步骤数 | 浪费步骤数 | 浪费百分比 |
|------|----------|-----------|-----------|-----------|
| Claude 3.5 Sonnet | 186 | 124 | 71 | 38.17% |
| Claude 3.7 Sonnet | 332 | 124 | 208 | 62.65% |
| Claude 4 Sonnet | 397 | 124 | 273 | 68.77% |
| GPT 4o | 193 | 124 | 76 | 39.38% |
| GPT 4.1 | 238 | 124 | 114 | 47.90% |

我们排除了没有有意义人类等效项的智能体操作：（1）思考工具调用（内部推理），（2）完成调用（任务完成信号），（3）前三个步骤（系统提示、任务重述和上下文 priming），以及（4）轮询操作（进程监控、kill 命令）。这种过滤隔离了真正的行为效率低下。

如表 4 所示，在分析的 10 个实例中，所有模型都表现出显著的效率低下，浪费步骤从 38%（Claude 3.5）到 68%（Claude 4）不等。这表明在以下方面存在巨大的改进空间：

**改进智能体的探索和配置策略**

为了理解步数膨胀的原因，我们对智能体的轨迹进行了手动检查，并识别出三种主要的低效模式：

**冗余文件读取**：智能体经常重复发起对同一文件的多次部分读取，而不是一次性读取完整内容。例如，GPT-4.1经常执行如 `head -40`、`head -60`、`head -100`、`head -140` 这样的序列。这种行为在GPT-4.1中最普遍（34次冗余读取，占浪费步数的29.8%），而在Claude 3.7中最少（仅2次，占1%）。

**指令遵循不当**：尽管环境明确说明是全新的Ubuntu 22.0系统，无预装软件包且无root权限，智能体仍然浪费步数去检查已存在的安装包并执行不必要的sudo命令。这种行为反映出一个更广泛的挑战：如何使智能体的动作与环境先验知识保持一致。虽然部分智能体在遵循指令方面有所改进，但这种模式仍然普遍存在。例如，Claude 3.5执行了19次不必要的sudo命令和2次安装检查（占浪费步数的29.5%），而GPT-4.1仅进行了1次安装检查和5次sudo调用（占5.2%）。

**无关文件读取**：智能体读取了对于配置完成不必要的文件，包括辅助脚本、深度嵌套的配置文件以及不包含可操作配置指令的元数据文件。GPT-4.1显示最高发生率（占浪费步数的30.7%），而GPT-4o最具纪律性（13.1%）。

总体而言，这些模式凸显了通过更好的提示策略和指令对齐来改进智能体行为的特定方向。

**设计启示**

我们的实验评估揭示了若干关于提升智能体性能和人机协作的关键洞察：

**上下文感知的配置完成**。高测试工具失败率（20-27%的仓库配置失败）表明智能体缺乏关于完整开发环境的领域知识。它们无法从常规项目结构中推断所需工具（例如tox.ini、tox、package.json、npm test），有时甚至浪费token去探索与配置相关但无信息价值的文件。这揭示了智能体无法优先处理那些实际包含安装、构建或测试命令的文档子集。未来的设计应纳入语义搜索机制，根据内容和配置工作流相关性对文件进行排序。另一种可能性是在智能体上下文窗口早期注入仓库文件结构的树形表示，使其能够更明智地推理文件重要性和探索顺序。

**跨人机协作的环境持久化**。当智能体与人类异步协作时，一个关键摩擦点出现了：智能体经常进行临时性的环境修改（安装工具、修改PATH），这些修改在人类在新shell中恢复工作时不会持久化，导致配置工作无法访问。智能体应采用明确的持久化协议。首先，将环境修改写入持久配置文件（例如 `/etc/profile.d/agent.sh` 或 `.bashrc`）。其次，在当前会话中source这些文件以确保后续步骤能看到更新。第三，提供结构化的变更摘要。这将环境配置从临时shell修改转变为智能体与人类之间的持久契约。

**效率优先的探索策略**。智能体步数产生的38-69%开销揭示了它们通过冗余低级命令（cd、ls、head、cat）进行低效的仓库探索。与使用视觉工具进行层次化检查的人类不同，智能体缺乏持久的仓库模型，只能根据其最近上下文被动驱动。解决方案包括架构层面的改变，使智能体能够缓存目录结构、批量处理探索操作，并通过专用文件记忆模块维护项目布局的工作记忆。

系统抽象工具。

智能体与人类协作时的环境持久化问题。智能体将Shell视为临时性工具，作出不会跨会话保留的环境修改，导致人类接管时工具变得不可用。智能体应采用明确的持久化协议：将环境修改写入持久配置文件（例如.bashrc）、在当前会话中加载这些配置，并提供结构化的变更摘要。这将临时性修改转变为持久的智能体-人类契约。

模型选择策略。不同模型间的性能-效率权衡（如Claude 3.7：57%成功率，token消耗增加99%；GPT 4.1：50%成功率）表明，最优部署需要根据任务复杂度和资源约束进行动态模型选择。简单配置任务可受益于高效模型，而复杂的依赖解析任务可能需要更高容量的模型。

约束验证机制。智能体产生的幻觉任务约束表明，智能体需要内置验证系统，在做出配置决策时要求明确的文档引用，以防止虚假修改。

讨论

本节回顾了SetupBench中观察到的性能趋势，识别了当前模型行为的局限性，并概述了将基准测试扩展到更具挑战性和更现实的开发工作流程的机会。

智能体展现出强大的基础能力。现代编码智能体在环境配置场景中表现出稳健的基线性能。Claude 4以62.4%的最高解决率位居榜首，擅长后台服务配置（75.0%）和依赖解析（87.5%）。GPT-4.1达到50.5%，Claude 3.5 Sonnet达到53.8%。这些结果表明，智能体能够可靠地处理包安装、服务启动和依赖解析等常见配置任务。

失败反映了隐式推理和会话管理的不足。尽管取得进展，智能体仍表现出反复出现的失败模式。它们经常无法安装必要的开发工具（如测试运行器），即使文件（如tox.ini）明确表明了需求，却未能理解人类开发者的隐式期望。智能体还无法跨会话保留Shell状态——全局安装工具或更新环境变量时未将更改持久化到配置文件，导致后续命令失败。其他问题包括生成的端口号幻觉和不必要的配置修改，表明智能体对任务规范的把握不足。

效率和准确性的权衡出人意料地有利。不同模型的资源消耗差异显著。Claude 4的卓越性能需要1.049亿个token和4,377次工具调用——几乎是Claude 3.5 Sonnet（3,770万个token，1,793次调用）的三倍token和两倍调用次数。GPT-4.1使用4,010万个token和2,715次调用达到50.5%的解决率。这些数据表明，虽然更高性能往往需要更多token，但收益并非线性增长。值得注意的是，Claude 3.5在性能上比GPT-4.1高出3.5%，同时减少6%的token使用和34%的调用次数。这表明混合架构存在机会：轻量级模型用于常规任务，更强大的模型用于执行用户的核心目标。此类系统可以减少延迟、降低成本，并为最关键的阶段保留上下文。

SetupBench可以作为更具雄心的评估基础。在SetupBench上的出色表现支持更复杂的评估，以测试更高阶的推理和真实世界的工作流程。自然延伸包括：（1）将配置与下游开发任务（如bug修复或功能实现）串联，测试持续的上下文管理和前瞻性决策；（2）使用Terraform或Kubernetes等工具进行云基础设施管理，引入凭证管理、API可靠性和成本考量；（3）需要协调的系统迁移。

在多个资源和依赖项之间进行扩展。这些扩展将走向端到端评估，模拟实际开发者的工作流程，测试长期规划、适应能力以及超越基本正确性的任务连续性。

**局限性**

手动整理与规模：SetupBench 中的全部 93 项任务均经过人工审查和验证。大约一半完全由人工创建，另一半则改编自真实代码库。这种高投入的方式确保了清晰度和可靠性，但限制了扩展性。要扩展到数百或数千项任务，需要更高的自动化程度。现有的任务集以及用于生成任务的提示词和脚本为未来的扩展工作提供了良好的基础。

安全上下文：智能体以 root 权限运行且无出站网络限制，这简化了执行但无法反映真实世界的约束，如 CI/生产环境中有限的权限或受限制的网络访问。虽然此设置使我们能够专注于灵活环境中的功能性和正确性，但未来的扩展可以探索智能体在更严格的执行约束下如何适应。

领域覆盖范围：SetupBench 涵盖了七种语言生态系统、五种数据库以及各种服务编排模式，但忽略了 GPU 驱动程序、Redis 以外的消息队列、Docker Compose/Kubernetes 以及基础设施即代码工具。扩展这些领域将能够更全面地评估全栈和 DevOps 工作负载。

尽管存在局限性，SetupBench 为现实世界开发者智能体必须掌握的环境搭建技能提供了首个可复现的衡量标准。我们发布了提示词、脚本和评估工具，以鼓励社区贡献来解决这些差距。

**相关工作**

我们将先前的工作分为三个方向：假设工作环境已就绪的代码编辑基准测试、环境搭建与 DevOps 基准测试，以及用于通用智能体能力的工具使用评估套件。

代码编辑和完整流水线基准测试。SWE-BENCH [Jimenez et al., 2024] 及其验证版本提供了 2k+ 个 GitHub 问题，但将每个任务打包在定制的 Docker 镜像中，所有依赖项均已预装。DEV-BENCH [Li et al., 2024a] 将范围扩展到设计、编码和测试，但也分发预制容器。AGENT-BENCH [Liu et al., 2024] 评估跨领域（游戏、网络、推理）的多步骤智能体，但仅涵盖少量软件任务且不涉及系统配置。SetupBench 通过隔离代码更改之前的环境搭建阶段来补充这些工作。

环境搭建与 DevOps 评估。ENV-BENCH [Eliseeva et al., 2025] 是最接近的前身，针对 994 个 Python/JVM 仓库，通过静态分析或编译检查评分。它忽略了操作系统级包、数据库和守护进程编排，而 SetupBench 明确解决了这些问题。LADS [Khan et al., 2025] 提出了一个用于云配置的 LLM 框架并捆绑了一个小验证集，而 OPS-EVAL [Liu et al., 2025] 专注于 IT 运营中的问答。两者都不提供可运行的端到端搭建任务。因此，SetupBench 通过提供跨越语言、数据库和进程管理器的环境搭建实例来填补这一剩余空白。

工具使用基准测试。TOOL-BENCH [Qin et al., 2024] 和 STABLE-TOOL-BENCH [Guo et al., 2024] 评估智能体调用模拟 API 的能力；TOOL-RET [Shi et al., 2025] 衡量正确 API 的检索。这些数据集完全抽象掉了操作系统。早期 CLI 生成工作如 NL2BASH [Lin et al., 2018] 专注于单行命令。相反，SetupBench 需要多命令规划、包安装和服务监控，使工具使用更接近真实开发者的工作流程。

总之，虽然先前的基准测试阐明了软件工程的有价值方面，但都没有直接评估智能体是否能让代码运行起来。

旨在填补这一评估空白。

7 结论与未来工作

本文介绍了 SetupBench，这是一个综合性的基准测试，用于评估 AI 智能体在真实软件仓库设置任务中的表现。我们在 93 个多样化任务上对五个领先的语言模型进行了系统评估，首次大规模实证分析了智能体在环境初始化任务中的能力——这是软件工程自动化领域中关键但未被深入探索的一个方面。我们的研究结果揭示了当前编码智能体的优势和局限性。尽管表现最佳的模型（Claude 4）实现了 62.4% 的成功率，但仍存在重大挑战。我们识别出三种主要失败模式：未能安装隐式的开发工具、产生幻觉任务约束导致不必要的修改，以及非持久化的环境配置破坏了智能体与人类的协作工作流程。此外，所有模型都表现出严重的效率低下问题，38-69% 的步骤被浪费，原因是冗余的文件读取、遵循指令不当以及偏离目标的探索。SetupBench 将环境设置确立为软件工程自动化领域中一个独特且重要的评估维度。未来的工作应探索架构层面的改进，包括持久化的文件系统表示、语义搜索机制，以及平衡效率与准确性的混合方法，同时将基准测试扩展到多仓库设置和交互式配置场景。

9

参考文献

Vibhor Agarwal, Yulong Pei, Salwa Alamir, Xiaomo Liu. Codemirage: 大型语言模型生成代码中的幻觉现象。Proc. AutoMates Workshop @ IJCAI, 2024. URL https://arxiv.org/abs/2408.08333.

Aider. Aider 代码编辑工具。https://aider.chat/docs/benchmarks.html#the-benchmark.

Anthropic. 推出 Claude 4。https://www.anthropic.com/news/claude-4, 2025.

CognitionAI. 认识 Devin，首位 AI 软件工程师。https://www.cognition-labs.com/blog/devin, 2024.

Aleksandra Eliseeva, Alexander Kovrigin, Ilia Kholkin, Egor Bogomolov, Yaroslav Zharov. Envbench: 自动环境设置基准测试。ICLR 2025 第三次深度学习代码研讨会，2025. URL https://openreview.net/forum?id=izy1oaAOeX.

GitHub. GitHub Copilot 代码补全功能。https://code.visualstudio.com/docs/copilot/ai-powered-suggestions, 2022.

GitHub. 认识新一代 GitHub Copilot 编码智能体。https://github.blog/news-insights/product-news/github-copilot-meet-the-new-coding-agent/, 2025. 博客文章。

Zhicheng Guo, Sijie Cheng, Hao Wang, Shihao Liang, Yujia Qin, Peng Li, Zhiyuan Liu, Maosong Sun, Yang Liu. StableToolBench: 面向大型语言模型工具学习的大规模稳定基准测试。在 Lun-Wei Ku、Andre Martins、Vivek Srikumar 编者中，计算语言学协会发现：ACL 2024 会议论文集，页码 11143–11156，泰国曼谷，2024年8月。计算语言学协会。doi: 10.18653/v1/2024.findings-acl.664. URL https://aclanthology.org/2024.findings-acl.664/.

Nan Jiang, Qi Li, Lin Tan, Tianyi Zhang. Collu-Bench: 预测语言模型代码生成幻觉的基准测试。Proc. ACM/IEEE 国际软件测试与分析研讨会 (ISSTA), 2024. URL https://arxiv.org/abs/2410.09997.

Carlos E Jimenez, John Yang, Alexander Wettig, Shunyu Yao, Kexin Pei, Ofir Press, Karthik R Narasimhan. SWE-bench: 语言模型能否解决真实世界的 GitHub 问题？第十二届国际学习表征会议，2024. URL https://openreview.net/forum?id=VTF8yNQM66.

Ahmad Faraz Khan, Azal Ahmad Khan, Anas Mohamed, Haider Ali, Suchithra Moolinti, Sabaat Haroon, Usman Tahir, Mattia Fazzini, Ali R. Butt, Ali Anwar. LADs: 利用大型语言模型实现 AI 驱动的 DevOps。arXiv 预印本 arXiv:2502.20825, 2025.

Bowen Li, Wenhan Wu, Ziwei Tang, Lin Shi, John Yang, Jinyang Li, Shunyu Yao, Chen Qian 等. Devbench: 软件开发的综合基准测试。arXiv 预印本 arXiv:2403.08604, 2024.

以下是翻译成中文的内容：

---

**参考文献**

Huayang Li, Pat Verga, Priyanka Sen, Bowen Yang, Vijay Viswanathan, Patrick Lewis, Taro Watanabe, 和 Yixuan Su。ALR2：一种用于长上下文问答的检索-推理框架。arXiv 预印本 arXiv:2410.03227, 2024b。URL https://arxiv.org/abs/2410.03227。

Tianle Li, Ge Zhang, Quy Duc Do, Xiang Yue, 和 Wenhu Chen。大型语言模型在长上下文学习中面临困难。arXiv 预印本 arXiv:2404.02060, 2024c。URL https://arxiv.org/abs/2404.02060。

Xi Victoria Lin, Chenglong Wang, Luke Zettlemoyer, 和 Michael D. Ernst。NL2Bash：一个用于自然语言接口的语料库和语义解析器，面向 Linux 操作系统。arXiv 预印本 arXiv:1802.08979, 2018。

Tobias Lindenbauer, Egor Bogomolov, 和 Yaroslav Zharov。Gitgoodbench：用于评估 Git 智能体性能的新型基准。arXiv 预印本 arXiv:2505.22583, 2025。

Xiao Liu, Hao Yu, Hanchen Zhang, Yifan Xu, Xuanyu Lei, Hanyu Lai, Yu Gu, Hangliang Ding, Kaiwen Men, Kejuan Yang, Shudan Zhang, Xiang Deng, Aohan Zeng, Zhengxiao Du, Chenhui Zhang, Sheng Shen, Tianjun Zhang, Yu Su, Huan Sun, Minlie Huang, Yuxiao Dong, 和 Jie Tang。AgentBench：作为智能体评估大型语言模型。第十二届国际学习表征会议，2024。URL https://openreview.net/forum?id=zAdUB0aCTQ。

Yuhe Liu, Changhua Pei, Longlong Xu, Bohan Chen, Mingze Sun, Zhirui Zhang, Yongqian Sun, Shenglin Zhang, Kun Wang, 等。Opseval：用于评估大型语言模型在 IT 运维领域能力的综合基准套件。arXiv 预印本 arXiv:2310.07637, 2025。

Louis Milliken, Sungmin Kang, 和 Shin Yoo。超越 pip install：评估用于自动安装 Python 项目的语言模型智能体。IEEE 软件分析、演化与重构国际会议论文集，SANER '25, 2025。

Yanzhou Mu, Rong Wang, Juan Zhai, Chunrong Fang, Xiang Chen, Jiacong Wu, An Guo, Jiawei Shen, Bingzhuo Li, 和 Zhenyu Chen。为大型语言模型设计深度学习框架：挑战、预期与机遇。arXiv 预印本 arXiv:2506.13114, 2025。

Marcos Nazário, Rodrigo Bonifacio, 和 Gustavo Pinto。缓解开发与生产环境之间的配置差异：策略目录。arXiv 预印本 arXiv:2505.09392, 2025。

Ike Obi, Jenna Butler, Sankeerti Haniyur, Brian Hassan, Margaret-Anne Storey, 和 Brendan Murphy。识别导致软件开发者"糟糕一天"的因素：一项混合方法研究。arXiv 预印本 arXiv:2410.18379, 2024。

OpenAI。引入 Codex。https://openai.com/index/introducing-codex, 2025。博客文章。

Yujia Qin, Shihao Liang, Yining Ye, Kunlun Zhu, Lan Yan, Yaxi Lu, Yankai Lin, Xin Cong, Xiangru Tang, Bill Qian, Sihan Zhao, Lauren Hong, Runchu Tian, Ruobing Xie, Jie Zhou, Mark Gerstein, dahai li, Zhiyuan Liu, 和 Maosong Sun。ToolLLM：助力大型语言模型掌握 16000 余个真实世界 API。第十二届国际学习表征会议，2024。URL https://openreview.net/forum?id=dHng2O0Jjr。

Zhengliang Shi, Yuhan Wang, Lingyong Yan, Pengjie Ren, Shuaiqiang Wang, Dawei Yin, 和 Zhaochun Ren。检索模型不擅长工具：为大型语言模型进行工具检索的基准测试。第 63 届计算语言学协会年会论文集，2025。

---

**示例数据集条目**

```json
{
  "ecosystem": "bundler-compat",
  "base_commit": "45b78114e1a5dab96e59fc70933277a56f65b53b",
  "success_command": "bundle install --jobs=1 --retry=2 --without development test",
  "instance_id": "deps-acts_as_bookable-45b78",
  "problem_statement": "该项目在运行 bundle install --jobs=1 --retry=2 --without development test 时存在依赖冲突。请解决该依赖冲突，使命令能够成功运行。",
  "task_type": "dependency_resolution",
  "base_image": "ruby:2.7"
}
```

**字段约定**。每个条目包含 instance_id、problem_statement、success_command、base_image 和 task_type。类别特定的

## 附录B

## 仓库设置提示模板

### B.1 设置指令推导提示

您需要分析一个GitHub仓库，并为项目提供详细的设置说明。这是一个基准测试的一部分，用于评估自主软件工程代理正确设置项目的能力。……
<repo_url> https://github.com/ceph/ceph </repo_url>

1. 彻底分析仓库文档……
2. 基于您的分析，提供详细的分步指南……
3. 确定一个可用于验证成功设置的"success_criteria_command"命令……
<setup_instructions>
[在此处提供分步设置说明]
</setup_instructions>
<success_criteria_command>
[提供用于验证成功设置的单行命令]
</success_criteria_command>
注意：沙盒环境为Ubuntu 22.04，且未预装任何软件。

### B.2 成功命令合成提示

您需要为软件工程基准测试创建一个成功命令。此命令将用于评估仓库是否已被正确设置和配置。

仓库URL：
<repo_url>
{{ repo_url }}
</repo_url>

仓库中的Markdown文件：
<markdown_files>
{{ markdown_files }}
</markdown_files>

指南：
1. 输出"Setup successful"或"Setup failed"。
2. 仅在仓库完全配置成功时才算成功。
3. 测试关键功能或组件。
4. 如需要，可使用&&链接命令。

请按以下格式提供您的答案：
<success_command>
您的bash命令
</success_command>
<explanation>
此命令为何能验证正确设置
</explanation>

目标仓库中的所有Markdown文件都会被注入到{{ markdown_files }}占位符中，从而使语言模型在制作验证命令时拥有丰富的上下文信息。

## 附录C

## 依赖解析资源

### C.1 挖掘脚本

```python
#!/usr/bin/env python3
import os
import re
import json
from pathlib import Path
from github import Github
GITHUB_TOKEN = ""
if not GITHUB_TOKEN:
    raise RuntimeError("请设置GITHUB_TOKEN环境变量")
# 定义生态系统，包含搜索查询、错误正则表达式和锁文件
ECOSYSTEMS = {
    "npm-peer-dep": {
        "search_query": "npm ERR! peer dep is:issue in:comments state:closed,→ language:JavaScript",
        "regex": re.compile(r"npm ERR! peer dep", re.IGNORECASE),
        "manifest": "package.json",
        "lockfiles": ["package-lock.json", "yarn.lock"],
    },
    "npm-eresolve": {
        "search_query": "npm ERR! code ERESOLVE is:issue in:comments state:closed,→ language:JavaScript",
        "regex": re.compile(r"npm ERR! code ERESOLVE", re.IGNORECASE),
        "manifest": "package.json",
        "lockfiles": ["package-lock.json", "yarn.lock"],
    },
    "pip-conflict": {
        "search_query": "ERROR: Could not install is:issue in:comments state:closed,→ language:Python",
        "regex": re.compile(r"ERROR: (?:Could not,→ install|ResolutionImpossible)", re.IGNORECASE),
        "manifest": "requirements.txt",
        "lockfiles": ["Pipfile.lock", "poetry.lock"],
    },
    "poetry-conflict": {
        "search_query": "ResolutionImpossible is:issue in:comments state:closed,→ language:Python",
        "regex": re.compile(r"ResolutionImpossible", re.IGNORECASE),
        "manifest": "pyproject.toml",
        "lockfiles": ["poetry.lock"],
    },
    "bundler-compat": {
        "search_query": "Bundler could not find compatible versions is:issue in:comments state:closed language:Ruby",
        "regex": re.compile(r"Bundler could not find compatible versions", re.IGNORECASE),
        "manifest": "Gemfile",
        "lockfiles": ["Gemfile.lock"],
    },
}

MAX_ISSUES = 500
OUTPUT = Path("mined_conflicts.jsonl")
def main():
    gh = Github(GITHUB_TOKEN)
    with OUTPUT.open("a") as out:
        for eco, cfg in ECOSYSTEMS.items():
            print(f"正在挖掘 [{eco}]")
            for issue in gh.search_issues(cfg["search_query"],
                    sort="updated",
                    order="desc")[:MAX_ISSUES]:
                for comment in issue.get_comments():
                    body = comment.body or ""
                    if not cfg["regex"].search(body):
                        continue
                    # 获取issue创建时的提交
                    default_branch = issue.repository.default_branch
                    commits = issue.get_commits()
```

以下是该代码段的中文学术翻译：

```python
e.repository.get_commits(sha=default_branch,
until=issue.created_at)
base_commit = commits[0].sha if commits.totalCount else None
# 确保至少存在一个锁文件
lockfiles_found = []
for lf in cfg["lockfiles"]:
try:
issue.repository.get_contents(lf, ref=base_commit)
lockfiles_found.append(lf)
except: # noqa: E722
continue
if not lockfiles_found:
continue
snippet = "\n".join(
line for line in body.splitlines()
if cfg["regex"].search(line)
)
entry = {
"ecosystem": eco,
"repo": issue.repository.full_name,
"issue_number": issue.number,
"issue_url": issue.html_url,
"comment_id": comment.id,
"snippet": snippet,
"matched_at": comment.updated_at.isoformat(),
"base_commit": base_commit,
"manifest": cfg["manifest"],
"lockfiles_found": lockfiles_found
}
out.write(json.dumps(entry) + "\n")
print(f" • 已挖掘 {eco} → {entry['repo']}#"
f"{entry['issue_number']} @ {base_commit}")
if __name__ == "__main__":
main()

14

C.2

验证脚本

#!/usr/bin/env python3
import os, re, json, tempfile, subprocess, shutil
from pathlib import Path
from concurrent.futures import ThreadPoolExecutor, as_completed
from tqdm import tqdm
from github import Github
ECOSYSTEMS = {
"npm-eresolve": {
"image": "node:16",
"setup": "npm install -g npm@7",
"cmds": [
"npm ci --ignore-scripts",
"npm install --ignore-scripts --legacy-peer-deps"
],
"err": re.compile(r"npm ERR! code ERESOLVE", re.IGNORECASE),
},
"npm-peer-dep": {
"image": "node:16",
"setup": "npm install -g npm@7",
"cmds": [
"npm ci --ignore-scripts",
"npm install --ignore-scripts --legacy-peer-deps"
],
"err": re.compile(r"npm ERR! peer dep", re.IGNORECASE),
},
"pip-conflict": {
"image": "python:3.9",
"setup": None,
"cmds": [
"pip install --no-build-isolation --no-deps -r requirements.txt",
"pip install --no-build-isolation -r requirements.txt"
],
"err": re.compile(r"ERROR: (?:Could not install|ResolutionImpossible)",
re.IGNORECASE),
},
"poetry-conflict": {
"image": "python:3.9",
"setup": None,
"cmds": [
"pip install poetry && poetry install --no-root "
"--no-interaction --no-scripts",
"poetry install --no-root --no-interaction --no-scripts"
],
"err": re.compile(r"ResolutionImpossible", re.IGNORECASE),
},
"bundler-compat": {
"image": "ruby:2.7",
"setup": None,
"cmds": [
"bundle install --jobs=1 --retry=2 --without development test"
],
"err": re.compile(r"Bundler could not find compatible versions",
re.IGNORECASE),
},
}

15

# 配置
GITHUB_TOKEN = ""
INPUT = Path("mined_conflicts.jsonl")
OUTPUT = Path("validated_results.jsonl")
gh = Github(GITHUB_TOKEN)
def load_entries():
for line in INPUT.open():
yield json.loads(line)
def get_done():
seen = set()
if OUTPUT.exists():
for l in OUTPUT.open():
r = json.loads(l)
seen.add((r["repo"], r["issue_number"], r["comment_id"]))
return seen
def record(entry, success, out):
r = {**entry,
"validation_success": success,
"install_output": out.strip(),
"validated_at": __import__("datetime").datetime.utcnow()
.isoformat()+"Z"}
with OUTPUT.open("a") as f:
f.write(json.dumps(r) + "\n")
def docker_run(workdir, image, cmd):
full = ["docker", "run", "--rm", "-v", f"{workdir}:/app",
"-w", "/app", image, "sh", "-c", cmd]
p = subprocess.run(full, capture_output=True, text=True)
return p.stdout + p.stderr

def process(entry, done):
key = (entry["repo"], entry["issue_number"], entry["comment_id"])
if key in done:
return
cfg = ECOSYSTEMS[entry["ecosystem"]]
tmp = Path(tempfile.mkdtemp(prefix="val_"))
out, success = "", False
try:
repo_url = f"https://github.com/{entry['repo']}.git"
subprocess.run(["git", "clone", "--depth", "1", repo_url, tmp/"r"],
check=True)
subprocess.run(["git", "-C", tmp/"r", "fetch", "--depth", "1",
"origin", entry["base_commit"]], check=True)
subprocess.run(["git", "-C", tmp/"r", "checkout",
entry["base_commit"]], check=True)
wd = str(tmp/"r")
if cfg["setup"]:
out += docker_run(wd, cfg["image"], cfg["setup"])
for cmd in cfg["cmds"]:
out += docker_run(wd, cfg["image"], cmd)
if cfg["err"].search(out):
success = True

16

break
except Exception as e:
out
```

# 数据库设置示例：MySQL 层级架构

## D.1 层级 1 — 基础安装 + 单一转储

**实例 ID**: dbsetup-mysql-1

**成功命令**:
```bash
mysql -u root -e "USE benchmark_db; SHOW TABLES;" | grep -q products \
&& echo "Setup successful" echo "Setup failed"
```

**任务要求**:
- 使用 root 登录进行非交互式 MySQL 安装。
- 创建 benchmark_db 数据库。
- 解压缩并导入 dump.sql.gz 文件。

## D.2 层级 2 — 有序迁移 + 字符集调整

**实例 ID**: dbsetup-mysql-2

**成功命令**:
```bash
mysql -u root -e "USE benchmark_db; SHOW TABLES;" | grep -q products \
&& echo "Setup successful" echo "Setup failed"
```

**任务要求**:
- 应用带有外键的编号 .sql.gz 迁移文件。
- 确保服务器和数据库使用 utf8mb4 字符集。
- 启用 root 密码认证。

## D.3 层级 3 — 端口变更、严格模式、用户权限

**实例 ID**: dbsetup-mysql-3

**成功命令**:
```bash
mysql -u benchmark_user -pbenchmark_pass -e \
"USE benchmark_db; SELECT COUNT(*) FROM products;" | grep -q '[1-9]' \
&& echo "Setup successful" echo "Setup failed"
```

**任务要求**:
- 在端口 3307 上运行 MySQL（端口 3306 被阻塞）。
- 在 STRICT_TRANS_TABLES 模式下运行；对引用缺失 DEFINER 的迁移进行修补。
- 重新排序并修复顺序错乱的迁移文件。
- 创建具有相应权限的 benchmark_user/benchmark_pass 用户。

---

# 后台服务示例：Gunicorn + Unix 套接字

**实例 ID**: bgsetup-gunicorn-systemd-socket

**成功命令**:
```bash
curl --unix-socket /tmp/gunicorn.sock http://localhost/ | grep -q "Hello" \
&& echo "Setup successful" echo "Setup failed"
```

**任务要求**:
- 安装 Python、Flask、Gunicorn 和 supervisord。
- 通过 Gunicorn 在 /tmp/gunicorn.sock 上提供 /testbed/app.py 服务。
- 配置 supervisord 以在故障时重启。
- 端点必须通过 Unix 套接字返回字符串 "Hello"。



---

## 论文 4

# Process-Level Trajectory Evaluation for Environment Configuration in Software Engineering Agents

**作者**: Jiayi Kuang, Yinghui Li, Xin Zhang, Yangning Li, Di Yin

**arXiv**: https://arxiv.org/abs/2510.25694

---

EnConda-Bench：软件工程智能体的环境配置过程级轨迹评估

江毅·康、应辉·李、欣·张、阳宁·李、迪·尹、星·孙、英·申、菲尔·S·于

1 腾讯优图实验室 Youtu-LTM 团队  
2 中山大学  
3 香港理工大学  
4 伊利诺伊大学芝加哥分校

基于大语言模型的智能体在软件工程领域展现出巨大潜力，然而环境配置仍是一个关键瓶颈，因为其需要大量的人工操作，且缺乏大规模、高质量的数据集。现有基准测试仅评估端到端的构建/测试成功与否，无法清晰揭示智能体成功或失败的具体原因与位置。我们提出了环境配置诊断基准测试 EnConda-Bench，它能够对智能体在环境配置过程中的细粒度能力进行过程级轨迹评估，涵盖环境搭建规划、感知驱动的错误诊断、反馈驱动的修复以及最终环境配置的执行。我们的任务实例通过注入真实的 README 错误自动构建，并在 Docker 中进行验证，以实现规模化的高质量评估。EnConda-Bench 将过程级分析与端到端可执行性相结合，能够实现超越单纯成功率聚合的能力评估。对当前最先进的 LLM 和智能体框架的评估表明，尽管智能体能够定位错误，但难以将反馈转化为有效的修正，这限制了端到端性能。据我们所知，EnConda-Bench 是首个提供环境配置过程级内部能力评估的框架，为改进软件工程智能体提供了可行的见解。

### 1 引言

大语言模型（LLM）的快速发展推动了具有高学术和工业价值的挑战性软件工程（SWE）任务的探索 [He et al., 2025, Wang et al., 2024, Fan et al., 2023, Wang et al., Zhang et al., 2024]。软件工程提供了精确、可验证的评估系统，使其成为研究智能体智能的理想领域 [Hendrycks et al., Austin et al., 2021]。许多面向代码的智能体，如 OpenHands [Wang et al.] 和 Swe-Agent [Yang et al., 2024]，旨在协助复杂的项目开发和维护。在 SWE-BENCH [Jimenez et al., 2024] 等软件工程基准测试中，智能体会根据给定的问题编辑和修复代码，然后提交拉取请求并验证执行。在此工作流中，配置可运行的执行环境是最基本且关键的第一步，然而这对于人类工程师和当前的大语言模型 [Eliseeva et al.] 来说仍然是一个挑战，需要大量的人工操作。这种负担限制了大规模、高质量数据集的生产，使得对智能体环境配置能力的严格评估对于软件工程的进步至关重要。

大多数现有的环境配置基准测试依赖于端到端的成功（构建和测试通过）[Milliken et al., 2025, Bouzenia and Pradel, 2025, Eliseeva et al., Vergopoulos et al., 2025]，只能产生粗粒度的结果，无法揭示配置轨迹中过程级的能力。例如，很难定位环境配置中容易出错的特定阶段，也难以识别哪些能力不足导致了最终的失败。

**环境配置示例：**

> 我想为一个代码库设置环境……  
> # 按照 README 操作  
> # 安装依赖  
> pip install 'pyHanko[pkcs11,imagesupport,opentype,xmp]'  
> 错误安装！！  
> 思考并尝试修复错误……  
> # 下一步  
> 
> 然而，我遇到了一些问题……  
> 错误 1：依赖安装错误  
> 错误 4：文件路径或文件缺失错误  
> 错误 2：命令使用错误  
> 
> 说明说要安装 `yamllint`，但实际需要的工具是 `ansible-lint`。  
> ansible/ansible-zuul

日期：2025 年 10 月 29 日  
代码与数据：https://github.com/TencentYoutuResearch/EnConda-Bench

# 错误分析

## 语法错误
命令 `bob bu ild` 的调用方式不正确，因为该项目使用 GNU Make。

## 错误 6：逻辑顺序错误
图像文件目录的命名不正确。

## 错误 7：版本兼容性错误
README 文档错误地声明支持 Python 3.6，但项目需要 Python 3.8 或更高版本，这将导致安装失败。

---

# 图 1：环境配置常见问题示意图

当人类工程师配置环境时，他们经常会遇到各种错误。他们应该首先确定发生错误的步骤，然后修复该问题，再继续进行下一步，直到配置完成。同样，执行环境配置的智能代理也应该具备良好的规划、感知、反馈和行动能力。

当前研究的一个主要限制在于，我们无法精确识别代理在执行过程中缺乏哪些能力，从而进行更精准有效的配置。它们无法定位失败阶段或缺失的能力，限制了深入洞察和研究方向的探索。此外，数据构建也是一个瓶颈：高质量的、可正确构建的仓库资源稀缺；选择和标注这些仓库需要专家投入。因此，研究人员难以获得大量高质量的数据来评估代理的环境配置能力。

为了应对这些挑战，我们专注于沿代理配置轨迹的过程级评估。具体而言，我们研究：（1）代理如何运用规划能力，根据任务要求制定合理的配置步骤和策略；（2）代理如何使用感知能力，在发生故障时准确定位错误原因（例如版本不兼容或依赖缺失）；（3）代理如何利用反馈能力分析错误并尝试修复错误；（4）代理如何将精确的反馈转化为纠正这些错误、完成环境配置并确保后续代码运行和通过评估的行动。这种过程级轨迹评估为改进代理在环境配置中的能力以及后续相关研究提供了更深层次且更有价值的参考。

然而，直接从代理轨迹中提取规划和反馈片段，或评估整个长轨迹，存在一定困难。借鉴人类工程师配置环境的方式——通常首先遵循 README 步骤，然后分析失败原因并尝试修复——我们考虑通过注入错误命令或混淆步骤来编辑原本正确的 README。当模型根据此类 README 配置环境时，它必须定位并修复这些错误。这种设计能够沿代理轨迹进行过程级评估，并使我们能够观察模型更容易修复哪些错误类型，以及哪些错误更难检测，从而为未来的代理开发提供有价值的洞察。

受此任务模式的启发，我们进一步设计了一个自动化数据构建框架，用于扩展实例生成并产生用于训练的代理执行轨迹。我们（1）通过严格标准选择高质量仓库；（2）使用先进的 LLM 编辑关键环境 README，注入常见错误类型并标注类别和建议修复方案；（3）通过自动化框架验证和筛选有效错误，以获得高质量任务实例。我们随后构建了一个评估套件，支持过程级分析（错误定位、修复）和端到端可执行性，以及一个自动数据工程管道，用于生成任务实例和代理执行轨迹。据我们所知，我们是首个实现代理过程级评估并在此环境下提出自动化数据框架的团队。在先进 LLM 和代理上的实证评估表明：

虽然智能体展现出基本的错误判断/定位能力，但它们难以将反馈转化为有效的纠正操作，这限制了端到端的性能。我们提出以下贡献及若干值得注意的发现：

EnConda-Bench

• 我们提出了一种基于轨迹的EnConda-Bench，用于对软件工程环境中配置进行过程级评估，能够对智能体在环境配置过程中展现的能力进行详细评估。
• 我们引入了一条自动化数据构建 pipeline，以减少人工劳动并为智能体和大语言模型提供大规模训练数据。
• 我们对多个大语言模型/智能体进行的评估发现，它们具备基本的错误定位/分类能力，但在环境交互和反馈利用方面表现有限，往往产生无效的修复，这为后续研究提供了有价值的发现和启发。

相关工作

智能体方法 早期的智能体尝试依赖从源代码推断依赖关系的特定启发式方法来自动化环境设置，虽然提供了确定性，但在系统包管理、版本锁定和平台异构性方面存在不足[Gruber and Fraser, 2023, Zhang et al., 2024, Yang et al., 2025]。工具增强型代码智能体为大语言模型赋予了搜索、编辑和执行能力，展现出一定前景[Wang et al., 2024, Wang et al., Zhang et al., 2024, Yang et al., 2024, Xia et al., 2024]，然而由于对外部工具链的敏感性和冗长的决策链，环境设置仍然是脆弱的瓶颈。专用环境智能体试图缩小这一差距。INSTALLAMATIC 针对 Python 项目提供精选的安装上下文和示例 Docker 文件，通过测试判断成功与否[Milliken et al., 2025]。EXECUTIONAGENT 将适用范围扩展到五种语言，基于 CI 日志作为真实标签，需要同时提供 Docker 文件和设置脚本，并评估构建成功率和测试结果偏差[Bouzenia and Pradel, 2025]，但仍需人工检查且速度相对较慢。Repo2Run 采用双环境架构，在隔离的 Docker 环境中执行配置，同时利用外部环境进行监控和辅助，并配备回滚机制以在命令失败时将系统恢复到最后一个已知的稳定状态[Hu et al., 2025]。总体而言，轨迹方法从启发式方法演进到工具增强型智能体，再到交互式智能体，而我们的方法旨在改进与环境的过程级、可操作交互。

环境配置基准 早期软件工程基准（如 HumanEval、MBPP、APPS）推动了函数级基准的发展，但与真实世界的构建场景不符[Chen et al., 2021, Hendrycks et al., Austin et al., 2021, Jain et al.]。仓库级工作更好地反映了实际做法[Liu et al., 2024, Jain et al., 2024, Jimenez et al., 2024]，但它们通过提供手动配置的 Docker 文件而忽略了环境配置任务。专门的基准测试正在探索这一领域。INSTALLAMATICbench 整理了 40 个 Python 仓库及示例 Docker 文件，通过测试评估成功率[Milliken et al., 2025]。EXECUTIONAGENTbench 涵盖五种语言，基于 CI 日志作为真实标签，评估构建/测试成功率和测试结果偏差[Bouzenia and Pradel, 2025]。为实现更大规模和更多语言的支持，近期基准 EnvBench 扩展到 994 个跨 Python、Java 和 Kotlin 项目的仓库，但在数据收集和评估策略方面仍缺乏透明度[Eliseeva et al.]。因此，自动化构建进一步扩大了评估规模。SETUPAGENT 进一步自动化了安装和测试程序的提取，支持历史状态收集，并收集测试级结果，加速了数据生成，尽管其评估仍然主要是端到端的[Vergopoulos et al., 2025]。然而，大多数基准仍然将评估简化为端到端的可执行性，掩盖了设置失败的位置和原因。相比之下，我们的工作提供了过程级轨迹评估。

# 中文翻译

## EnConda-Bench：自动化数据构建框架

**摘要**：本文提出了一种自动化数据构建框架，在规模、多样性和诊断深度之间取得平衡，以实现对智能体的稳健评估。

---

### EnConda-Bench 工作流程示例

**输入任务实例**

- **智能体执行**
  - **错误类型**：命令使用或语法错误
  - **错误描述**：`pip install -r requirements.txt --update-all` 命令不正确，因为 `--update-all` 不是有效的 pip 标志。

- **标准答案 JSON**
  ```json
  {
    "readme": "ajenti_ajenti_error_readme_1_only_E2",
    "errors": [
      {
        "error_type": "Command Usage or Syntax Error",
        "error_description": "The command to install dependencies `pip install -r requirements.txt --update-all` is incorrect because `--update-all` is not a valid pip flag.",
        "correction_candidates": [
          "Run `pip install -r requirements.txt`",
          "Run `pip update --all -r requirements.txt`"
        ],
        "golden_answer": "Run `pip install -r requirements.txt`"
      }
    ]
  }
  ```

**进程级评估**

- 错误类型判断：命令使用或语法错误
- 错误描述判断：命令 `pip install -r requirements.txt --update-all` 不正确，因为 `--update-all` 不是有效的 pip 标志。

**流程图说明**：图 2 展示了环境配置任务的整体工作流程，包括智能体执行 Shell 脚本、Docker 容器运行以及成功/失败判定。

---

### 3.1 EnConda-Bench 任务定义与工作流程

如图 2 所示，EnConda-Bench 要求智能体诊断并修复环境配置错误。具体而言，当发生错误时，智能体应当：（i）识别失败发生的步骤；（ii）分析精确的错误类型； （iii）规划适当的修复策略。在此基础上，智能体应完善其反馈和纠正措施，最终生成准确配置环境的完整 Shell 脚本。在评估方面，我们同时考察：（a）环境是否成功构建并可执行；以及（b）智能体的执行轨迹是否展示了正确的错误定位、推理和反馈使用。具体而言，任务设计和完整流程包含三个组件：输入任务实例、智能体执行和评估。

#### 输入任务实例

每个任务实例包括：

1. **代码仓库（Repository）**：我们收集并筛选了一组高质量的 GitHub 仓库，以确保可复现性和中等难度。为避免评估期间的版本漂移，每个代码仓库都固定到特定的提交版本。
2. **Dockerfile**：参考 EnvBench [Eliseeva et al.]，我们提供了一个包含最小化前置条件（如 Python、Conda）的基础 Docker 镜像。我们让智能体在 Docker 容器内执行环境配置。
3. **自述文件（README）**：无论是人类还是智能体，通常都从仓库的 README 开始进行环境配置。因此，每个任务实例都将 README 作为智能体执行的主要指南。
4. **标注的标准答案 JSON**：对于每个任务实例，我们提供一个 JSON 文件以支持评估，包括错误类型的标准答案、详细的错误描述、候选修复命令集以及最终正确的命令序列。

#### 智能体执行

对于每个实例，智能体利用其规划能力，结合提供的 README 规划环境配置步骤序列。凭借其感知能力，智能体仔细检查 README 和代码仓库以识别潜在错误。当遇到错误时，智能体需要分析错误原因并生成相应的修复命令。

---

### 代码仓库选择与错误合成

**选择标准**：

- ☐ Star 数量超过 10
- ☐ Issue 数量超过 10
- ☐ 提交次数超过 1000

**数据过滤**：

- GitHub 手动检查
- 代码库质量评估
- 自动验证
- 错误有效性检验

**README 编辑与错误生成**：

- 使用 Gemini 和 Claude 等工具
- 生成依赖安装错误
- 生成命令使用或语法错误
- 创建错误 JSON 文件

---

### 术语对照表

| 英文术语 | 中文术语 |
|---------|---------|
| Agent | 智能体 |
| Repository | 代码仓库 |
| Dockerfile | Docker 镜像定义文件 |
| README | 自述文件 |
| Golden Answer | 标准答案 |
| Error Type | 错误类型 |
| Environment Configuration | 环境配置 |
| Process-Level Evaluation | 进程级评估 |
| Evaluation Pipeline | 评估流程 |
| Commit | 提交（版本控制） |
| Docker Container | Docker 容器 |

# 基准测试构建流程说明

**错误类型**：
依赖安装错误、命令使用或语法错误、文件路径或缺失文件错误、逻辑顺序错误、版本兼容性错误、其他类型错误

**检查**：判断是否与GPT一致

**图3**：基准测试构建的整体流程示意图。

代理在诊断错误时，会运用反馈机制，并对其进行详细分析性推理，同时制定相应的修复策略。凭借其动作技能，代理实施所提出的修复方案并生成环境设置的shell脚本。执行后，我们处理轨迹信息并提取错误类型判断、修复命令以及最终的shell脚本。

**过程级评估**：基于从轨迹和最终shell脚本中提取的判断信息，我们采用两种互补的方法进行过程级评估。对于错误诊断，我们将预测的错误类型、描述和修复建议与标准JSON进行对比，并计算相应的指标。对于可执行性，我们拉取Docker和仓库，运行代理的shell脚本，并检查其是否成功构建环境并通过单元测试。该评估套件对代理在环境配置方面的能力进行了过程级评估，揭示了代理哪些能力较弱、哪些错误类别更容易被检测到，以及哪些更具挑战性。

**3.2 数据构建**

**仓库选择**：尽管GitHub托管着大量仓库，但许多仓库无法满足可靠环境配置的要求。如果一个仓库不可靠（例如由于README有误或依赖缺失），错误标注将变得劳动密集且不可靠，由此产生的任务可能过于困难。因此，我们保留满足以下高质量指标条件的仓库：至少10颗星、超过1000次提交以及超过10个已关闭的问题。此外，我们纳入来自现有基准测试的仓库，这些仓库经过严格的人工筛选和环境设置的人工验证，并将其作为后续错误合成的依据。仓库选择的详细信息见附录A.1。

**错误合成**：在收集高质量仓库后，我们编辑README以合成现实且常见的配置错误。事实上，我们的最初计划并不涉及合成错误，而是考虑通过将现有README分解为可执行步骤（以评估每个步骤是否正常运行）或对固有易错步骤进行标注来加以利用。然而，这种方法劳动强度极大。如果没有步骤或错误标注，仅使用工具对代理轨迹进行评估将过度依赖模型本身，使得难以从长轨迹中提取关键步骤并探索具体能力。此外，每个仓库通常只包含一个README，而高质量仓库稀缺，这限制了可用任务实例的数量。

为解决这些问题，我们将每个可执行的README作为真实值并注入错误。这使得可扩展的自动化任务生成成为可能，并支持对环境配置过程中的规划、感知、反馈和动作进行过程级评估。我们定义了六类标准错误：依赖安装错误、命令使用或语法错误、文件路径或缺失文件错误、逻辑顺序错误、版本兼容性错误以及其他类型错误（详见附录A.2中的定义和示例）。对于每个README，我们提示claude-4-sonnet和gemini-2.5-pro引入两种错误，并生成包含错误类型、描述、候选修复方案和真实值的结构化JSON，同时指示进行最小化编辑，仅限于必要行，避免可能损害README完整性的广泛重写。

egrity（详见附录A.3）。以严格筛选的原始README文件为参考，每个案例将产生一个受控的错误标签、具体描述和正确的修复方案。从323个代码库中，我们生成了1,772个错误README文件，每个README文件恰好包含两个注入的错误。

自动验证 随后我们自动验证所注入错误的有效性。如果满足以下条件，则认为注入的错误有效：(i) 遵循错误README文件后，环境配置失败；(ii) 修复该错误后，配置过程能够通过受影响的步骤。对于每个错误README文件，我们使用gpt-4.1-mini生成一个shell脚本并在提供的Docker环境中执行（详见附录A.4）。如果脚本成功构建环境并通过测试，则对应的错误被视为无效。我们有意在此阶段避免使用更强的模型，因为它们可能会隐式地“自动修复”，从而生成与错误README文件不一致的脚本，进而破坏对错误有效性的验证。

大语言模型辅助筛选与人工验证 自动验证无法保证注入的错误是有效的，因此我们进行第二轮验证以确保：(a) 错误确实影响配置；(b) 错误在README文件中明确呈现；(c) 分类和修复方案是正确的。为减少人工工作量，我们使用gpt-4.1-mini根据预定义的标准（详见附录A.5）进行评估。未能通过筛选的项目将被移除。人工评估者随后在同一标准下审查剩余数据。大语言模型筛选与人工判断之间的一致性达到98.5%，证明了该流程的可行性。我们最终保留了1,230个有效的错误README文件，每个包含两个错误。为增加难度多样性，我们进一步拆分和合并错误，以构建包含1-10+个错误的README文件，最终生成4,201个README文件和9,471个总错误数。

(a) 每个代码库的README文件统计

(b) 难度级别分布

3.3 数据集统计与数据评估

(c) 错误类型分布

图4. 数据统计结果。

数据统计。如第3.2节所述，我们完成了基准测试的构建。从323个代码库中，我们构建了4,201个README文件，平均每个代码库约13个，分布如图4(a)所示。我们进一步按每个README文件注入的错误数量对难度进行分层，定义1-10级，如图4(b)所示。大多数README文件处于1级或2级，这与现实实践相符：README文件通常包含1-2个阻碍环境配置的问题，但很少有更多的问题。这确保了任务的难度对智能体来说保持适中。最后，在图4(c)的错误类型分布中，五个标准错误类别的数量相近，每个约1,600个实例，从而构成了一个平衡的数据集。“其他”类别仅包含312个实例，既保留了覆盖完整性，又防止智能体过度使用这个catch-all类别。与表1中的其他基准测试相比，我们的基准测试在评估智能体的环境配置能力方面表现出显著优势。

表1. 环境配置基准测试对比。

基准测试

实例数

指标

过程级

INSTALLAMATIC [Milliken等，2025]

40

成功构建

✗

EXECUTIONAGENT [Bouzenia和Pradel，2025]

50

成功构建与测试

✗

EnvBench [Eliseeva等]

994

成功构建与测试、

✗

缺失导入

SetupBench [Vergopoulos等，2025]

93

成功构建与测试、

✗

错误检测与修复

EnConda-Bench

4,201

成功构建与测试、

✓

错误检测与修复

数据评估。由于我们的任务实例是使用大语言模型生成的，这些生成的错误可能无法完全反映真实世界的任务条件，因此我们进一步验证了数据质量。尽管如此，我们旨在进一步验证生成的数据是否与现实世界难度相符。

现实世界环境配置任务的难度水平，并反映人类认知模式。为此，我们选取现有的环境配置基准测试集，其实例直接来源于真实代码仓库，并建立了一套标准来评估这些基准测试集及我们任务的难度等级。难度由人类专家按1（非常简单）到5（非常困难）的量表进行评分（详见附录B）。表2中的结果表明，我们任务的难度分布和平均分与真实世界实例高度吻合，证明我们的数据集具有实际适用性和高质量。

表2展示了各基准测试集的难度评分，其中简单/中等/困难分别对应1-2/3/4-5分。对于EnvBench和EnConda-Bench，我们各采样了100个任务实例。

基准测试集 | 实例数 | 平均分 | 简单 | 中等 | 困难
INSTALLAMATIC [Milliken等，2025] | 40 | 3.92 | 12 | 21 | 7
EXECUTIONAGENT [Bouzenia和Pradel，2025] | 50 | 3.85 | 16 | 26 | 8
EnvBench [Eliseeva等] | 100 | 4.08 | 27 | 46 | 27
SetupBench [Vergopoulos等，2025] | 93 | 3.78 | 34 | 47 | 12
EnConda-Bench | 100 | 3.95 | 30 | 47 | 23

评估套件设计

在验证基准测试实例后，我们构建了一个用于环境配置智能体的评估套件。给定README和仓库信息，智能体会进行规划并执行，产生一条轨迹，从中提取感知（错误诊断）、反馈（修复）以及用于规划和行动的最终Shell脚本。由于一个README可能包含多个错误，我们将智能体预测的错误类型/描述与标准答案进行对比，并报告精确率、召回率和F1值。随后，我们将每个预测的错误描述和修复方案与标准答案进行匹配，并使用GPT-4.1-mini作为评判器来评估一致性并评估准确性。对于可执行性，每个脚本在固定提交的Docker容器中运行。只有当环境成功构建、测试文件正确执行且进程正常退出时，才计为通过。此外，我们还提出了一个整体的数据合成框架，可自动从仓库生成和验证任务实例，运行智能体收集轨迹，并生成评估结果以获取训练后的乃至训练前的轨迹数据（更多信息见附录C）。

以下是该段落的学术中文翻译：

---

当前仍处于探索阶段，ACC.分数为33.8，Pass@1分数为9.1。环境配置代理实现了最大的端到端增益，表现出更强的感知与反馈利用能力以及执行动作能力。我们观察到，Repo2Run + Claude-4达到F1分数60.6、描述准确率52.2、修复准确率47.3以及Pass@1分数22.9，凸显了环境探测感知与失败处理反馈的价值。尽管如此，描述准确率与修复准确率之间、以及修复准确率与Pass@1之间的持续差距揭示了代理在将正确反馈转化为稳健有效的执行动作方面的瓶颈。因此，我们对代理轨迹的过程级评估至关重要，这种评估能够指导针对性的改进，而非将所有失败笼统归入通过率，这进一步表明代理的规划过程需要优化，我们需要更好地利用从与环境交互中获取的反馈信息，以真正提升执行能力。

4.3 错误类型判断分析

对于每种错误类型，我们观察到图5所示的预测错误总数超过真实标签数量，表明采用了更为保守且严格的检查策略。对特定错误类型的敏感度不均：大多数模型倾向于过度预测E1类别。同时存在模型特定的差异。例如，DeepSeek-V3预测的E6案例极少，甚至少于真实标签数量，表明对该错误类型存在欠检测问题。最后，许多案例被归入“其他”这一综合类别，使得E8成为最高或次高类别。这并不理想，因为用户期望获得精确且可操作的诊断，而非模糊的分类。与上述一致，过度使用“其他”导致E8的F1分数显著降低，如图6所示。许多本应属于具体类型的实例被错误分配给E8，增加了E8的误报率，并压低了真实类型的召回率。这种保守行为削弱了错误感知与反馈的实际价值，损害了下游的规划与动作执行。除此之外，模型在特定类型上表现出细微但系统性的性能差异。例如，结果显示对命令使用和语法错误E2的检测能力较强，但对文件路径错误E4等类别的检测较弱，而这些类别往往依赖于代理对整个仓库的系统级理解以及与环境的交互能力。

E1 E2 E4 E6 E7 (a) 零样本 E8

E1 E2 E4 E6 E7 (b) Swe-Agent E8

E1 E2 E4 E6 E7 (c) 

---

**译注：** 文中"E1"至"E8"为原始论文中的错误类型分类编号，保留不变；"ACC."为准确率（Accuracy），"Pass@1"为首次通过率，均为评估指标的标准表述。

# 翻译

## 4.4 效率分析

我们研究了输出token数量与性能之间的关系，以期对模型效率进行全面评估。如图7所示，对于错误描述的准确率，大多数模型随着输出token数量的增加呈现出明显的上升趋势。相比之下，对于Pass@1指标，分配更多的token并不一定会带来持续的性能提升。例如，零样本Claude-4使用的token数量是零样本DeepSeek-V3的三倍，但性能仅提升约0.2。值得注意的是，在某些智能体框架中（如Repo2Run），性能随着token预算的增加呈现更优的扩展性，这表明其具有相对较高的效率。

图7. 输出token与模型性能关系的统计示意图。

## 4.5 案例研究

除上述分析外，我们进一步探讨了具体案例，以验证我们框架的有效性并支撑我们的结论。如图8所示，我们观察到模型有时能够正确判断错误类型，却未能准确定位错误命令，这会影响后续的反馈效果。我们还发现，某些提出的修复方案与标准答案存在差异，但我们的评估协议能够容纳这种可变性，侧重于问题是否得到解决而非精确匹配，从而支持了我们方法论的合理性。此外，我们观察到通过率相对较低。一个关键原因是单个README可能包含多个错误，而模型往往难以全部修复。此外，在执行过程中，模型可能正确诊断出错误并提出适当的修复命令，但未能将其应用于环境设置所使用的shell脚本中，或者可能引入新的错误。值得注意的是，这类错误很可能在多轮智能体迭代过程中产生，因为大量的编辑操作会引入额外的错误。

## 5 结论

环境配置仍然是SWE智能体的决定性瓶颈。除了端到端基准测试之外，我们还引入了一个侧重于过程级评估的基准测试，包括规划、感知驱动的诊断、反馈驱动的修复以及最终动作。通过向README中注入真实错误并在Docker中验证其效果，我们提出的自动化数据框架生成了可扩展的高质量任务数据集。

# 中文翻译

**环境设置：**

```
v env' 和 'source env/bin/activate'，然后运行 'pip install -r requirements_debug.txt'。
Set_up.sh: ……# 5. 创建并激活虚拟环境。 / source env/bin/activate / # 6. 升级 pip。 / pip install --upgrade pip / # 7. 安装 Python 依赖项。 / pip install -r requirements.txt
```

**图 8.** 我们选取了一些典型案例，并提供了对所观察现象的分析。

我们在高级智能体上进行了实验，发现其在错误感知方面表现出一定的能力，但倾向于将不确定的错误类型归类为“其他”。然而，在具体的修复操作方面，智能体的表现有限。我们将此局限归因于智能体缺乏有效的交互和反馈机制。尽管它能够识别错误，但在规划反馈信息以及与真实世界环境交互以提供更好的修复操作方面存在困难。未来，增强智能体与环境交互的能力将是一个重要的研究方向。

**伦理声明**

我们引入了一个新颖的基准测试 EnConda-Bench ，其中包含了存储库收集、错误合成、数据验证和过滤的详细描述。我们强调，该数据集的创建严格遵循伦理准则。我们确保所有使用的存储库均符合其各自的许可协议。在数据集的构建过程中，我们高度重视伦理标准的维护，采用匿名化、脱敏和数据清理等措施。这些样本不会对公众福利造成任何风险。对于从这些网站获取的所有数据，我们均已获得数据使用许可。因此，所提出的创新研究方向和任务在伦理上对社会无害。

**参考文献**

[1] Junda He, Christoph Treude, and David Lo. Llm-based multi-agent systems for software engineering: Literature review, vision, and the road ahead. ACM Transactions on Software Engineering and Methodology, 34(5):1–30, 2025.

[2] Xingyao Wang, Boxuan Li, Yufan Song, Frank F Xu, Xiangru Tang, Mingchen Zhuge, Jiayi Pan, Yueqi Song, Bowen Li, Jaskirat Singh, et al. Opendevin: An open platform for ai software developers as generalist agents. arXiv preprint arXiv:2407.16741, 3, 2024.

[3] Angela Fan, Beliz Gokkaya, Mark Harman, Mitya Lyubarskiy, Shubho Sengupta, Shin Yoo, and Jie M Zhang. Large language models for software engineering: Survey and open problems. In 2023 IEEE/ACM International Conference on Software Engineering: Future of Software Engineering (ICSE-FoSE), pages 31–53. IEEE, 2023.

[4] Xingyao Wang, Boxuan Li, Yufan Song, Frank F Xu, Xiangru Tang, Mingchen Zhuge, Jiayi Pan, Yueqi Song, Bowen Li, Jaskirat Singh, et al. Openhands: An open platform for ai software developers as generalist agents. In The Thirteenth International Conference on Learning Representations.

[5] Yuntong Zhang, Haifeng Ruan, Zhiyu Fan, and Abhik Roychoudhury. Autocoderover: Autonomous program improvement. In Proceedings of the 33rd ACM SIGSOFT International Symposium on Software Testing and Analysis, pages 1592–1604, 2024.

[6] Dan Hendrycks, Steven Basart, Saurav Kadavath, Mantas Mazeika, Akul Arora, Ethan Guo, Collin Burns, Samir Puranik, Horace He, Dawn Song, et al. Measuring coding challenge competence with apps. In Thirty-fifth Conference on Neural Information Processing Systems Datasets and Benchmarks Track (Round 2).

[7] Jacob Austin, Augustus Odena, Maxwell Nye, Maarten Bosma, Henryk Michalewski, David Dohan, Ellen Jiang, Carrie Cai, Michael Terry, Quoc Le, et al. Program synthesis with large language models. arXiv preprint arXiv:2108.07732, 2021.

[8] John Yang, Carlos E Jimenez, Alexander Wettig, Kilian Lieret, Shunyu Yao, Karthik Narasimhan, and Ofir Press. Swe-agent: Agent computer interfaces enable software engineering language models. arXiv preprint arXiv.2405.15793, 2024.

[9] Carlos E. Jimenez, John Yang, Alexander Wet

以下是相关学术参考文献的中文翻译：

**参考文献：**

[10] Aleksandra Eliseeva、Alexander Kovrigin、Ilia Kholkin、Egor Bogomolov 和 Yaroslav Zharov。Envbench：自动化环境配置基准。在 ICLR 2025 第三次深度学习代码研讨会。

[11] Louis Milliken、Sungmin Kang 和 Shin Yoo。超越 pip install：评估用于自动化 Python 项目安装的 LLM 智能体。在 2025 年 IEEE 软件分析、演进与再工程国际会议（SANER），第 1–11 页。IEEE，2025。

[12] Islem Bouzenia 和 Michael Pradel。你命名，我运行：用于执行任意项目测试的 LLM 智能体。ACM 软件工程汇刊，2(ISSTA)：1054–1076，2025。

[13] Konstantinos Vergopoulos、Mark Niklas Müller 和 Martin Vechev。面向仓库级编码任务的自动化基准生成。arXiv 预印本 arXiv:2503.07701，2025。

[14] Martin Gruber 和 Gordon Fraser。Flapy：大规模挖掘不稳定的 Python 测试。在 2023 年 IEEE/ACM 第 45 届软件工程国际会议附刊（ICSE-Companion），第 127–131 页。IEEE，2023。

[15] Kechi Zhang、Jia Li、Ge Li、Xianjie Shi 和 Zhi Jin。Codeagent：通过工具集成的智能体系统增强代码生成，以应对真实世界的仓库级编码挑战。在第 62 届计算语言学协会年会论文集（第 1 卷：长论文），第 13643–13658 页，2024。

[16] John Yang、Kilian Lieret、Carlos E Jimenez、Alexander Wettig、Kabir Khandpur、Yanzhe Zhang、Binyuan Hui、Ofir Press、Ludwig Schmidt 和 Diyi Yang。SWE-smith：软件工程智能体的规模化数据构建。arXiv 预印本 arXiv:2504.21798，2025。

[17] Chunqiu Steven Xia、Yinlin Deng、Soren Dunn 和 Lingming Zhang。Agentless：揭示基于 LLM 的软件工程智能体的本质。arXiv 预印本 arXiv:2407.01489，2024。

[18] Ruida Hu、Chao Peng、Xinchen Wang 和 Cuiyun Gao。面向可靠 Docker 环境配置的 LLM 智能体。arXiv 预印本 arXiv:2502.13681，2025。

[19] Mark Chen、Jerry Tworek、Heewoo Jun、Qiming Yuan、Henrique Ponde De Oliveira Pinto、Jared Kaplan、Harri Edwards、Yuri Burda、Nicholas Joseph、Greg Brockman 等。评估代码训练的大型语言模型。arXiv 预印本 arXiv:2107.03374，2021。

[20] Naman Jain、King Han、Alex Gu、Wen-Ding Li、Fanjia Yan、Tianjun Zhang、Sida Wang、Armando Solar-Lezama、Koushik Sen 和 Ion Stoica。Livecodebench：大型语言模型的全面且无污染评估。在第十三届国际学习表征会议。

[21] Tianyang Liu、Canwen Xu 和 Julian J. McAuley。Repobench：仓库级代码自动补全系统的基准测试。在第十二届国际学习表征会议（ICLR 2024），奥地利维也纳，2024 年 5 月 7–11 日。OpenReview.net，2024。

[22] Naman Jain、Manish Shetty、Tianjun Zhang、King Han、Koushik Sen 和 Ion Stoica。R2E：将任意 GitHub 仓库转化为编程智能体环境。在第四十一届国际机器学习会议（ICML 2024），奥地利维也纳，2024 年 7 月 21–27 日。OpenReview.net，2024。

[23] Aixin Liu、Bei Feng、Bing Xue、Bingxuan Wang、Bochao Wu、Chengda Lu、Chenggang Zhao、Chengqi Deng、Chenyu Zhang、Chong Ruan 等。Deepseek-v3 技术报告。arXiv 预印本 arXiv:2412.19437，2024。

---

**附录 A**

**数据构建**

**A.1 仓库选择**

除第 3.2 节所述的筛选标准外，我们主要从现有的、经过人工验证的和预筛选的基准中选择仓库，以确保原始项目可构建。这一选择至关重要，因为我们的错误合成过程依赖于能够成功编译和运行的目标代码。

该流程将未经修改的README文件作为基准真值；因此，README文件必须正确且可执行。为此，我们聘请了专业标注人员执行手动环境配置，剔除了那些README本身存在错误或依赖项不完整的代码库，从而保留了高质量的基线数据。我们进一步验证了所有选定的代码库均持有允许非限制性研究使用的许可证：大多数采用宽松许可证（如BSD、MIT、Apache），其余则采用著佐权许可证（如GPL），这些许可证与我们预期的研究场景相兼容。我们还审查了采用自定义许可证的代码库，并确认其适用于本研究设想的用途。未来研究的一个有前景的方向是扩大代码库覆盖范围，纳入用更广泛编程语言编写的代码库，并将评估扩展到更多语言生态系统中的环境配置任务。

A.2

错误类型定义

常见环境配置错误分类法

根据从软件仓库配置执行环境时频繁遇到的故障模式，我们定义了一个六类分类法，旨在覆盖绝大多数实际问题，同时保持类别之间清晰、可操作的边界。该方案旨在支持后续的错误生成，且该集合对于常规评估和错误综合来说是完整的。

表4. 代码库环境配置错误分类法。标识符保留自我们内部的模式设计，且故意不连续。

| ID | 名称 | 定义 | 示例 |
|---|---|---|---|
| E1 | 依赖项安装错误 | 与系统或Python依赖项安装步骤相关的错误，包括缺失依赖项、不必要依赖项或版本错误。 | • E1典型症状包括包未找到错误（如索引或渠道返回404）、缺失系统库（如OpenSSL、GCC工具链）或安装程序失败（apt/conda/pip）。 |
| E2 | 命令使用或语法错误 | 由不正确命令、无效参数或不当语法导致的执行失败错误。 | • E2通常表现为立即终止并显示用法消息或退出代码2、无效或已弃用的标志、shell引号/转义错误，或从错误的工作目录调用命令。 |
| E4 | 文件路径或文件缺失错误 | 依赖项文件路径不正确或引用的文件不存在的错误。 | • E4常见信号包括"无此文件或目录"、文件名拼写错误、不正确的相对路径，或依赖未被纳入版本控制的产物。 |
| E6 | 逻辑顺序错误 | 由安装步骤执行顺序不正确导致的错误，例如在创建虚拟环境之前安装pip依赖项。 | • E6症状包括安装到未激活的环境中、使用前未执行激活脚本，或在安装工具链之前尝试构建。 |
| E7 | 版本兼容性错误 | 由未指定的Python或依赖项版本、版本冲突或不兼容导致的错误。 | • E7表现为求解器冲突、运行时ImportError（由于ABI/GLIBC/CUDA版本不匹配），或跨Python或库次要版本的行为细微差异。 |
| E8 | 其他杂项错误 | 超出上述类别的其他未分类错误，如混乱格式、关键说明缺失或描述不清晰。 | • E8用于处理上述范围之外的问题，如不完整或模糊的指令、不一致的命名，或掩盖必要步骤的多余格式；标注人员应谨慎使用此类别，并在可行时优先使用具体类别。 |

A.3

错误生成

我们同时使用Claude-4-sonnet和Gemini 2.5-pro来生成错误。我们输入原始README文件、要插入的错误类型、每个README的错误数量、期望输出的README文件数量，以及错误类型定义列表，然后让它们输出以下内容

EnConda-Bench

• 错误的 README（Markdown 或 RST 文件）
• 列表（JSON 格式）：
– README id
– 错误类型
– 错误描述（自然语言）
– 候选修复建议（操作提示）
– 修复答案的真实值（标准答案）

以下是错误生成的指令：
错误合成提示词
您是一位专业的环境设置工程师和 README 文本修改人员。
您将收到以下输入：
- 一个位于路径 {readme_path} 的正确 README markdown 文件。
- 要注入的错误类型列表，从以下选项中选择：
{error_types_str}
- 每个 README 要插入的错误数量：{errors_per_readme}。
- 要输出的不同错误 README markdown 文件数量：{num_readmes}。

任务：
请通过最小化修改原始 README 来生成 {num_readmes} 个不同的错误 README markdown 文件。尽可能保持原始 README 不变，仅通过更改或添加极少数量的句子（通常 1 或 2 个）来引入所需的错误类型。

对于每个生成的错误 README 文件，请输出以下部分：
1. 完整的错误 README markdown 文本，除最小化注入的错误外，保留所有原始格式和内容。
2. 描述所有插入错误的 JSON 元数据块，结构如下：
```json
{
"readme": "readme_{{index}}",
"errors": [
{
"error_type": "<错误类型代码，例如 E1>",
"error_description": "<对该 README 中引入的错误的简要描述>",
"correction_candidates": [
"<候选修复方案 #1>",
"<候选修复方案 #2>"
],
"golden_answer": "<修复该错误的精确修正>"
}
]
}
```
请按此格式输出所有 {num_readmes} 个文件的结果，并用恰好三个破折号（---）分隔：

---

A.4

自动验证

在生成错误的 README 文件后，按照正式评估流程，我们使用 GPT-4.1-mini 根据给定的 README 和仓库的目录结构生成一个 shell 脚本。该脚本随后在 Docker 中运行，以验证环境是否可以成功构建。我们指示模型在生成脚本时严格遵循 README 中的说明，不做任何修改或更正，以确保评估结果的准确性。具体使用的提示词如下：
验证用 Shell 脚本生成提示词
您将获得一个 Python 项目的 README 和该仓库的目录结构。请仅输出一个完整的 bash shell 脚本，用于在 Ubuntu 22.04 上自动为此项目设置环境。请勿包含任何解释或 markdown，仅输出脚本内容。
脚本要求：
- 使用 Miniconda 在项目目录中创建并激活新的 Python 虚拟环境（例如，命名为 ./env 或 ./venv）。
- 安装 README 中推断出的任何必需系统包和 Python 包。
- 安装 Python 依赖项（从 README 推断；如果存在 requirements.txt/pyproject.toml，请同时处理）。
- 如适用，以可编辑模式安装项目（pip install -e .）。
- 您必须运行测试套件来验证设置（pytest 或任何指示/推断出的测试工具）。
- 以 shebang（#!/usr/bin/env bash）开头，并使用 'set -euo pipefail'。

---

验证用 Shell 脚本生成提示词
注意：您无需确保生成的 Shell 脚本一定能够成功配置，但请**确保 Shell 脚本的内容与 README 中的内容完全一致**。

## A.5 LLM作为评判者进行验证

我们进一步使用GPT-4.1-mini对每个错误进行注释，以确保最终生成的错误是有效的。我们输入错误的README文件（包含错误注释的JSON文件）、错误定义以及用于我们环境配置的基本Dockerfile设计。我们请GPT-4.1-mini检查以下内容：（1）错误类型分类是否准确？如果不准确，请建议更正后的类型。（2）该错误是否在README中有描述？（3）该错误是否有效？我们仅在错误确实阻止环境配置中的某一步成功执行、且其修复方案允许配置正确执行时，才认为该错误有效。（4）该错误的标准解决方案是否正确？经过此筛选后，我们将所有与无效错误对应的README丢弃。具体提示如下：

**用于LLM作为评判者筛选有效错误README的提示**

SYSTEM_PROMPT
您是环境设置检查器。仅输出N行精简的JSON对象，每个错误一行，严格匹配下方架构。不要包含解释、散文、代码块、空行或尾随逗号。

DEVELOPER_PROMPT
任务：
对于errors_json.errors中的每个错误（按顺序），请决定：
- error_type_judgment：error_type是否与错误描述匹配（根据定义）？如果为false，请将error_type_modify设置为单个最佳匹配类型；否则为""。
- error_readme_cr：README文本是否实际包含错误内容（错误的命令/标志/版本/路径/模块、错误的虚拟环境顺序）或成功所需的可忽略项？
- answer_judgment：golden_answer是否会消除此错误的根本原因？
- error_valid：在给定的环境假设和成功标准下，此错误是否会在修复前破坏设置（安装命令错误、依赖解析冲突、必需库的ImportError、由于虚拟环境误用导致的环境不可用）？对于装饰性/可选问题或不确定的情况，请返回false。

仅使用：
- readme_text
- error_definitions
- errors_json
- 环境假设/成功标准

如果上下文缺失，请保持保守。请保留输入顺序。
输出架构（每个错误一行）
{"id":"<readme_id>#<zero_based_index>",
"error_type_judgment":true|false,"error_type_modify":"E? or empty
string",
"error_readme_cr":true|false,"answer_judgment":true|false,"error_valid
":true|false}

---

## 用于LLM作为评判者筛选有效错误README的提示

规则
- error_type_judgment：与提供的定义进行严格比较；如果多个类型符合，请选择最具体的根本原因类别。
- error_readme_cr：仅在README证实了错误时才为true；对于可忽略项，仅在缺失的步骤/依赖明显是成功所必需时才为true。
- answer_judgment：golden_answer必须直接修复错误；仅存在于correction_candidates中是不够的。
- error_valid：对于无效的标志/选项、不兼容/被阻止的依赖项声明、缺失的必需依赖项、错误的命令/路径/模块、严重的虚拟环境误用/顺序问题，应为true；对于风格建议或推测性失败，应为false。

预期模型输出（每个错误一行，按顺序）：
{"id":"<readme_id>#0","error_type_judgment":true,
"error_type_modify":"","error_readme_cr":true,"answer_judgment":
true,"error_valid":true}

---

## B 难度评级

由于我们的任务实例使用LLM将错误注入到现有的README文件中，我们希望验证这些生成的任务实例是否反映了现实世界环境配置中遇到的挑战，从而具有与现实世界环境设置任务相似的特征。因此，我们选择了几个直接从现实世界代码仓库收集任务实例的基准测试，包括INSTALLAMATIC Bench、ExecutionAgent Bench、EnvBench和SetupBench，并将它们的难度评分与我们的基准测试进行了比较。对于EnvBench和我们的基准测试，我们各抽取了100个任务实例；对于其他基准测试，我们对所有可用的任务实例进行了评分。

通过分析难度分数的分布，我们可以评估基准测试中所采用的方法是否与实际任务实例相一致。具体而言，我们采用了1-5分的评分标准，其中1分表示非常容易，2分表示容易，3分表示中等，4分表示困难，5分表示非常困难。我们邀请了专业标注人员对所选定的任务实例进行评分，主要考虑以下因素：README文件中指令的清晰度和完整性、命令是否可以直接执行、是否需要查阅额外的文件或页面，以及需要考虑的依赖项数量。

**轨迹训练数据生成框架**

我们已实现了环境配置任务实例生成的自动化，并设计了一套全面的评估套件，使智能体能够执行这些任务实例、捕获其执行轨迹并进行评估。因此，我们可以基于此构建一个完整的合成数据框架，用于生成代表智能体成功和失败执行环境配置任务的合成轨迹数据。只要拥有足够数量的高质量原始仓库数据，便可以高效地生成大量用于模型微调或大规模预训练的轨迹数据。具体的数据生成过程如图9所示。

**实验设置**

当智能体被赋予配置环境的任务时，我们提供的指令包括仓库目录信息、README信息以及基本环境需求。我们还为智能体规划了一个可行的工作流程，以确保整个环境配置符合指定标准。我们要求智能体明确识别执行过程中出现的任何错误，并在完成环境配置后执行单元测试以验证其成功。具体提示如下：

**智能体执行环境配置的提示**

您是一位专业的Python环境配置助手。您的任务是分析README文件，检测环境设置指令中的潜在错误，并提供全面的解决方案。

给定一个README文件，您应该：

1. **错误检测与分析**：仔细分析README中环境设置指令的潜在错误，包括：
   - E1：依赖安装错误（缺失依赖、不必要的依赖或版本错误）
   - E2：命令使用或语法错误（命令不正确、参数无效或语法不当）
   - E4：文件路径或缺失文件错误（依赖文件路径不正确或引用的文件不存在）
   - E6：逻辑顺序错误（安装步骤的执行顺序不正确）
   - E7：版本兼容性问题（未指定的Python或依赖版本、版本冲突或不兼容）
   - E8：其他各类错误（格式混乱、关键说明缺失或描述不清楚）

2. **错误分析输出**：...

```json
{
  "detected_errors": [
    {
      "error_type": "E1",
      "error_description": "Spelling error: 'generalpurpose' should be two separate words 'general purpose'",
      "fix_suggestion": "Add a space between 'general' and 'purpose' to correct 'generalpurpose' to 'general purpose'"
    },
    {
      "error_type": "E8",
      "error_description": "Formatting error: The number '21' appears at the end of the paragraph, which appears to be a misplaced page number or section indicator",
      "fix_suggestion": "Remove '21' from the end of the paragraph as it disrupts the flow of the disclosure statement"
    },
    {
      "error_type": "E8",
      "error_description": "Formatting error: The letter 'E' appears at the very end of the text as a standalone character, which seems to be an artifact or error",
      "fix_suggestion": "Remove the trailing 'E' character from the end of the text"
    }
  ]
}
```

```bash
#!/bin/bash

# ==============================================================================
# Comprehensive Python Environment Setup Script
# ==============================================================================

set -e  # Exit on any error

# Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Function to print colored output
log_info() {
    echo -e "${GREEN}[INFO]${NC} $1"
}

log_warn() {
    echo -e "${YELLOW}[WARN]${NC} $1"
}

log_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

# ==============================================================================
# Step 1: Examine Repository Structure
# ==============================================================================
log_info "Examining repository structure..."

if [ -d ".git" ]; then
    log_info "Git repository detected"
else
    log_warn "Not a git repository"
fi

# List repository contents
echo "Repository contents:"
ls -la

# Locate dependency files
log_info "Locating dependency files..."
DEPMGR="none"

if [ -f "requirements.txt" ]; then
    log_info "Found requirements.txt"
    cat requirements.txt
    DEPMGR="pip"
elif [ -f "pyproject.toml" ]; then
    log_info "Found pyproject.toml"
    cat pyproject.toml
    DEPMGR="poetry"
elif [ -f "setup.py" ]; then
    log_info "Found setup.py"
    cat setup.py
    DEPMGR="setuptools"
elif [ -f "environment.yml" ]; then
    log_info "Found environment.yml"
    cat environment.yml
    DEPMGR="conda"
else
    log_warn "No standard dependency file found"
fi

# ==============================================================================
# Step 2: Check and Set Python Version with pyenv
# ==============================================================================
log_info "Checking Python version requirements..."

# Check for version specification in files
PYTHON_VERSION="3.11"  # Default version

if [ -f "pyproject.toml" ]; then
    SPECIFIED_VERSION=$(grep -A5 "\[tool.poetry.dependencies\]" pyproject.toml | grep "python" | grep -oP '">=\K[^"]+' | head -1)
    if [ -n "$SPECIFIED_VERSION" ]; then
        PYTHON_VERSION="$SPECIFIED_VERSION"
    fi
elif [ -f "setup.py" ]; then
    SPECIFIED_VERSION=$(grep "python_requires" setup.py | grep -oP '">=\K[^<]+' | head -1)
    if [ -n "$SPECIFIED_VERSION" ]; then
        PYTHON_VERSION="$SPECIFIED_VERSION"
    fi
fi

log_info "Required Python version: $PYTHON_VERSION"

# Install and use pyenv if not available
if ! command -v pyenv &> /dev/null; then
    log_info "Installing pyenv..."
    curl https://pyenv.run | bash
    
    # Add pyenv to PATH
    export PYENV_ROOT="$HOME/.pyenv"
    export PATH="$PYENV_ROOT/bin:$PATH"
    eval "$(pyenv init -)"
    eval "$(pyenv virtualenv-init -)"
fi

# Install Python version if not installed
if ! pyenv versions | grep -q "$PYTHON_VERSION"; then
    log_info "Installing Python $PYTHON_VERSION..."
    pyenv install "$PYTHON_VERSION"
fi

pyenv local "$PYTHON_VERSION"
log_info "Using Python $(python --version)"

# ==============================================================================
# Step 3: Install System-Level Dependencies
# ==============================================================================
log_info "Installing system-level dependencies..."

# Update package list
apt-get update -qq

# Install common dependencies
apt-get install -y \
    python3-dev \
    python3-pip \
    python3-venv \
    build-essential \
    libssl-dev \
    libffi-dev \
    libjpeg-dev \
    libxml2-dev \
    libxslt1-dev \
    zlib1g-dev \
    git \
    curl \
    wget || log_warn "Some system packages may require root privileges"

# ==============================================================================
# Step 4: Set Up Virtual Environment
# ==============================================================================
log_info "Setting up virtual environment..."

# Check if venv already exists
if [ -d "venv" ]; then
    log_warn "Virtual environment 'venv' already exists. Removing..."
    rm -rf venv
fi

# Create virtual environment
python -m venv venv

# Activate virtual environment
source venv/bin/activate

# Upgrade pip
pip install --upgrade pip

log_info "Virtual environment activated: $(which python)"

# ==============================================================================
# Step 5: Install Python Dependencies
# ==============================================================================
log_info "Installing Python dependencies using $DEPMGR..."

case "$DEPMGR" in
    "pip")
        pip install -r requirements.txt
        ;;
    "poetry")
        pip install poetry
        poetry config virtualenvs.create true
        poetry install
        ;;
    "setuptools")
        pip install -e .
        ;;
    "conda")
        conda env update -f environment.yml
        ;;
    "none")
        log_warn "No dependency manager detected. Installing basic packages..."
        pip install numpy pandas
        ;;
esac

# ==============================================================================
# Step 6: Verification Steps
# ==============================================================================
log_info "Verifying installation..."

# Check Python version
echo "Python version: $(python --version)"

# Check pip version
echo "pip version: $(pip --version)"

# List installed packages
echo "Installed packages:"
pip list

# Verify key packages if requirements.txt existed
if [ -f "requirements.txt" ]; then
    log_info "Verifying critical packages from requirements.txt..."
    while IFS= read -r package; do
        if [ -n "$package" ] && [ "${package:0:1}" != "#" ]; then
            PKG_NAME=$(echo "$package" | cut -d'=' -f1 | cut -d'<' -f1 | cut -d'>' -f1 | tr -d ' ')
            if pip show "$PKG_NAME" > /dev/null 2>&1; then
                log_info "✓ $PKG_NAME installed"
            else
                log_warn "✗ $PKG_NAME not found"
            fi
        fi
    done < requirements.txt
fi

# Test Python imports
log_info "Testing basic Python imports..."
python -c "import sys; print('Python executable:', sys.executable)"
python -c "import pip; print('pip available:', pip.__version__)"

log_info "=========================================="
log_info "Environment setup completed successfully!"
log_info "=========================================="

# Show activation command
echo ""
echo "To activate the virtual environment, run:"
echo "  source venv/bin/activate"
```

---

## 翻译 (Translation):

**大型语言模型（LLMs）的使用**

根据大型语言模型使用的官方政策，我们在稿件撰写过程中仅将LLMs用作语法检查和 minor wording refinement（轻微措辞润色）的通用辅助工具。所有LLMs建议的修改均经过作者的人工审阅，并由作者选择性采纳。我们的使用符合官方要求，并在此予以披露。

---

*注：原文中"generalpurpose"应修正为"general purpose"（两个词）；"21"和末尾的"E"应为排版或格式错误，已在错误分析中指出。*



---

## 论文 5

# MEnvAgent: Scalable Polyglot Environment Construction for Verifiable Software Engineering

**作者**: Chuanzhe Guo, Jingjing Wu, Sijun He, Yang Chen, Zhaoqi Kuang

**arXiv**: https://arxiv.org/abs/2601.22859

---

**语言识别 (Language Identification):** 该段落为**英文** (English)。

---

## 中文翻译

MEnvAgent：用于可验证软件工程的可扩展多语言环境构建框架

**摘要**

大型语言模型（LLM）代理在软件工程（SW E）领域的发展受到可验证数据集稀缺性的制约，这一瓶颈源于跨多种语言构建可执行环境的复杂性。为解决这一问题，我们提出了 MEnvAgent，一个用于自动化环境构建的多语言框架，可实现可验证任务实例的可扩展生成。MEnvAgent 采用多代理"规划-执行-验证"架构，能够自主解决构建失败问题，并创新性地集成了环境复用机制，通过增量修补历史环境来降低计算开销。在 MEnvBench（一个包含 10 种语言、1000 个任务的新基准测试）上的评估表明，MEnvAgent 优于基线方法，失败转成功（F2P）率提高了 8.6%，同时时间成本降低了 43%。此外，我们通过 MEnvAgent 构建了 MEnvData-SWE——迄今为止规模最大的开源多语言真实可验证 Docker 数据集，并配备了解决方案轨迹，能够在广泛的模型中实现 SWE 任务的一致性性能提升。我们的代码、基准测试和数据集均可在 GitHub 上获取。

**图 1.** 手动环境构建与 MEnvAgent（我们的方案）的对比。MEnvAgent 利用多代理协作实现自动化环境构建，并具有高效的环境复用机制。

（图片描述：流程图展示从仓库选择、基础镜像选择、环境设置、测试配置，到 MEnvAgent 的自动化构建流程，包括代理选择基础镜像、生成安装脚本、生成测试脚本、自动化构建、反馈重试、验证测试结果等环节。）

---

以及其变体（Jimenez et al., 2024; Yang et al., 2025c; Zan et al., 2025）已成为评估 LLM 编码能力的标准设置。在这些设置中，像 OpenHands（Wang et al., 2025b）和 SWE-Agent（Yang et al., 2024）这样的自主代理负责探索仓库、定位问题、生成补丁（Pull Request），并执行测试以验证解决方案。这种基于执行的验证不仅对评估至关重要，而且对新兴的训练范式（如可验证奖励强化学习（RLVR）（Wen et al., 2025））也具有重要意义。然而，这些方法的有效性受到可执行环境构建可扩展性的制约。因此，现有工作面临两难境地：基于静态代码指标的方法（Xie et al., 2025; Wei et al., 2025）虽然能够高效扩展，但只能提供近似的验证信号。

这段文字是**英文**。以下是其学术性的中文翻译：

---

手动构建（Pan et al., 2025）虽然能够保证质量，但仍然劳动密集且主要局限于Python。这为跨多种编程语言的可扩展、可验证支持留下了关键空白。

## 1. 引言

大型语言模型（LLMs）的快速发展显著推进了软件工程领域内仓库级代码修改任务的探索。真实世界的issue解决基准测试，如SWE-bench

为了弥补这一空白，我们引入了MEnvAgent，一个专为可扩展、多语言环境构建而设计的自动化框架（见图1）。我们的方法旨在解决该领域两个基本挑战：（1）复杂性。管理非标准仓库中的多样化依赖需要深入的专业知识。频繁的构建失败（例如，版本冲突、编译错误）和不一致的测试协议（例如，pytest或mvn test）往往导致成功率较低。（2）时间消耗。构建过程由于安装和编译步骤而固有的缓慢。此外，环境非常脆弱；单个错误通常需要代价高昂的“全新开始”重启，这为大规模数据扩展带来了难以承受的开销。

跨200个开源仓库的流式语言任务，共包含1,000个任务。
- 我们发布了MEnvData-SWE，这是迄今为止最大的开源多语言真实可验证Docker环境数据集（见表12）。通过MEnvAgent构建，该数据集使LLM在下游SWE任务上能够获得一致的性能提升。

## 2. 问题形式化

在本节中，我们定义可验证SWE数据集的环境构建任务。一个可验证的任务实例由两个核心组件组成：任务上下文和可执行环境。任务上下文从GitHub收集，包括仓库快照R以及相关的issue和pull request（PR）。从PR中，我们提取两种不同的代码更改：用于解决issue的逻辑修改的修复补丁（fix patch），以及用于验证修复的新测试用例的测试补丁（test patch）。设Rf ix表示将修复补丁应用于R后的仓库状态。

为了应对复杂性，我们设计了一种多智能体架构，采用迭代的“规划-执行-验证”闭环。在该闭环中，专门的智能体履行不同的职责，迭代地诊断并自主解决构建失败问题，以确保高成功率。为了解决时间消耗问题，我们提出了一种新的环境复用机制。该机制不是从头开始构建每个实例，而是检索相似的历史环境

**语言识别：该段落为英语。**

**翻译：**

它通过合成并执行增量环境补丁来适配目标仓库快照。这种方法避免了完全重建的巨大成本，从而提高了效率。

给定任务上下文，环境构建的目标是确定一个配置三元组（B, P, T）。其中，B表示基础镜像，P表示由一系列安装命令组成的构建过程，T表示测试配置，包括应用测试补丁和执行测试命令。构建的环境正式定义为S = δ(B, P)，其中δ表示转换函数。

当前的环境构建基准测试（Milliken et al., 2025; Eliseeva et al., 2025; Guo et al., 2025）受到语言覆盖范围狭窄、不可执行评估或质量保证不足等限制。为解决这些限制并严格评估我们的方法，我们构建了MEnvBench，这是一个包含10种编程语言、1000个任务的综合基准测试，具有严格的基于执行的评估和质量保证。广泛的评估表明，MEnvAgent在所有语言上均优于最先进的基线方法，将失败转通过率（F2P）提高了8.6%，同时将时间成本降低了43%。此外，我们利用MEnvAgent扩展可验证数据构建，生成了MEnvData-SWE，这是一个真实的可验证SWE训练数据集。通过在该数据集上合成的解决方案轨迹对开源模型进行微调，我们在下游SWE任务上获得了显著的性能提升，有效验证了MEnvAgent的实用性。

根本目标是可执行性。具体而言，构建的环境必须允许在应用修复补丁后使仓库状态Rfix通过测试（PASS）：
ε(Rfix, S, T) = 0

（1）

这里，ε(·) = 0表示测试成功通过。然而，仅有可执行性是不够的。为确保其作为可验证环境的有效性，我们强制执行失败转通过（F2P）标准：
ε(R, S, T) = 1 ∧ ε(Rfix, S, T) = 0

（2）

这种差异化结果确保环境准确重现特定问题（失败）并验证其解决（通过）。（详见附录B）。

本文的主要贡献如下：

- 我们提出了MEnvAgent，一个基于规划-执行-验证架构的多智能体环境构建框架，涵盖10种编程语言。值得注意的是，它引入了一种新的环境重用机制，显著降低了计算开销。

3. MEnvAgent设计

在本节中，我们介绍MEnvAgent的设计，如图2所示。我们详细阐述两个关键组件：旨在构建可执行环境并解决构建失败问题的多智能体架构，以及环境重用机制，该机制通过适配历史环境来加速这一过程（详见附录C）。

The provided text is written in **English**.

以下是中文翻译：

---

t MEnvBench，首个用于评估多语言可执行环境构建的综合基准。该基准涵盖10个主要类别。

---

**ICML 2026 提交与格式说明**

**环境复用机制**

输入：任务上下文
相似性搜索
仓库快照：./

可复用环境
是否找到？

版本：1.0
创建时间：2026.01.01
补丁：diff --git a/code.py

环境复用池
（成功环境）

是
（复用）

否
（从头构建）

环境补丁
智能体

MEnvAgent（规划-执行-验证）
1. 规划

仓库分析智能体
分析仓库及依赖

（验证与反馈）

（实现与动态修复）

基础操作系统：Ubuntu 22.04
依赖项：Setuptools
包管理器：Conda; uv; maven;
测试命令：Pytest –rA test.py

交互式修复循环
容器安装/执行
已准备环境

验证智能体
验证与错误归因

构建计划

环境设置脚本
与评估脚本

环境设置智能体
测试配置智能体
选择基础镜像并起草
起草测试脚本
环境设置脚本

3. 验证

2. 执行

（分析与计划制定）

错误→修复→重试

环境执行
智能体

评估脚本
验证结果

（低于阈值）

通过

失败

反馈循环（指导）

图2. MEnvAgent概述。（上）环境复用机制通过增量补丁检索并适配历史环境以减少开销。（下）规划-执行-验证循环，智能体自主起草脚本、交互式修复构建错误并诊断测试失败以指导迭代优化。

**3.1 多智能体架构**

错误（例如，缺失包或版本冲突）。如果安装成功完成，工作流进入验证阶段。然而，如果智能体在多次尝试后仍无法解决安装错误，进程将中止当前尝试并返回规划阶段以重新生成构建策略。

MEnvAgent的架构分为三个迭代阶段：规划、执行和验证。
规划阶段。该阶段由三个专业智能体协调制定环境蓝图。首先，仓库分析智能体探索目标仓库的文件结构和内容，生成其项目类型、依赖项要求和入口点的综合总结。该总结传递给下游智能体。随后，环境设置智能体确定最合适的基础镜像并生成完整的环境安装脚本（记为构建过程P），其中包含所有必要的安装命令。之后，测试配置智能体分析仓库结构以及拟议的安装脚本，以合成兼容的测试配置脚本（记为T），确保验证逻辑与环境设置相一致。

验证阶段。最后阶段验证构建环境S和测试配置T的正确性。验证智能体执行配置的测试并收集结果。如果测试通过，则认为环境构建成功。如果测试失败，验证智能体会分析错误日志并提供诊断信息，这些信息被反馈到规划阶段以进行下一轮迭代优化。

---

**注意**：原文开头部分（"t MEnvBench..."）似乎有字符丢失或格式问题，我已根据上下文进行了合理翻译。

**语言识别**：该段落为**英语**（English）。

---

**翻译（中文）：

执行阶段。代理在容器内执行测试T中定义的测试。如果测试通过（满足ε(Rfix, S, T) = 0），则任务被视为成功。若验证失败，代理执行错误归因，以诊断故障是由缺失的环境依赖项还是错误的测试命令所致。此诊断反馈将传播回规划阶段，以指导代理的下一次迭代。最后，我们根据F2P标准（公式2）验证成功环境，以确认其作为可验证的软件工程（SWE）环境的有效性。

执行阶段。一旦计划确立，系统进入执行阶段。环境执行代理基于选定的镜像实例化容器，并执行计划P中的命令。关键在于，该代理能够实时监控终端输出，并动态调整命令以解决即时执行问题。

3.2 环境复用机制

为减少从原始基础镜像B推导和执行完整构建流程P的计算开销，我们引入了环境复用机制。我们将问题重新表述为：首先从环境池Spool中识别一个相似的现有环境状态Ssim，使其最小化预期适配工作Cadapt：

Ssim = arg min Cadapt(S, R) (公式3)
S∈Spool

一旦检索到Ssim，我们采用EnvPatchAgent生成增量命令序列ΔP，以将此环境适配到目标仓库快照R。形式上，我们寻求一个环境补丁ΔP = EnvPatchAgent(R, Ssim)，使检索到的环境转换到满足以下条件的新有效状态Snew：

ε(R, Snew, T) = 1 ∧ ε(Rfix, Snew, T) = 0 （公式4）
其中 Snew = δ(Ssim, ΔP)

我们的方法通过两个阶段执行此机制：环境检索和验证驱动适配。

4.1 数据收集与过滤

我们的数据采集管道包含两个阶段，将原始GitHub数据转化为高质量候选池。

第一阶段：仓库获取。我们针对10种主流编程语言的高质量仓库进行了筛选。为最大程度减少因固有代码缺陷导致的构建失败，我们采用了严格的标准：仓库必须满足（1）超过1,000个星标，（2）超过200个分支、问题、PR，（3）主要语言占比超过60%。该阶段生成了包含8,000个仓库的候选池。

环境检索。我们维护一个包含先前已验证环境的环境池Spool。为了近似公式3中的最优Ssim，我们采用了分层检索方法。



---

## 论文 6

# Spider2-V: How Far Are Multimodal Agents From Automating Data Science and Engineering Workflows?

**作者**: Ruisheng Cao, Fangyu Lei, Haoyuan Wu, Jixuan Chen, Yeqiao Fu

**arXiv**: https://arxiv.org/abs/2407.10956

---

这段文字是**英文**。以下是翻译：

---

# Spider2-V：多模态代理距离自动化数据科学与工程工作流还有多远？

**作者**

曹瑞升∗12 雷芳原 1 吴浩源 1 陈继轩 1 符业超 1 高洪成 1 
熊雪庄 1 张汉聪 2 毛雨辰 1 胡文静 1 谢天宝 1 徐洪升 2

张丹阳 12 王思达 3 孙若熙 3 尹鹏程 4 熊蔡明 5 倪安松 6 
刘倩 7 钟维克多 2 陈璐 2 余凯 2 于涛 1

**单位**

1 香港大学 2 上海交通大学 
3 谷歌云AI研究院 4 谷歌DeepMind 5 Salesforce研究院 
6 耶鲁大学 7 Sea AI Lab 8 滑铁卢大学

## 摘要

数据科学与工程工作流通常涵盖多个阶段，从数据仓库到编排过程，需要使用BigQuery、dbt和Airbyte等工具。随着视觉语言模型（VLM）在多模态理解和代码生成方面的进步，基于VLM的代理有望通过生成SQL查询、Python代码和图形用户界面（GUI）操作来自动化这些工作流。这种自动化可以提高专家的工作效率，同时使大规模数据分析更加普及。本文介绍了Spider2-V，这是首个专注于专业数据科学与工程工作流的多模态代理基准测试，包含494个真实世界的任务，涵盖真实计算机环境并整合了20个企业级专业应用。这些任务来源于真实用例，用于评估多模态代理在企业数据软件系统中通过编写代码和管理GUI来执行数据相关任务的能力。为了在真实模拟与简化评估之间取得平衡，我们投入了大量精力开发任务设置的自动配置，并为每个任务精心设计了评估指标。此外，我们还为这些企业数据软件系统提供了全面的文档支持。我们的实证评估表明，现有的基于LLM/VLM的最先进代理无法可靠地自动化完整的数据工作流（成功率仅为14.0%）。即使提供逐步指导，这些代理在需要细粒度、知识密集型GUI操作的任务（16.2%）以及涉及远程云托管工作空间的任务（10.6%）中仍然表现不佳。我们希望Spider2-V能为自主多模态代理改变数据科学与工程工作流自动化铺平道路。我们的代码和数据可在https://spider2-v.github.io获取。

## 1 引言

数据科学与工程流程通常依赖于专业的数据软件系统，如BigQuery、dbt和Airbyte，来获取、处理和编排大规模数据。使用这些企业系统需要编写SQL和Python代码，以及频繁且重复的图形用户界面（GUI）控制操作，这些操作即使对经验丰富的数据科学家和工程师来说也可能非常复杂。随着大型语言模型（LLM）和视觉语言模型（VLM）的快速发展，基于LLM/VLM的自主代理有潜

（原文在此处中断）

这段文字是**英文**。以下是翻译成的中文学术版本：

---

在香港大学实习期间完成的工作。

预印本。正在审稿中。

---

**任务1**：将当前Google Drive文件夹中的数据加载到已打开的BigQuery数据集中的新表"data1"中。
图形用户界面控制
在BigQuery界面上
（创建新表）

图形用户界面控制
在弹出面板上

图形用户界面控制
在Drive界面上

（填写信息）

（复制数据链接）

**任务2**：从Snowflake数据库IMDB中保存自2000年以来的前20部戏剧电影到桌面上的文件"top20movies.csv"，并遵循已打开的.txt文件中的详细要求。
图形用户界面控制
在Snowflake界面上
（创建新工作表）

跨应用图形用户界面控制
（重命名输出文件）

编写SQL

数据仓库

数据摄取

数据转换

数据可视化

数据编排

**图1**：Spider2-V是一个多模态智能体基准测试，涵盖完整的数据科学和工程工作流程（例如上图中的两个任务示例）。它涉及各种专业企业级应用程序，并在与可执行计算机环境的实时多轮交互中，除了代码编写外还包含大量的图形用户界面控制操作。

工作流程[37, 32]，提高了数据科学家和工程师的生产力[38, 16]，同时 democratizing 了对大规模数据的访问[15, 40]。

先前关于数据智能体的研究主要集中在通过生成代码或API调用[42, 9, 4]处理和分析日常生活数据，忽略了数据科学和工程的其他关键阶段（例如数据摄取和集成），以及使用企业应用程序（如Snowflake、Airflow和Dagster）。此外，为了完成数据工作流程，数据科学家和工程师通常需要导航多个专业数据系统，将代码编写与大量的图形用户界面控制相结合，例如浏览网页和点击按钮[5, 45]。然而，目前还没有一个同时整合代码生成和图形用户界面控制的专业数据科学和工程基准测试。

为了弥补这一空白，我们提出了Spider2-V，这是第一个涵盖整个数据科学和工程工作流程的多模态智能体基准测试，包含494个在实时可执行计算机环境中的真实世界任务，以及20个专业企业数据软件。Spider2-V旨在评估多模态智能体通过编写代码和管理企业数据软件系统中的图形用户界面来执行专业数据相关任务的能力，包括数据仓库（例如BigQuery）、数据摄取和集成（例如Airbyte）、数据转换（例如dbt）、数据分析和可视化（例如Superset），以及数据编排（例如Dagster）。这些任务来源于真实世界的实践，例如专业应用程序的官方教程和开源数据工程项目（如图1中呈现的两个任务示例）。我们还为检索增强型智能体提供了这些软件系统的官方文档和教程，以评估其从这些资源中泛化和学习的能力。

Spider2-V中的每个任务...

---

**注**：最后一句"Each task in Spider2-V i"似乎有截断，因此翻译以"..."结尾。

**语言识别：该段落为英语。**

---

**翻译（中文）：**

该系统定义于基于 OSWORLD [34] 的可执行计算环境之中，其允许多模态代理人在真实场景中模拟人类行为（例如输入代码或点击按钮）。具体而言，多模态代理人能够观察当前工作流程中专业数据应用的实时图像风格截图和文本风格可访问性树，并与计算机进行动态多轮交互以执行其预测的操作。该环境连接至真实互联网，使得需要真实用户账户的专业软件（如 Snowflake）得以集成使用。为确保使用该企业数据软件的可重复且可靠的实验，10 名具有计算机科学背景的作者共开发了 170 个自动任务配置和 151 个自定义评估指标。

我们针对最先进的闭源大语言模型和多模态大模型进行了实验，包括 GPT-4 系列 [21]、Gemini-Pro-1.5 [26]、Claude-3-Opus [2]、Qwen-Max [3] 以及开源代表模型 Mixtral-8x7B [11] 和 Llama-3-70B [20]。实验结果表明，即使是最顶级 的多模态大模型（GPT-4V [1]）也仅取得了 14.0% 的成功率。在最具挑战性的子任务中，当操作步骤超过 15 步时，性能下降至 1.2%。而对于开源大语言模型，成功率则不足 2%。这表明现有的大语言模型或多模态大模型距离实现完全的数据工作流程自动化仍有相当差距。即使提供了逐步的神谕（oracle）计划，整体性能也仅提升至 16.2%。这一发现揭示了多模态代理人在动作定位方面的能力不足（例如识别当前聚焦应用程序窗口中元素的精确坐标）。此外，我们对 Spider2-V 进行了广泛分析（第 4.3 节），结果表明以下策略显著提升了最终性能：增强不同观测模态之间的对齐、引入动作执行反馈、整合检索到的文档上下文以及扩大历史轨迹长度。这些发现为开发能够革新数据科学与工程工作流程自动化的实用多模态代理奠定了基础。

---

**Spider2-V 的可执行计算环境**

在本节中，我们介绍 Spider2-V 的实时可执行计算环境，该环境构建于虚拟机（VM）之上，并基于 OSWORLD [34] 进行了适配。

**2.1 任务定义**

通常，自主数据代理被建模为部分可观测马尔可夫决策过程（POMDP）。给定当前观测 ot ∈ O，其中包括自然语言指令以及截图、可访问性树（a11ytree）或它们的组合，代理生成一个可执行的动作 at ∈ A。该动作可以是点击屏幕上的某个像素位置（如 CLICK(560, 200)），或通过键盘输入代码（如 TYPE("ls -lh")）。执行 at 后产生新的状态 st+1 ∈ S（例如更新后的计算机状态）和新的部分观测 o

**语言识别**：该文本为**英语**（English）。

---

**中文翻译**：

t+1 ∈ O。ytree是一种文本风格的桌面环境表示形式，用于描述每个元素（例如窗口、按钮和输入框）的状态、位置和文本内容。交互循环持续执行，直到产生终止标记（DONE或FAIL）的操作，或智能体达到最大步数为止。更多关于观察空间和动作空间的详细信息见附录D。

**环境配置**

[图2：五种常见的环境重置操作]

为确保智能体从一致的初始状态开始，我们基于预存的虚拟机（VM）快照调用一系列函数来重置环境。这些函数调用因任务而异。我们将其功能归纳为5个通用类别（见图2），即：1）文件传输：将文件或项目归档（从本地或云存储）传输到虚拟机中；2）应用程序启动：在桌面上打开软件，例如Visual Studio Code和Chromium；3）远程API调用：调用特定工具的专业应用程序API，特别是那些需要真实用户账户的API，以重置和配置云工作空间；4）脚本执行：在虚拟机中执行shell脚本以设置初始状态，例如运行Docker容器以启动Superset的本地主机Web服务器；5）Playwright自动化：使用Playwright运行Web浏览器模拟，例如登录账户或点击特定按钮并重定向到目标网页。

**任务特定评估**

[图2：三种通用的任务评估方法]

交互终止后，我们只能访问计算机的开放性最终状态。因此，为衡量每个任务的目标是否达成，我们编写了任务特定函数以从开放性最终状态中检索期望结果并返回成功标志（0/1）。Spider2-V共包含170种初始状态配置。

**语言识别：该段落为英语（English）。**

---

**翻译（简体中文）：**

d 151 个评估脚本。相应地，我们将所有评估方法分为 3 个通用类别，如图 3 所示：

a) **基于文件的比较**：该方法从虚拟机中查找并复制目标文件到主机端，并借助文件类型相关的指标（如 .json、.csv 等）将生成文件与真实标签（ground truth）的指定方面进行比较。有时，真实标签可能会随时间更新。在这种情况下，我们会在评估期间从互联网获取最新的标签。

b) **基于信息的验证**：该方案通常用于从计算机中提取和检查所需信息。例如，在图 3(b) 中，我们希望确认 Airbyte 中数据调度的时间安排是否配置正确。我们可以调用 Airbyte API 来检索目标值，或使用 Chromium Playwright 来定位目标值。

c) **基于执行的验证**：为验证是否达成预期目标，我们可能还需要首先在最终虚拟机中执行复杂的 Shell 脚本。例如，在图 3(c) 中，我们手动触发目标 Airflow DAG 2，并通过运行日志检查最终状态。

### 3 基准测试构建

在本节中，我们介绍 Spider2-V 的一般标注流程、文档仓库构建和数据集统计。具体示例见附录 F。

#### 3.1 标注流程

为构建不同类别的任务，我们发现企业应用的官方教程是一个很好的起点。6 步标注流程如图 4(a) 所示，下面我们通过一个具体且真实的示例"使用 Airflow 和 Cosmos 编排 dbt Core 任务"3 进行详细说明：

1) **收集教程**：首先，我们从图 5 中每个专业工具的官方网站收集教程。共计 10 名标注者收集了 217 个源 URL。需要注意的是，这些教程可能使用其他专业软件，例如 MySQL。所有涉及的专业工具列于附录 B 中。

2) **学习教程**：标注者选择一个教程，在虚拟机中学习并实现它。之后，他们可以从该教程中总结关键知识点。例如，在图 4(b) 中，我们提取了将 dbt 项目集成到 Airflow 任务中的五个关键步骤。

3

3 在 Airflow 中，DAG 被定义为要运行的任务集合，DAG_ID 用于唯一标识它。

4 选定的 Airflow 教程 URL：https://www.astronomer.io/docs/learn/airflow-dbt

---

**抽象指令：**

𝒊) 配置 Astro 项目环境

𝒊𝒊) 准备 dbt 项目

𝒊𝒊𝒊) 创建 Airflow 连接

**1 收集教程**

𝒊𝒗) 编写 Airflow DAG

从 Airflow 选定的教程

用于构建任务

𝒗) 在 Web UI 上取消暂停 DAG

**2 学习教程**

2

**抽象表述：**
我想要……
此外，……

**详细表述：**
1. 打开 …
2. 输入 …
3. 点击 …

**3 编写指令**

**评估清单：**

```python
def get_results(...):
    pass
def compare(p, g):
    pass
```

```
airflow-proj/
dags/
```

**a**

**编写环境**

**4 设置函数**

**学习教程（并识别关键知识点）**



---

## 论文 7

# IDE-Bench: Evaluating Large Language Models as IDE Agents on Real-World Software Engineering Tasks

**作者**: Spencer Mateega, Jeff Yang, Tiana Costello, Shaurya Jadhav, Nicole Tian

**arXiv**: https://arxiv.org/abs/2601.20886

---

这段文字是**英文**。以下是其**中文翻译**：

---

IDE-Bench：基于真实软件工程任务评估大型语言模型作为IDE代理的表现

Spencer Mateega¹ Jeff Yang¹ Tiana Costello¹ Shaurya Jadhav¹ Nicole Tian¹ Agustin Garcinuño¹

arXiv:2601.20886v1 [cs.SE] 2026年1月28日

摘要

目前尚无基准测试能够通过工具调用来严格评估模型作为IDE代理在真实开发者所涉及的调试、重构、功能开发和全栈工作流等方面的表现（Kwa et al., 2025; Mündler et al., 2024; Zhuo et al., 2025; Jain et al., 2025）。

IDE-Bench是一个综合评估框架，通过IDE原生的工具接口来评估AI IDE代理在真实软件工程任务中的表现。我们提出了一个Docker化测试框架，该框架不仅限于原始终端执行，而是为模型提供了结构化的工具生态系统，模拟Cursor和Windsurf等AI原生IDE。通过提供代码库搜索、结构化文件编辑以及全栈应用测试的高级抽象，IDE-Bench评估代理作为真正工程协作者的能力。为确保评估并防止训练数据污染，我们在8个从未公开发布的仓库中创建了80个任务，涵盖C/C++、Java和MERN技术栈，代表了现代技术栈的生产场景，包括功能实现、错误修复、重构和性能优化，这些任务反映了私有代码库中开发者的日常工作流程。我们的基准测试首次在多语言、全栈环境中系统地将代理报告的意图与成功的项目级修改相关联，且代码完全未受污染。

在这项工作中，我们评估了能够充分发挥IDE代理能力的自主代理，包括工具调用和代码生成能力。我们将IDE代理定义为在基于聊天的IDE环境中运行的AI模型，可访问代理功能IDE（如Cursor）中可用的相同工具。我们提出了IDE-Bench，这是一个综合评估框架，用于在多样化技术栈上评估AI IDE代理在真实软件工程任务中的表现。IDE-Bench相比现有LLM基准测试具有优势，最显著的是工具调用能力。我们的主要贡献包括：

• **IDE原生结构化工具接口**：与SWEBench的静态上下文检索或Terminal-Bench的原始shell命令不同，IDE-Bench专为基于聊天的IDE代理设计，这是Cursor和Windsurf等工具使用的交互模型。它使用定义IDE代理范式的相同工具抽象（如读取文件、编辑文件、代码库搜索）来评估代理。基准测试包含条件工具访问，用于API端点测试和MongoDB查询验证（当任务需要MERN栈组件时），这些功能是当前基准测试所缺乏的。用户可以获取评估指标，例如完整对话轨迹、工具调用序列以及捕捉代理在IDE中探索、调试和错误恢复行为的行为指标。

---

**语言识别 (Language Identification):** 英文 (English)

---

**翻译 (Translation - Chinese):**

环境

一、引言

自 Cursor、GitHub Copilot 和其他具备代理能力的集成开发环境（IDE）兴起以来，软件工程工作流程已发生重大转变。工程师已将代理工具集成到日常任务中，减少了代码重复并提高了生产力。因此，具备代理能力的 IDE 得到了快速采用，改变了开发者编写和审查代码的方式（Coutinho et al., 2024; Peng et al., 2023）。GitHub Copilot 报告称截至 2025 年 7 月用户已超过 2000 万，Cursor 截至 2025 年 11 月付费客户已超过 36 万。尽管软件工程实践发生了这一转变，但仍然存在以下问题：

• 代表真实开发者工作流程的新型代码库：我们提供了八个从未在互联网、GitHub 或任何公开平台上发布过的新型代码库，确保免受训练数据污染——这是一个重要关切点，因为在线发布的基准任务可能会被未来模型版本所记忆。这些任务旨在呈现开发者在私有代码库中可能遇到的真实场景，例如根据规格说明实现用户请求的功能、调试复杂的多文件问题、重构遗留系统，以及在真实世界约束下优化性能。每个代码库都模拟了现实的生产环境场景，包括完整的依赖管理、集成测试需求、多语言技术栈以及开发者在实际软件项目中经常遇到的未明确规范的边缘情况。

算法在性能约束下的测试。该测试套件涵盖 Python、C、C++、JavaScript/TypeScript 和 Java。许多任务强制要求严格的输出格式规范，并处理仅在实际部署中才会出现的边缘情况，旨在测试代理满足生产规范而非通过人工单元测试的能力。所有代码库均保持未发布状态，以防止训练数据污染，确保模型在真正新颖的代码上进行评估。

每个任务目录包含一个 task description.txt 文件（包含问题陈述、说明和目标）、一个 task diff.txt 参考补丁，以及一个 task tests.py 评估文件。为维护评估完整性，我们在模型部署前从环境中移除 task diff.txt。评估通过位于代码库根目录的统一 run tests.sh 脚本执行，该脚本运行任务特定的测试，并根据脚本的退出代码返回成功状态。

二、相关工作

SWE-Bench 评估 LLM 导航复杂代码库的能力，来源于 GitHub 问题和新功能请求（PR），主要测试调试和小规模修正，而非……

这段文字是**英文**。以下是翻译：

大规模项目开发（Jimenez et al., 2024）。值得注意的是，SWE-Bench不提供对IDE环境的模型访问，而是以单次、静态范式评估模型性能，其中上下文检索仍是主要的性能瓶颈；SWE-Bench Verified通过移除无效任务实例并为静态检索补充系统调用工具来解决原论文中的挑战（Chowdhury et al., 2024）。SWE-Bench Pro扩展了SWE-Bench，纳入了企业代码库的专业级任务（Deng et al., 2025），并同样致力于实现更真实的评估和最小化污染，但它仍保持单次上下文检索范式。相比之下，IDE-Bench为智能体提供了完整的IDE工具界面访问权限——包括搜索、结构化文件编辑以及测试全栈应用程序的工具（API端点、数据库、WebSockets）——并支持多语言技术栈（C/C++、Java、TypeScript、Python）。最后，Terminal-Bench在以终端为中心的工作流中评估真实的长期计算机任务（Merrill et al., 2026）；它在测试底层命令执行和环境操作方面表现出色，但无法衡量高级IDE原生行为，如词汇搜索、代码库索引和IDE级导航。这些观察促使IDE-Bench的设计，旨在IDE环境中评估交互式、工具驱动的多步骤软件开发。

这八个代码仓库涵盖多个领域，包括系统编程（ESIM管理系统、内存分析应用、游戏引擎服务）、企业应用（SmartHub运营中心）、Web服务（事件回调系统、跨语言文档翻译器）、代码分析（代码质量分析器）和网络编程（网络流量分析器）。每个仓库的详细描述见附录F.1。

**3.2 测试环境**

我们使用LiteLLM接口部署智能体测试框架，这是一款在容器化环境中针对复杂编码任务对智能体进行基准测试的智能体评估框架，实现了一个模拟真实智能体驱动IDE（如Cursor和Windsurf）的工具生态系统。该测试框架架构利用Docker容器化实现可重现的测试环境、基于git的变更追踪实现精确的代码差异分析，以及多阶段执行流程，包括针对黄金差异的特定任务需求验证和综合测试套件。

**3.3 智能体工具接口**

该测试框架按照OpenAI的函数调用规范为模型配备了5类17种工具：（1）文件系统与代码导航（如读取文件、列出目录、代码库搜索），（2）代码编辑（编辑文件），（3）执行与测试（运行终端命令），（4）MERN任务的全栈测试（如API调用、数据库查询），以及（5）专业工具。每次工具调用都需要一个解释参数，以鼓励显式推理并支持轨迹分析。完整的工具规范见原文。

该文本为**英文**。

**翻译如下：**

带有参数和使用指南的说明在附录F.2中提供。

3. IDE-Bench基准测试设计
3.1 任务领域
IDE-Bench评估套件包含八个代码仓库，每个仓库包含十个任务，每个任务均经过精心设计，以代表某些真实世界的软件工程工作。与综合基准测试或孤立的编程挑战不同，我们的任务来源于工程师在生产环境中遇到的真实开发场景，包括解释不明确的功能需求、调试跨多个文件的状态管理问题、在保持向后兼容性的同时重构代码，或优化代码等。

IDE-Bench: 评估大型语言模型作为IDE代理在真实世界软件工程任务中的表现
表1. 各模型的任务解决率

3.4 执行工作流
评估管道包含三个主要阶段，如图1所示：

GPT 5.2
Claude Sonnet 4.5
Claude Haiku 4.5
Claude Opus 4.5
GPT 5.1 Codex Max
Gemini 3 Pro Preview
Qwen3 Max
Qwen3 Coder
DeepSeek V3.2
Grok 4.1 Fast
DeepSeek R1 0528
Grok Code Fast 1
Llama 4 Maverick
Command-R+ 08 2024
Llama 4 Scout

1. 容器配置：每个任务都从一个基于该仓库Dockerfile构建的新鲜Docker容器开始，提供一个隔离的Ubuntu 24.04环境。测试工具配置git用于变更跟踪，初始化仓库状态，并加载任务描述。

2. 代理执行：测试工具启动Gladiator代理（使用LiteLLM的被评估模型）或Oracle基线（直接应用参考补丁）。对于Gladiator代理，系统提供一个全面的系统提示（见附录F.4），其中包含工具描述和任务要求。代理迭代使用17个可用工具来探索代码库、修改文件并测试实现（工具规范：附录F.2）。所有工具调用、文件编辑和响应都会被记录以用于轨迹分析。安全限制阻止代理读取测试文件或黄金解决方案，确保它们必须仅根据任务要求进行推理。

pass@1 (%)

pass@5 (%)

85.00 ± 7.81
87.50 ± 7.28
78.75 ± 8.86
83.75 ± 8.05
73.75 ± 9.48
55.00 ± 10.65
65.00 ± 10.23
57.50 ± 10.59
31.25 ± 9.96
35.00 ± 10.23
20.00 ± 8.67
11.25 ± 6.99
2.50 ± 3.99
0.00 ± 2.29
2.50 ± 3.99

95.00 ± 5.10
88.75 ± 6.99
87.50 ± 7.28
86.25 ± 7.56
85.00 ± 7.81
80.00 ± 8.67
76.25 ± 9.19
75.00 ± 9.34
71.25 ± 9.74
67.50 ± 10.06
46.25 ± 10.67
32.50 ± 10.06
8.75 ± 6.34
7.50 ± 5.96
6.25 ± 5.56

表明在测试的各个领域表现稳定（Anthropic, 2025c;a;b; OpenAI, 2025a;b）。另一方面，下一层包括Gemini 3 Pro Preview（80.00%），其次是Qwen3模型（Qwen3 Max为76.25%，Qwen3 Coder为75.00%）和DeepSeek V3.2为71.25%（Google, 2025; Qwen-Team, 2025a;b; DeepSeek-AI, 2025）。随后性能下降更为陡峭，大多数较低层模型无法超过50%的解决率。因此，在能够

*说明：由于提供的文本在末尾处被截断，翻译亦止于此。*

该文本是**英文**（English）。

以下是翻译：

---

真正的容器化IDE智能体与那些无法做到这一点的智能体之间存在本质区别。

**3. 评估与评分：** 运行完成后（或达到100次迭代上限），我们执行仓库测试套件（./run tests.sh），并将智能体的代码更改与参考补丁进行对比。附录F.3.1提供了完整的评分流程和差异提取细节。

**3.4.1 实现与指标（摘要）**

其次，我们发现即使在聚合率看似相近的情况下，模型的身份仍然至关重要。Codex Max的表现符合预期，因为它专门针对智能体编码进行了微调，处于该基准测试的天花板水平。然而，更令人惊讶的是，Claude Haiku（一款面向速度的模型）表现几乎同样出色，且在某些任务上甚至优于Sonnet。这表明，在IDEBench的相当一部分任务中，限制因素往往是智能体能否执行一个干净的工具循环并收敛而不发生漂移。

我们使用基于LiteLLM的测试框架，配备标准化的工具调用和安全限制；详细说明见附录F.3.3。基准测试报告了基于实际表现的边界值（Null与Oracle基线）、任务解决率（pass@k）以及每次测试的通过率；完整指标套件（包括迭代次数和工具使用信号）见附录F.3.3。

**4. 实验**

表现较差的模型在如何从重试中获益方面呈现出不同的模式。在pass@1和pass@5之间的差距中，我们看到，pass@5低于85%的模型相比顶级模型获得了更大的提升（例如，DeepSeek V3.2提升了40个百分点，从31.25%升至71.25%），而pass@5高于85%的模型则几乎没有提升（例如，Claude Sonnet仅提升了1.25个百分点，从87.50%升至88.75%）。这个85%的阈值似乎标志着一种转变：模型从不一致的、迭代依赖的行为转变为稳定的、首次尝试即成功的行为。

**4.1 总体表现**

表1报告了pass@1和pass@5（即模型在一次和五次独立尝试中至少成功解决一次任务的比例）。我们对15个模型进行了6000次运行评估（80个任务 × 5次尝试 × 15个模型）。当按任务解决率对模型进行排序时，两种模式脱颖而出。

首先，在我们的评估中，顶级前沿模型与开源模型之间存在明显差距。GPT 5.2以95%的pass@5取得了最高性能，而Claude Sonnet 4.5、Claude Haiku 4.5、Claude Opus 4.5和GPT 5.1 Codex Max紧密聚集在85.0-88.75%范围内，且置信区间相对较窄——

**首次尝试表现（pass@1）：** 如表1所示，Claude Sonnet 4.5以87.50%的首次尝试通过率位居榜首，GPT 5.2以85.00%紧随其后，Claude Opus 4.5以83.75%排名第三。对于API成本和延迟限制重试次数的部署场景，这一首次——

---

**IDE-Bench：** 在真实世界软件工程任务中评估大型语言模型作为IDE智能体

**IDE-Bench执行流程：** 每个任务在隔离的Docker容器中运行。测试框架（the ha



---

## 论文 8

# LoCoBench-Agent: An Interactive Benchmark for LLM Agents in Long-Context Software Engineering

**作者**: Jielin Qiu, Zuxin Liu, Zhiwei Liu, Rithesh Murthy, Jianguo Zhang

**arXiv**: https://arxiv.org/abs/2511.13998

---

**语言识别：** 该文本为英语（English）。

---

**翻译（中文）：**

arXiv:2511.13998v1 [cs.SE] 2025年11月17日

LoCoBench-Agent：面向长上下文软件工程中LLM代理的交互式基准评测

Jielin Qiu, Zuxin Liu, Zhiwei Liu, Rithesh Murthy, Jianguo Zhang, Haolin Chen,
Shiyu Wang, Ming Zhu, Liangwei Yang, Juntao Tan, Roshan Ram, Akshara Prabhakar,
Tulika Awalgaonkar, Zixiang Chen, Zhepeng Cen, Cheng Qian,
Shelby Heinecke, Weiran Yao, Silvio Savarese, Caiming Xiong, Huan Wang
Salesforce AI Research
SalesforceAIResearch/LoCoBench-Agent

LoCoBench-Agent基准评测
LoCoBench基础

场景转换

代理框架

无偏评估

8,000个场景
10种语言
8个类别
36个领域

多阶段任务
交互式设置
上下文初始化（3种模式）
→ 多轮

ReAct模式
8个专业化工具
上下文管理
最多50轮

9个指标
LCBA-理解力（5个）
LCBA-效率（4个）
无文件偏差

上下文与记忆管理
层级记忆

摘要

语义搜索

上下文窗口利用率：

分层压缩

三级记忆系统：

会话摘要系统：

代码搜索系统：

40%利用率 → 警告
60%利用率 → 压缩
95%利用率 → 截断

工作记忆 → 最近3轮
压缩记忆 → 摘要
架构记忆 → 全局

5部分结构化格式
压缩比：4-10倍压缩
保留关键信息

@codebase风格
基于向量的RAG
读取前无上下文

8个专业化代理工具
文件操作（4个）

搜索功能（3个）

分析工具（1个）

安全特性

read • write • search_replace • list_dir

grep • glob_search • codebase_search

file_search（匹配）

沙盒化 • 结果限制 • 错误处理

9个无偏评估指标
LCBA-理解力（5个指标）

LCBA-效率（4个指标）

执行成功率、跨会话记忆保留
跨文件一致性、依赖遍历、解决方案可用性

运行时效率、记忆效率
信息覆盖率、长程依赖

框架特性
多轮交互：探索、适应性推理
长上下文聚焦：10K-1M tokens，降解分析
工具使用模式：效率追踪、错误恢复、策略分析

摘要
随着大型语言模型（LLM）演变为能够完成复杂软件开发任务的复杂自主代理，评估其真实世界能力变得至关重要。虽然现有的基准评测如LoCoBench [1]评估长上下文代码理解，但它们聚焦于单轮评估，无法捕捉真实世界编码代理所需的多轮交互特性、工具使用模式和适应性推理。我们推出了LoCoBench-Agent，这是一个综合评估框架，专门用于在现实的长上下文软件工程工作流中评估LLM代理。我们的框架将LoCoBench的8,000个场景扩展为交互式代理环境，实现了对多轮对话和工具使用效率的系统性评估。

这段文字是**英文**。以下是中文翻译：

---

我们在理解力、效率、错误恢复以及跨扩展开发会话的建筑一致性方面引入了涵盖9个指标的评估方法。我们的框架为智能体提供了8种专业工具（文件操作、搜索、代码分析），并对从10K到1M标记的上下文长度进行评估，从而能够精确评估长上下文性能。通过对最先进的模型进行系统评估，我们揭示了几个关键发现：（1）智能体展现出显著的长上下文鲁棒性；（2）存在理解力-效率的权衡且呈负相关，即深入探索会提高理解力但降低效率；（3）对话效率在不同模型间差异显著，战略性的工具使用模式是区分高性能智能体的关键。作为首个用于软件工程的长上下文LLM智能体基准，LoCoBench-Agent建立了衡量智能体能力、识别性能差距并推动大规模自主软件发展的严格基础。

1. 引言
大型语言模型（LLM）从被动代码补全工具演变为自主软件工程智能体，这代表了AI辅助开发的根本性转变。现代LLM智能体现在可以进行多轮对话、利用多样化的工具集、根据反馈调整策略，并在扩展的开发会话中保持上下文。然而，评估这些复杂能力需要与传统的单轮代码生成基准截然不同的方法。

智能体评估差距。虽然LoCoBench[1]为长上下文代码理解建立了全面评估，但它专注于单轮评估，即模型接收完整上下文并独立生成响应。现实世界的软件工程智能体的运作方式根本不同：它们进行多轮对话、通过工具使用逐步收集信息、根据反馈自适应地完善解决方案，并在扩展会话中保持建筑一致性。现有的智能体基准如AgentBench[5]和SWEBench[6]评估了智能体行为的有限方面，但缺乏对长上下文能力、系统性工具使用模式以及无偏评估方法论的系统性评估。

智能体评估中的关键挑战。在软件工程中评估LLM智能体面临独特挑战：（1）多轮复杂性：智能体必须在数十轮对话中保持上下文、建筑理解和解决方案连贯性；（2）工具使用模式：有效的智能体高效利用文件操作、搜索工具和代码分析能力，而不是依赖原始上下文记忆；（3）评估偏差：传统指标往往存在文件计数偏差（奖励修改更多文件的解决方案）；（4）长上下文退化：智能体性能可能随着上下文长度的增加而下降。

## Language Identification

The provided text is written in **English**.

---

## Translation (English → Chinese)

### 摘要

文本长度持续增长，但现有基准测试无法系统地衡量这一影响。

### LoCoBench-Agent：面向LLM代理在长上下文软件工程中的交互式基准测试

**我们的贡献：LoCoBench-Agent。** 我们推出了LoCoBench-Agent，这是一个专为长上下文软件工程中的LLM代理设计的综合评估框架。基于LoCoBench的8,000个高质量场景，我们将其转化为交互式代理环境，实现了真实的多轮评估。我们的框架具有以下特点：

- **交互式代理环境**：我们提供了一个完整的代理框架，包含8种专业工具（文件操作、搜索、代码分析），能够在10K-1M token上下文中实现真实的软件开发工作流程。
- **无偏评估方法论**：通过迭代指标设计和严格验证，我们开发了9个评估指标（5个理解力指标 + 4个效率指标），消除了文件数量偏差。
- **综合多轮评估**：我们的框架评估对话效率、工具使用模式、错误恢复速度、跨文件一致性和长距离依赖解析能力，这些是现实世界代理所必需但现有基准测试未测量的能力。
- **系统性长上下文评估**：我们提供了一个可扩展的框架，用于在所有8,000个场景中评估最先进模型，能够系统分析代理性能如何随上下文长度和任务复杂度变化。

**主要发现。** 通过综合评估，我们获得了关于代理能力和架构权衡的见解：(1) **理解力-效率权衡**：存在根本性的架构张力（r = −0.42 负相关），即实现高理解力所必需的详尽代码库探索与效率优化直接冲突，目前没有任何架构能够解决这一权衡，最佳表现的代理沿着帕累托边界聚集，而非实现同时优化；(2) **策略分化**：对话效率在不同模型间差异显著（平均10-22轮），高性能代理采用不同的策略——语义搜索优先配合针对性读取与穷举探索模式——揭示了在长上下文场景中，工具使用模式而非原始能力区分代理效能。

### 2. LoCoBench-Agent设计

LoCoBench-Agent将LoCoBench的8,000个静态评估场景转化为交互式代理环境。基准测试设计包含四个组成部分：(1) 从静态到交互式格式的场景转换，(2) 用于长上下文处理的智能上下文管理系统，(3) 配备专业开发工具的交互式代理系统，以及(4) 综合多轮评估流程。

### 2.1 从LoCoBench到LoCoBench-Agent

**系统性场景转换流程。** 我们将LoCoBench的8,000个单轮场景转化为多轮交互式环境。

**语言识别：该段落为英语（English）**

---

**译文：**

通过结构化的三阶段流程并采用缓存机制以提高效率。

**第一阶段：项目提取与规范化。** 对于每个场景，我们从LoCoBench的生成流程输出中提取完整的项目代码库，保留原始文件结构、依赖关系和架构组织。项目涵盖10种编程语言（Python、JavaScript、Java、C++、Go、Rust、TypeScript、PHP、Ruby、C#），分布在36个领域类别（Web应用程序、机器学习系统、数据处理管道、系统工具等）。每个项目包含10-100个文件，代码行数为100-10,000行，根据难度级别生成10K到1M标记不等的上下文。我们规范化文件路径、验证编译/执行，并创建结构化元数据，包括文件依赖图、入口点和架构模式。

**第二阶段：任务分解与阶段生成。** 单轮任务规范转化为多阶段对话结构。复杂需求分解为需要探索（"理解认证系统架构"）、规划（"设计新的OAuth2集成方案"）、实现（"实现令牌刷新机制"）和验证（"使用现有端点测试集成"）的增量子任务。每个阶段定义：初始提示、预期智能体操作（文件读取、工具使用模式）、成功条件（中间目标）以及根据智能体进展的动态后续提示。这种分解方式模拟了真实开发者的workflow，任务通过发现和迭代优化逐步展开。

**第三阶段：成功标准与评估模式。** 我们生成多维度的成功标准来评估：（1）功能正确性：代码编译、执行并产生预期输出；（2）架构一致性：保持现有设计模式、遵循项目约定、保留模块边界；（3）测试通过：现有测试套件保持通过，新测试覆盖新增功能；（4）需求满足：实现所有指定功能、处理边缘情况、包含文档。此外，我们还定义了中间检查点，评估整个会话过程中的工具使用效率、代码探索模式和问题解决方法。

**类别特定适配。** LoCoBench的8个任务类别经过针对性转换：代码理解→交互式代码探索，智能体通过有针对性的文件读取和搜索逐步发现代码库结构，而非一次性接收所有代码；架构理解→交互式架构探索，需要多轮分析设计模式、依赖关系和组件交互；Bug调查→交互式调试会话，包含假设生成、测试和迭代优化；功能实现→协作式...

这段文字是**英文**。以下是中文翻译：

---

**显性功能开发**，包含明确的规划、实施和验证阶段；**跨文件重构** → **引导式多文件重构**，需要在多个文件间协调同时保持一致性；**集成测试** → **测试驱动开发会话**，智能体在实现前先编写测试；**安全分析** → **交互式安全审计**，发现并修复漏洞；**多会话开发** → **扩展开发项目**，模拟多天工作并保持上下文延续。

**上下文初始化策略**。我们提供三种初始化模式，反映不同的现实场景：(1) **最小模式**（默认，90%的评估）：仅加载README、项目文件结构（文件名和路径，不含内容）以及入口点文件名。智能体必须通过file_search、grep、codebase_search和read_file工具发现相关代码，模拟开发者在VS Code或Cursor等生产环境中处理陌生代码库的方式。此模式可防止上下文窗口溢出，鼓励高效使用工具，并提供最真实的评估。(2) **空模式**（探索性评估）：仅提供任务规范和项目根路径。智能体必须发现所有内容，包括文件结构，强制完全探索。用于测试智能体在零初始上下文下的能力。(3) **完整模式**（基线比较）：将完整代码库加载到初始上下文（所有文件），模拟传统的单轮评估。仅适用于小型项目（<100K tokens），因为受上下文限制。用于比较交互式与单轮方法。

**2.2 长上下文内存管理**

我们的内存管理系统借鉴了Cursor等生产级编码助手，实现了针对系统评估改编的类似上下文窗口策略。

**三级上下文压缩策略**。我们实现了三级自适应压缩系统，使用基于tiktoken的令牌计数实时监控上下文利用率。系统在三个不同阈值下运行：(1) **预警阶段**（40%容量）：开始选择性压缩非活跃文件，即过去5轮未访问的文件。文件被压缩为保留函数/类签名、导入语句和文档字符串的结构摘要，同时移除实现主体。文档文件（README、LICENSE、.md）首先被大幅压缩。(2) **临界阈值**（60%容量）：通过LLM对较早轮次进行摘要来压缩对话历史（保留前2轮和后3轮原文）。生成结构化摘要，包含：任务上下文、执行的操作（工具调用及结果）、结果（修复的错误、取得的进展）、下一步行动以及重要引用（文件路径、函数名、变量）。(3) **紧急截断**（95%容量）：激进截断，仅保留...



---

## 论文 9

# SWE-Dev: Building Software Engineering Agents with Training and Inference Scaling

**作者**: Haoran Wang, Zhenyu Hou, Yao Wei, Jie Tang, Yuxiao Dong

**arXiv**: https://arxiv.org/abs/2506.07636

---

该文本为**英文**。以下为中文翻译：

---

**SWE-Dev：基于训练与推理扩展构建软件工程智能体**

Haoran Wang¹⁎, Zhenyu Hou¹⁎, Yao Wei², Jie Tang¹, Yuxiao Dong¹
¹ 清华大学
² Code

**摘要**

大语言模型（LLMs）已从对话式问题求解快速发展至解决涉及工具使用的现实世界任务，如软件工程（SWE）。近期基于LLM的SWE系统（如OpenAI Codex和Cursor）已实现了软件开发过程的端到端自动化。然而，由于缺乏高质量的训练数据及可靠的测试时评估，构建有效的SWE智能体仍面临挑战。为解决这一问题，我们提出了SWE-Dev——一个基于开源LLM的SWE智能体，重点关注训练扩展与推理扩展。在训练扩展方面，我们开发了一个稳健的管道来合成测试用例并扩展智能体轨迹，以构建训练数据。在推理扩展方面，我们增加了单次运行中的交互预算，以支持在单次独立尝试中进行更深入的思考。在SWE-bench Verified基准测试上的实验表明，SWE-Dev模型在所有开源SWE智能体中达到了最优性能。具体而言，我们的7B和32B模型的解决率分别达到23.4%和36.6%，优于当前最先进的开源模型。所有代码、模型和数据集均已在https://github.com/THUDM/SWE-Dev上公开。

**图1：** 训练与推理扩展下的SWE-Dev性能。值得注意的是，SWE-Dev-32B无需推理扩展即可达到34.0%的解决率，与GPT-4o的性能相当。

...（需处理复杂且脆弱的运行时环境，解决工具链问题，执行脚本，并对大型、相互依赖的代码库进行推理（Ma et al., 2024b））。

SWE任务通常在SWEA-bench（Jimenez et al., 2024）及其近期的多模态扩展（Yang et al., 2024b; Zan et al., 2025）等基准测试上进行评估。这些基准测试要求模型在真实代码库上生成可验证的、通过测试的解决方案。为此，模型必须具备逐步推理、工具使用和长程规划的能力。

迄今为止，针对这些任务的模型训练需要可靠的奖励信号，这些信号通常来自验证解决方案正确性的测试用例。然而，大多数现有数据集缺乏此类测试用例或可执行环境（Chen et al., 2021），这使得在训练过程中难以评估解决方案或提供有用的反馈。这一缺陷限制了模型通过试错迭代优化输出的能力，从而制约了其解决需要基于事实且可验证解决方案的真实世界SWE任务的潜力。

为解决这一问题，我们推出了SWE-Dev——一个开源的SWE智能体...

---

*注：原文结尾处有缺失（"an open-source SWE a"），已按原文完整性保留翻译。*

该文本为**英文**。以下为中文翻译：

---

**gent framework coupled**

**引言**

大语言模型（LLMs）已从生成简单代码片段快速发展到处理更复杂的任务，如竞赛编程（Li et al., 2022; OpenAI, 2025）、机器学习问题（Chan et al., 2024）以及现实世界的软件工程（SWE）任务（Xi et al., 2024; Jimenez et al., 2024; Zan et al., 2025）。在这些任务中，SWE任务尤为困难且具有挑战性（OpenAI, 2025; Cursor, 2024），但对于提升现实世界生产力具有极高的价值。与简单的代码生成不同，SWE任务通常*工作

Zhipu AI

在Zhipu AI实习期间部分完成。

1

配备可扩展的测试用例生成管道。该管道分为两个阶段：首先，使用LLMs生成Gherkin格式的描述——一种用于指定测试场景的结构化自然语言格式。其次，代码生成器输出代码补丁以进行进一步验证。实证分析表明，这些合成的测试用例与原始问题语义高度吻合。

通过大规模实验，我们发现明确的训练扩展趋势：增加采样的智能体轨迹数量能够提升下游性能。为提高效率，我们提出了一种基于LLM的过滤方法，用于筛选高质量轨迹。这使我们能够在保留最有价值数据的同时，保持完整数据集的优势。

我们还提出了迭代扩展——一种简单而有效的策略，旨在通过增加单次评估回合中的交互轮次来扩展推理预算。这减少了对重复重新评估的需求，使其在测试预言机访问成本较高或存在延迟的场景中特别有利。

此外，我们探索了先进的后训练策略，包括拒绝采样微调（RFT）（Yuan et al., 2023）、KTO（Ethayarajh et al., 2024）和OREO（Wang et al., 2024a）。我们观察到RFT带来了最显著的性能提升，而离线强化学习（RL）方法——KTO和OREO——则取得了边际或任务特定的收益。

我们基于开源的Qwen2.5-Coder（Hui et al., 2024）、Llama3.1（Llama, 2024）和GLM4-9B（GLM, 2024）模型构建了SWE-Dev。其性能在SWE-Bench-Verified基准上进行了评估，结果如图1所示。使用Qwen2.5-Coder-32B模型，SWE-Dev在SWE-Bench-Verified上达到了36.6%的解决率，在开源SWE智能体中代表了最先进水平。

表明SWE-Dev-32B模型通过增加45个交互回合，将解决率从34.0%提升至36.6%，凸显了多步骤执行对智能体的益处。
3. SWE智能体的后训练方案。我们研究了多种后训练方法，包括RFT、KTO和OREO，以及混合组合。RFT通过有效利用高质量样本持续优于其他方法，展示了其训练SWE智能体的鲁棒性和可扩展性。

2

**相关工作**

**SWE数据**

**语言识别：该段落为英语（English）。**

---

**翻译（中文）：**

数据集构建。现有的基准测试，如SWE-bench（Jimenez等，2024）、DevEval（Li等，2024b）、EvoCodeBench（Li等，2024a）和Commit0（Zhao等，2024），均从大型数据集中爬取数据并进行人工标注，这一过程耗费大量人力。部分研究尝试从现有失败-通过测试用例中进行筛选，如SWE-Gym（Pan等，2024）手动标注了2400个实例。尽管从大型数据集中进行筛选具有一定效果（Golubev等，2024），但许多有用的Issue仅因对应的PR不包含测试用例而被遗漏。因此，从Issue描述自动生成测试用例变得至关重要。

SWE智能体框架。SWE智能体框架主要聚焦于两种主流类型：交互式框架和流水线式框架。交互式框架，如OpenHands（Wang等，2024c）、SWE-Agent（Yang等，2024a）、Learn-By-Interact（Su等，2025）以及Wang等（2024b），通常预定义一组智能体-计算机接口（ACI）来辅助模型操作环境。而流水线式框架则将整个过程设计为多个步骤，如Agentless（Xia等，2024）、CoreR（Chen等，2024）、MarsCode（Liu等，2024）和SuperCoder2.0（Gautam等，2024）。智能体通过故障定位、补丁生成和多数投票来生成补丁。此外，推理阶段还运用了MCTS（Antoniades等，2024）和批评者引导生成（Badertdinov等，2024）以及模型集成（Zhang等，2024a）等技术。流水线式流程虽可受益于规范定义，但难以推广到其他编码任务；相比之下，交互式框架能够使用自然语言作为指令完成通用任务。

1. **测试用例生成流水线。** 我们构建了一个可扩展的基于大语言模型的流水线，用于生成真实世界的SWE实例及其可执行测试用例。通过该流水线，我们从4000个仓库中筛选出38000个高质量Issue，成功构建了2000个测试用例。
2. **数据与推理的规模化趋势。** 我们通过实证研究确定了训练数据量、交互步骤数量与模型性能之间的规模化关系。我们发现[此处原文似有缺失]。

**![修订自回溯]**

**描述生成 → Repo信息提取 → 语句 → PR补丁 → 相关文件**

**测试用例生成**

"该错误似乎是由...引起的..."

**功能：** 使用Gherkin运行...
**场景：** 给定；然后；且...

```diff
diff --git
a/test_swedev.py
b/test_swedev.py
...
@@ -0,0 +1,5 @@
+import pytest
+def test_zero ():
+ assert fn(1) == 0
```

**失败-通过测试用例**

图2：测试用例生成流水线，分为描述生成和代码生成两个阶段。该流水线首先提取仓库信息，然后生成Gherkin场景，随后生成详细的测试用例。可选的修订步骤利用回溯错误来优化生成的测试用例。最终输出包括失败-通过测试用例。

3000

SWE-D

这段文字是**英语**。

---

**译文：**

# 大规模构建软件任务

在该部分中，我们开发了一种系统性的策略来大规模构建软件任务。SWE-Dev 的核心在于大规模收集仓库和任务实例，然后开发一种基于 LLM 的自动化测试用例生成方法。这种可扩展的方法使我们能够为 SWE 任务构建全面的训练数据集，并通过测试用例驱动的轨迹采样来提升性能，如图 2 所示。基于可扩展数据集构建和轨迹优化的系统流程，我们构建了 SWE-Dev 智能体。

## 3.1 实例收集

我们首先爬取了 24 万个包含 GitHub URL 的 PyPI 包元数据，筛选出 Star 数 ≥ 5 且 PR 数 ≥ 3 的仓库，得到 5.9 万个仓库的子集。由于网络限制、机器容量和复杂的依赖管理，我们成功下载了 10,416 个仓库。按照 Jimenez 等人（2024）描述的方法并做了一些修改，我们共提取了 8.8 万个实例。

为了优化该数据集，我们应用了基于规则的过滤方法来调整补丁长度以适应模型上下文窗口，同时剔除平凡或无关的问题，并保持多样性。最终，我们从 4,413 个仓库中保留了 3.8 万个实例作为训练集。如图 3 所示，超过 4,000 个仓库包含不到 5 个实例，这显著增强了数据集的多样性。

## 3.2 自动测试用例生成

虽然从互联网爬取包含正确补丁的 PR 相对简单，但这些补丁通常缺少对应的测试用例。这种缺失阻碍了轨迹正确性的验证，并使强化学习技术不可行。为了解决这个问题，我们利用 LLM 来生成带有必要上下文的测试用例。

具体而言，我们的测试用例生成流程包括四个步骤。该流程首先从代码库中提取相关信息，包括上下文代码片段和元数据，这些作为后续步骤的基础。接着，利用提取的信息生成 Gherkin 描述，其中模型会生成结构化的高层场景。这些描述采用 Gherkin 语法，使用特殊关键字来提供结构和含义，使场景简洁且易于理解。此后，这些描述被用于生成测试用例，利用上下文细节来创建健壮且有意义的测试用例。随后，生成的测试用例可以进入可选的修订阶段，在该阶段对其进行审查和细化以确保正确性。

这种分阶段方法反映了 LLM 内在的结构化能力……

这段文字是**英文**。以下是学术化的中文翻译：

---

生成测试用例。该模型从提供的上下文中提取场景，并结合代码片段作为参考，生成相应的测试用例。将前三个步骤合并为一个步骤往往会产生无关或不连贯的测试用例。通过引入细粒度指令，我们提供了更清晰的指导，显著提高了生成输出的准确性和上下文相关性。

由于计算预算的限制，我们将部分数据集用于合成测试用例。从包含26,000个实例的数据集中，我们生成了2,097个失败转通过（fail-to-pass）函数。此外，我们整合了数据集中现有的测试用例，共计4,630个测试用例。值得注意的是，我们方法中大多数不成功的实例是由于环境限制而非方法论本身的根本性局限，这进一步证明了通过我们的方法生成测试用例的可行性。

3.3 失败转通过（F2P）函数及带有测试用例的实例，凸显了利用各任务领域特定优势的重要性。

| 模型 | #w/test | F2P | F2F | #w/F2P |
|------|---------|-----|-----|--------|
| Llama | 757 | 185 | 1,273 | 73 |
| Mistral | 767 | 151 | 990 | 81 |
| Qwen | 793 | 190 | 1,185 | 83 |
| Mix | 828 | 237 | 1,408 | 92 |

**表1：测试用例合成模型对比**。#w/test和#w/F2P分别表示带有测试用例和带有F2P测试用例的实例数量。模型包括Llama-3.1-70B-Instruct、Qwen-2.5-Coder-32B-Instruct和Mistral-Large-Instruct-2407。F2P和F2F代表合成过程中生成的测试函数总数。Mix模型使用Llama进行描述生成，Qwen进行代码生成。

**与开源数据集的对比**。我们进一步通过比较在不同数据集问题上训练的模型质量来验证生成的测试用例的质量。如表2所示，加入我们生成的测试用例后，模型质量与现有开源数据集（Badertdinov等，2024年）相当。

| 数据集 | 数据集统计 | 解决率 |
|--------|------------|--------|
| Nebius/SWE-bench-extra | | 15.0 |
| SWE-Gym/SWE-Gym | | 13.8 |
| SWE-Dev（保留） | | 15.4 |
| SWE-Dev（过滤后） | | 12.4 |

**表2：不同训练数据集的性能对比**。所有轨迹均未经过评估。SWE-Dev（保留）指过滤后保留的提示词，而SWE-Dev（过滤后）对应被丢弃的提示词。Random表示从轨迹池中随机选取。

为了评估我们构建的数据集的质量，我们进行了三项实验：（1）描述与代码合成之间的通过率分析；（2）过滤后的问题与开源数据集的对比；（3）测试用例的验证。这些实验共同凸显了我们数据集在生成高质量测试用例方面的优势及其在下游任务中的实用性。

**LLM生成的测试用例验证**。我们将数据集与其他广泛使用的数据集（如Nebius/SWE-bench-extra）的有效性进行了对比。如表3所示，模型...



---

## 论文 10

# SWE-Bench++: A Framework for the Scalable Generation of Software Engineering Benchmarks from Open-Source Repositories

**作者**: Lilin Wang, Lucas Ramalho, Alan Celestino, Phuc Anthony Pham, Yu Liu

**arXiv**: https://arxiv.org/abs/2512.17419

---

## 语言识别

这段文字是**英文**（English）。

---

## 中文翻译

# 一个可扩展生成软件工程基准测试的框架

## 源自开源仓库

Lilin Wang、Lucas Ramalho、Alan Celestino、Phuc Anthony Pham、Yu Liu、Umang Kumar Sinha、Andres Portillo、Onassis Osunwa、Gabriel Maduekwe

研究与开发部，Turing

arXiv:2512.17419v1 [cs.SE] 2025年12月19日

项目页面：https://research.turing.com/swebench

**摘要**

像SWE-bench这样的基准测试已经将大型语言模型（LLM）在仓库级软件工程任务上的评估标准化。然而，这些工作仍然受到人工策划、静态数据集以及仅关注Python错误修复的限制。我们推出了SWE-bench++，这是一个自动化框架，可从开源GitHub项目生成仓库级编码任务。与合成方法不同，我们的流水线通过抓取实时拉取请求（pull requests），覆盖了来自开源GitHub仓库的11种语言的错误修复和功能请求。SWE-bench++通过四个阶段将GitHub拉取请求转化为可复现的基于执行的任务：程序化来源获取、环境合成、测试预言提取和质量保证。最后的提示引导轨迹合成步骤将强模型无法解决的实例转化为训练轨迹。我们的初始基准测试包含来自11种语言3,971个仓库的11,133个实例。在这个基准测试的1,782个实例子集上当今最强的模型表现如下：claude-sonnet-4.5达到36.20%的pass@10，gpt-5-2025-08-07达到34.57%，gemini/gemini-2.5-pro达到24.92%，gpt-4o达到16.89%。我们进一步展示了数据集的实用性，表明在SWE-bench++实例上进行微调可以在SWE-bench多语言基准测试上产生可测量的改进。SWE-bench++提供了一个可扩展的多语言基准测试，用于评估和改进仓库级代码生成。

图1：SWE-bench++框架。与静态基准测试不同，我们的流水线使用约束神经合成来生成可复现的Docker环境，并针对11种语言使用自适应日志解析器。三状态状态差分预言器自动将任务分类为错误修复或功能请求，在规模上生成经过验证的基准测试实例和提示引导的训练轨迹。

---

## 引言

大型语言模型（LLM）编码智能体的评估已经从孤立函数合成（例如HumanEval（Chen et al., 2021））转移到仓库级软件工程。SWE-bench引入了一个基于真实GitHub问题的仓库级基准测试，能够对LLM编码智能体进行更真实的评估（Jimenez et al., 2024）。然而，它依赖于人工策划且仅覆盖12个Python仓库。这种规模太小，无法捕捉开源项目的结构和语言多样性。

最近提高可扩展性的工作遵循了两条路径。首先，像Multi-SWE-bench（Zan等...

---

*注意：提供的文本在结尾处似乎不完整，最后一句话以"First, benchmarks such as Multi-SWE-bench (Zan"中断。*

The paragraph is written in **English**.

---

**Translation (English → Chinese):**

该评估框架可扩展至Java和Rust等语言，但依赖于人工整理，且仅覆盖少数几十个代码库。其次，以Python为中心的自动化方案（如SWEE-bench，Vergicros等人，2025）虽能扩展至数百个代码库，但仍局限于Python领域，并存在两个技术局限。其一，其两状态测试预言机（补丁前→补丁后）设计初衷并非用于提取引入新API或新功能的特性请求，因为这些请求会导致补丁前状态因符号缺失而无法构建——现有流程必须将此类情况过滤为错误。这种方法论约束将自动化基准测试限制在可于两种状态下执行测试的场景，严重限制了特性请求的覆盖范围。其二，其依赖于静态正则表达式进行日志解析，导致无法扩展至拥有异构测试运行器和非标准输出的"长尾"代码库。

此外，近期研究如SWE-Smith（Yang等人，2025）和SWE-Flow（Zhang等人，2025a）尝试通过合成方式扩展数据生成，例如从测试驱动开发（TDD）模式中合成任务。尽管这些方法对训练有价值，但在评估模型在"野外"分布上的表现时效果欠佳。它们缺乏人类编写代码所具有的噪声、复杂性和历史特性。此外，上述所有基准测试的静态特性引入了关键的数据污染风险：大多数实例创建于现代模型训练截止日期之前，使得它们容易遭受记忆化攻击。

为弥补这些差距，我们提出了SWE-Bench++，一个自动化的多语言框架，可从GitHub拉取请求中生成软件工程基准测试。与以往方法不同，我们的方法论提供了一个系统性流程，可在无需人工干预的情况下将原始GitHub仓库转换为可执行的评估环境。

为应对这些挑战，我们引入了三种机制：

1. **受限环境与预言机合成**：现有框架通常依赖非结构化命令提取来构建环境，并使用静态正则表达式解析日志，这些方法在规模化时可能不够稳健。我们为基础设施和验证引入了合成引擎。对于环境，我们使用模板引导合成来填充安全加固的Dockerfile（例如强制执行多阶段构建）。这种方法结合了LLM推理与静态模板，在同一2377个有效拉取请求的池中运行，相比SetUpAgent基线实现了约137%的更高产出。对于验证，当确定性解析器失效时，我们使用自适应解析器合成来为异构日志生成自定义Python解析器。这种受限神经合成方法在11种语言和3971个仓库中标准化了环境与预言机的构建。

2. **状态差分任务分类**：当前基准测试难以区分错误修复与...

这段文本是**英文**。以下是学术性的中文翻译：

---

s和功能请求的案例往往被丢弃，因为这些案例中预置代码库无法构建。我们实现了一个状态差分Oracle，用于比较三个仓库状态：Base（基线）、Before（应用测试补丁后）以及After（完整PR应用后）。我们不将Before状态中的特定构建失败视为错误，而是将其作为功能请求的语义信号（其中测试依赖于尚未实现的代码）。这使得我们能够同时验证回归修复和新功能实现。

3. 提示引导的轨迹合成：标准训练数据生成（如SWE-Gym）依赖于对智能体已能解决的简单任务的被动过滤。我们引入了一种主动的提示注入算法，将SOTA模型失败的模型崩溃案例（在SWE-Bench++环境中）转换为可执行的训练轨迹。通过注入函数签名和依赖图作为提示，我们为智能体搭建了解决先前不可能完成的任务的脚手架。仅对145条这样的轨迹进行微调，就能将跨语言性能从SWE-bench多语言基准上的1.6%提升至3.6%。

贡献：我们的工作做出以下贡献：

• 大规模基准测试：我们从3,971个仓库构建了11,133个仓库级实例，涵盖了多样化的构建系统和编码模式。

---

表1：软件工程基准测试与框架对比

| 特性 | SWE-bench / Multi-SWE | SWEE-bench (SetUpAgent) | SWE-Smith | SWE-Flow | SWE-Fixer | SWE-Gym | SWE-Bench++ ( Ours) |
|------|----------------------|------------------------|-----------|-----------|-----------|---------|---------------------|
| 功能 | 基准测试 | 基准测试 | 基准测试 | 基准测试 | 求解器工具 | RL接口 | 实时基准生成器 |
| 生成方式 | 手动整理 | 自动化 | 合成 | 合成 | 静态抓取（仅PR） | 自动化 | 自动化 |
| 范围 | 不适用 | 容器 | Bug修复对 | 修复-测试对 | 不适用 | 不适用 | 容器、日志解析器、轨迹 |
| 环境策略 | 预构建镜像 | 提取命令 | 预构建镜像 | 预验证镜像 | 不适用 | 预构建镜像 | 自动合成 |
| 规模 | 12 / 42 | 514 | 128 | 74 | ~110,000 | 358 | 3,971 |
| 语言 | Python/9 | 仅Python | 仅Python | 仅Python | 以Python为中心 | 不适用 | 11种（自动化） |
| 任务范围 | Bug修复 | Bug修复 | 仅Bug修复 | TDD增量开发 | 简单Bug | Bug修复 | Bug修复与功能请求 |
| 日志解析 | 静态正则 | 静态正则 | 静态正则 | 静态正则 | 不适用 | 不适用 | 合成自适应解析器 |
| 分布 | 有机 | 有机 | 合成 | 合成 | 有机 | 有机 | 有机 |
| 新鲜度 | 静态 | 不适用 | 不适用 | 静态 | 静态 | 持续（动态） | 静态 |

• 自动化多语言环境：我们的管道自动合成了涵盖11种语言的Docker环境和日志解析器。
• 更广泛的任务覆盖：我们的状态差分Oracle能够识别Bug修复和功能请求，与先前基准测试相比提升了功能类覆盖（例如，在SWE-bench中提升9%）。
• 污染感知评估：SWE-Bench++由带时间戳的GitHub Pull Request构建，可按PR创建日期进行过滤，从而实现时间分离的评估集，降低数据污染风险。

**语言识别：** 该段落为**英语**（English）。

---

**译文：**

用于未来模型。
表1将SWE-Bench++与之前的基准测试和框架进行了比较。

2

**相关工作**

我们主要从结构局限性的角度回顾相关工作，而我们的基准测试正是旨在解决这些局限性。虽然SWE-bench及其人工整理的变体SWE-bench Verified（Chowdhury等人，2024年）确立了评估仓库级任务上LLM的金标准，但其静态和人工密集的本质为软件工程评估的规模化带来了根本性障碍。

**可扩展性** 首先，像SWE-Smith和SWE-Flow这样的合成生成方法利用LLM来合成训练信号—— either by injecting bugs into existing codebases or by inferring incremental steps from Test-Driven Development (TDD) patterns. While valuable for training efficiency, these synthetic tasks often lack the noise, ambiguity, and "in-the-wild" distribution of real human-written code. Second, static data scaling efforts like
SWE-Fixer (Xie et al., 2025) aggregate massive datasets by scraping GitHub history. However, it operates as a
retrieval-based pipeline without execution environments, prioritizing raw volume over execution-based verification.
Third, compute scaling frameworks like SWE-Gym (Pan et al., 2025) transform existing benchmarks into
reinforcement learning (RL) environments to generate millions of agent trajectories. While this scales experience,
it remains bound to the limited problem set of the original manually curated datasets. Finally, attempts to automate
organic task collection, such as SWEE-bench (Vergopoulos et al., 2025), utilize agents (e.g., SetUpAgent) to scaffold
environments but remain restricted to Python and lack support for feature requests.
Data Contamination and "Live" Evaluation Static benchmarks are highly vulnerable to data contamination, as
instances created before a model's knowledge cutoff are frequently memorized during pre-training. The community
has responded with "live" benchmarks such as SWE-bench-Live and LiveCodeBench, which continuously harvest
new problems (Zhang et al., 2025b; Jain et al., 2025). Similarly, SWE-bench Pro attempts to mitigate contamination
by incorporating private, commercial repositories (Deng et al., 2025). However, these solutions often rely on specific
language ecosystems or lack the fully automated, multi-stage verification pipeline required to scale beyond hundreds
of tasks to thousands.
3

The Weak Test Oracle Problem Standard evaluation protocols assume that passing a developer-written test suite
equates to a correct fix. However, this "test oracle" is often unreliable. Empirical studies on SWE-bench have revealed
that a significant percentage of plausible patches—those that pass the provided tests—are semantically incorrect or
diverge from the ground truth. This oracle limitation can lead to overestimation of model capabilities, highlighting the
need for more rigorous, state-based verification methods.
Environment Reproducibility Challenges Accurately reconstructi

The text is written in **English**.

---

**Translation (English → Chinese):**

ng历史开发环境是主要的可扩展性瓶颈。复杂的依赖树和版本不匹配经常导致“环境腐化”，即测试失败是由配置错误而非模型补丁缺陷引起的。虽然像SetUpAgent（Vergopoulos等人，2025）这样的代理系统通过从文档中提取命令来自动化环境创建的某些方面，但它们往往缺乏确定性、模板化脚手架的可靠性，导致其在多语言仓库中的成功率受限。

**解决方案泄露与模糊性** 最后，现有基准测试中问题陈述的质量差异显著。SWE-bench+（Aleithan等人，2024年）最近的分析显示，32.67%的成功代理解决方案涉及“解决方案泄露”，即正确的代码或直接指针明确存在于问题描述或评论中。这使得代理可以通过信息检索而非推理来“解决”任务，进一步扭曲了排行榜排名。

---

**方法论：SWE-Bench++框架**

SWE-Bench++是一个四阶段流水线（图1），将GitHub拉取请求转换为可执行的软件工程任务。

**3.1 阶段一：程序化检索**

流水线始于广泛搜索，以识别代表真实软件维护和演化的候选任务。我们采用可扩展过滤器处理GitHub数据流，选择满足以下条件的仓库和拉取请求（PR）：（a）具有近期提交活动的活跃维护历史；（b）有社区采用的证据（如超过100颗星）且有可识别的测试框架；（c）具有实质性复杂性，由超过10k行代码的代码库定义；（d）明确解决某个问题的已合并PR（确保自然语言问题描述与编码解决方案之间的关联）；以及（e）包含对测试文件编辑或添加的PR。这种粗粒度筛选撒下大网，为后续计算密集型阶段识别出数百万个潜在候选。

**3.2 阶段二：环境合成**

一旦识别出高质量的PR，我们会创建一个可复现的执行环境，镜像问题出现时的仓库状态。我们将环境生成视为受约束的合成问题。我们的系统采用混合架构，而非依赖非结构化生成：参数化的Dockerfile模板强制执行结构有效性和安全标准（如多阶段构建），而LLM则推断无法静态解析的缺失动态依赖和版本（如包版本、入口点）。

**3.2.1 基于模板的脚手架**

为降低从零生成Dockerfile固有的安全风险和逻辑错误，我们的系统采用经过验证的、语言特定的模板库（如Python、Java、Go、Rust）。这些模板强制执行最佳实践，如多阶段构建、最小基础镜像以及非



---

## 论文 11

# Multi-Agent Systems for Dataset Adaptation in Software Engineering: Capabilities, Limitations, and Future Directions

**作者**: Jingyi Chen, Xiaoyan Guo, Songqiang Chen, Shing-Chi Cheung, Jiasi Shen

**arXiv**: https://arxiv.org/abs/2511.21380

---

这段文字是**英文**。以下是学术化的中文翻译：

---

arXiv:2511.21380v1 [cs.SE] 2025年11月26日

**软件工程中的多智能体系统用于数据集适配：能力、局限性与未来方向**

陈静怡∗，香港科技大学，中国
郭晓燕∗，中国科学技术大学，中国
陈松强，香港科技大学，香港
张成志†，香港科技大学，香港
沈家思†，香港科技大学，香港

自动化软件工程（SE）研究成果在跨数据集间的适配对于可扩展性和可复现性至关重要，然而该领域目前仍缺乏深入研究。近期基于大语言模型（LLM）的多智能体系统（如GitHub Copilot的智能体模式）取得了进展，承诺通过协调推理、代码生成和工具交互来自动化复杂的开发工作流程。本文首次针对多智能体系统在数据集适配任务中的表现进行实证研究。我们评估了Copilot（在GPT-4.1和Claude Sonnet 4支持下）在适配来自ROCODE和LogHub 2.0等基准仓库的软件工程研究成果方面的能力。通过五阶段评估流程（文件理解、代码编辑、命令生成、验证和最终执行），我们测量了成功率，分析了失败模式，并评估了旨在提升智能体性能的基于提示的干预措施。结果表明，当前系统能够识别关键文件并生成部分适配方案，但很少产生功能正确的实现。基于提示的干预措施，尤其是提供执行错误信息和参考代码，显著提升了与真实基准的结构相似性（从7.25%提升至67.14%），凸显了上下文和反馈驱动指导的重要性。我们的发现揭示了当今多智能体LLM系统在数据集适配方面的前景与局限性，并为未来构建更可靠、更具自我纠正能力的智能体提供了具体方向。

**关键词与短语**：多智能体系统、数据集适配、大语言模型、自动化软件工程

---

**1 引言**

软件工程（SE）正处于快速创新时期。从代码生成、缺陷修复到性能分析和安全研究，研究人员不断提出新技术[9, 18, 27]。每种技术都必须在多个数据集上进行评估，以证明其在单一仓库或环境之外的通用有效性[8, 16]；然而跨数据集的自动适配在很大程度上仍未被探索。领域专家可以手动为每个数据集定制技术（例如安装依赖、构建流水线、编写测试用例），但随着数据集数量和多样性的增长，这种方法无法扩展。因此，对于未来软件工程研究而言，实现可扩展的自动化数据集适配至关重要。

大语言模型（LLM）的最新进展为解决这一问题带来了新的可能性……

The provided paragraph is written in **English**.

---

**Translation (Chinese Simplified):**

开启新的可能性。现代大语言模型（如ChatGPT和GitHub Copilot）能够解释代码、搜索项目目录、生成命令、修改文件，并与外部工具进行交互。这些能力可以在多智能体系统[11]中进行编排，多个专业化的大语言模型智能体协同工作以规划和执行复杂任务。最近的研究表明，多智能体系统通过交叉检验和角色分工，在软件开发活动中实现自主问题解决、鲁棒性和可扩展性。GitHub Copilot的智能体模式是多智能体系统的典型示例：该智能体分析代码库，规划多步骤解决方案，运行命令或测试，使用MCP工具[1]浏览代码库和编辑文件，并迭代优化其工作。结合新兴的基准测试，多智能体系统能够自主适配软件工件：解析代码库元数据，生成构建命令，纠正错误并验证结果，从而减少人工工作量并实现大规模可复现性。

然而，基于大语言模型的多智能体系统在自动化数据集适配方面的有效性仍不清楚。目前尚无实证研究来评估在将技术适配到新数据集的任务中，最先进（SOTA）智能体的表现。了解其局限性对于未来设计可靠的多智能体系统用于数据集适配任务至关重要。本文首次开展实证研究来评估多智能体系统在这一具有挑战性的任务上的表现。我们研究了一个由GitHub Copilot智能体模式代表的SOTA智能体如何将不同的软件工程工具适配到不同的基准数据集。我们的两个研究问题指导本研究：

**RQ1**：当自动将软件工程研究工件适配到新数据集时，SOTA多智能体系统的表现如何？我们详细记录智能体采取的步骤，测量成功率，并识别失败模式。

**RQ2**：提示级干预能否提高性能，并为更有效的多智能体系统设计提供信息？受近期提示工程和智能体协调研究[33]的启发，我们探索了提供错误消息、注入缺失信息或提示故障位置等干预措施，以指示智能体在失败时重试。

通过回答这些问题，...

## 语言识别

这段文字是**英文**（English）。

---

## 中文翻译

基于这些问题的研究，我们旨在阐明基于大语言模型的多智能体系统在自动化数据集适配方面的前景与局限性。我们的研究发现将为下一代智能体的设计提供参考，并为更具可扩展性的软件工程研究做出贡献。

**问题建模**

设 $R_D$ 表示数据集仓库，$R_T$ 表示技术仓库，两者均通过 GitHub URL 或本地路径提供。我们的目标是自动修改 $R_D$ 或 $R_T$，构建可运行的实验，并获取其执行结果。我们的问题可以表示为如下映射：

$$f : (R_D, R_T) \mapsto E$$

在该映射中，$E$ 表示最终的实验状态，包括为确保兼容性而执行的代码适配、为运行实验而生成的可执行脚本或命令，以及执行这些脚本或命令后产生的结果。该建模方式捕捉了将研究制品与给定数据集集成并获取有效执行结果这一过程的整体目标。

### 3.1 研究数据收集

为构建一套具有代表性的高质量软件工程研究制品，我们遵循了系统的多阶段筛选流程：

**制品识别**。我们首先收集了 2024-2025 年被软件工程顶级会议（FSE、ICSE、ASE 和 ISSTA）接受的论文，这些论文需满足以下条件之一：（i）获得可复用制品徽章，或（ii）作者明确声明提供可复用制品。这两条规则确保所选制品满足基本的可用性和可复用性标准。

**语言筛选**。为确保一致且可管理的实验环境，我们仅保留使用 Python 实现的制品。这一选择便于可靠地部署实验环境，并将我们的研究焦点隔离于环境配置挑战之外——这一问题已在先前的研究中得到了广泛探讨 [4]。

**评估范围**。我们进一步筛选制品，仅保留符合以下条件的制品：（i）在至少两个数据集上评估单一技术，或（ii）在新提出的数据集上评估两种或以上最先进（SOTA）技术。这一标准确保所选制品包含足够多的适用于我们研究问题的组件。

最后，两名领域专家独立审查了所选制品及其配套论文。最终选择两个项目（即 ROCODE [15] 和 LogHub 2.0 [16]）用于后续实验。

### 3.2 数据准备

第 3.1 节中收集的各制品仓库均包含两个或以上由作者适配到相应方法的数据集。对于每个仓库，我们选择一项适配作为目标任务，其原始实现作为数据集适配任务的基准真实值（ground truth）。为构建适配任务，我们首先将目标数据集仓库与相应方法的仓库进行集成。

**语言识别 / Language Identification:**

该段落为**英语 (English)**。

---

**翻译 / Translation:**

(中文)

具体做法是：(1) 将数据集仓库 $R_D$ 复制到技术仓库 $R_T'$ 的目录中，或 (2) 将技术仓库 $R_T$ 复制到数据集仓库 $R_D'$ 的目录中。两个仓库的路径均在提示词中明确指定，以确保智能体能够定位并修改相关文件。

随后，我们移除与目标数据集相对应的实现，同时保持其他适配内容完整。具体而言，如果多个数据集适配以独立文件的形式实现，则可以明确检测并移除它们；如果多个适配共享通用配置文件，我们仅移除与目标数据集特定的条目或选项。这确保了仓库对剩余数据集仍可运行，但缺少该数据集适配的代码。因此，处理后的仓库模拟了真实的适配场景。

接下来，期望多智能体系统生成我们已移除的实现。已移除的实现被单独保存作为真值，用于评估智能体生成或修改的代码是否能正确复现原始的数据集适配。此外，其他数据集适配相关的文件将作为参考代码保留在各个仓库中。这些代码文件后续将用于 RQ1 步骤1（读取所需文件）和 RQ2 提示词2（提供参考代码）。

3.3

研究设计

3.3.1 RQ1：SOTA多智能体系统的表现如何？首先，我们旨在评估SOTA多智能体系统在该流程各阶段的表现。每个多智能体系统均配备如第3.2节所述准备好的处理仓库，以及一个简单的提示词，其中指定了适配任务、$R_D$ 和 $R_T$ 的位置以及其他必要信息。系统完全运行完成后，我们记录其完成状态。

我们的初步实验表明，大多数被评估的多智能体系统未能完成分配的任务。在此情况下，几乎所有结果都将被归类为失败，因此仅分析端到端性能的参考价值有限。为了更具体地了解每个智能体在整个过程中的行为，我们将整体任务手动分解为五个不同的步骤。

1) 读取所需文件。在修改代码或为仓库生成命令之前，多智能体系统必须浏览仓库中的必要文件以了解其架构。为了评估多智能体系统识别这些必要文件的能力，两名人类开发者尝试了数据集适配任务并记录了他们访问的文件。这些文件被视为必要文件。随后，我们跟踪多智能体系统的MCP使用记录，以确定智能体读取了哪些文件，并将该列表与必要文件集合进行比较。

该文本为**英文**。

以下是中文翻译：

文件。只有在系统一开始读取了所有必要的文件后，才认为其完成了第一步。

2). 编辑和创建必要的文件。在对代码库有了整体了解后，多智能体系统应通过创建或编辑其中的文件来修改代码库，这是数据集适配任务的核心步骤。我们通过与第3.2节中描述的基准版本进行比较来识别所需文件。为了细粒度地评估多智能体系统的性能，我们将此步骤分为两个子步骤。首先，系统必须编辑现有的必要文件并创建任何缺失的文件。我们通过追踪系统的MCP使用记录并确定哪些文件被编辑或创建来评估此子步骤。其次，多智能体系统创建或编辑的代码文件必须在功能上与基准版本一致。我们通过在代码文件中设置多个检查点来评估此子步骤。系统在每个检查点打印选定的中间输出以检查变量值，并与基准版本进行比较。考虑到为所有必要文件生成完全正确的代码极具挑战性，如果多智能体系统完成了第一个子步骤并为部分（而非全部）必要文件生成了正确的代码，我们将其评级为“部分通过(P)”。

3). 生成并执行命令。修改代码文件后，多智能体系统应生成脚本（如bash文件）或命令来实现数据集中评估该技术的流程。多智能体系统还应通过与终端交互来自动执行这些脚本或命令。我们同样将此步骤分为两个子步骤。第一个子步骤评估生成的脚本或命令是否与基准版本等效。例如，如果系统生成了一个名为run_humaneval.sh的脚本，我们会检查该脚本是否包含等效的命令序列，以执行预测流程并随后触发评估程序，如同基准bash文件中的那样。第二个子步骤衡量多智能体系统自动执行脚本或命令的能力。具体来说，如果系统能够自主执行其自身生成的命令，无论该命令本身是否正确（即无论子步骤1是否满足），则该案例被视为成功。

4). 验证与修复。由于多智能体通常很难一轮就生成完全正确的代码，因此此步骤重点关注多智能体系统验证中间和最终输出、识别并纠正第2步中产生的代码问题的能力。我们进一步将此步骤分为两个子步骤。首先，多智能体系统需要识别bug的存在，这通常通过为关键模块设计有针对性的测试输入并对数据集的一个子集运行小规模测试来实现。这些测试有助于验证这些模块是否按预期工作。在基准测试场景中，我们通过向关键函数传递特定输入来验证输出是否正确。如果多智能体系统能够自行检测并修复问题，则该子步骤被视为成功。



---

## 论文 12

# SWE-MERA: A Dynamic Benchmark for Agenticly Evaluating Large Language Models on Software Engineering Tasks

**作者**: Pavel Adamenko, Mikhail Ivanov, Aidar Valeev, Rodion Levichev, Pavel Zadorozhny

**arXiv**: https://arxiv.org/abs/2507.11059

---

该文本是**英语**（English）。

---

**中文翻译：**

SWE-MERA：用于在软件工程任务中动态评估大语言模型的代理基准测试

**摘要**

大语言模型（LLMs）在软件工程领域的快速发展揭示了现有基准测试的关键局限性，尤其是广泛使用的SWEbench数据集。最近的研究发现了严重的数据污染问题，例如SWEbench（Jimenez等人，2023年）报告称32.67%的成功补丁涉及直接解决方案泄露，31.08%由于测试用例不足而通过。我们推出了SWE-MERA，这是一个动态持续更新的基准测试，旨在通过自动收集真实世界的GitHub问题并实施严格的质量验证来应对这些基本挑战。我们的方法实现了一个可靠的流程，在确保质量的同时最大程度降低污染风险，目前可收集约10,000个潜在任务，其中300个样本已可用。使用Aider编码代理的评估表明，在最先进的模型中具有强大的区分能力。我们报告了从2024年9月到2025年6月收集的任务上对十几个最新大语言模型的性能评估。

**引言**

真实软件开发过程的复杂性不仅限于完成代码。它涵盖编码代理和一系列文本到代码的任务。例如，SWE-bench（Jimenez等人，2023年）创建自包含2,294个GitHub问题及其相应拉取请求（PRs）的数据集。SWE-bench中的每个任务代表一个真实的现实世界问题，其结构围绕：1）初始提交（修改前的代码），2）修复提交（问题的解决方案），3）问题描述（需要修复的内容）。

现有基准测试存在几个普遍问题：数据泄露——基准测试中的问题可能已存在于模型的训练数据中，导致评估结果不准确；过拟合过时的例子——模型可能记忆而非真正理解任务；基准饱和——随着最先进模型接近满分，基准测试失去区分有意义进展的能力。SWE-MERA通过动态更新测试用例来应对这些（许多代码基准测试所特有的）缺陷。定期用新的、未见过的问题刷新数据集确保：1）真实世界相关性——任务反映软件开发的最新挑战；2）公平评估——模型在新鲜问题上进行测试，最大限度降低数据泄露风险；3）持续改进——基准测试与人工智能和软件工程实践的进步同步发展。

本文的贡献如下：

1. 一个七阶段流程，有效确保质量并最小化污染风险，能够收集约10,000个潜在任务，其中300个样本目前可用。
2. 基于Aider编码代理的自动化评分系统和动态用户排行榜。

这段文字是**英语**（English）。

以下是翻译成的中文学术版本：

---

该基准测试的一个关键局限性在于其静态特性——任务仅收集一次，从未更新。这导致了两个主要问题。数据泄露：随着模型在同一个固定数据集上被反复测试，它们可能会无意中记忆相关内容。

相关工作

SWE-bench 引入了一个半自动化的流水线，从流行的开源 Python 仓库中挖掘软件工程任务，形成了一个包含 2,294 个问题及其对应拉取请求的基准测试。尽管这实现了大规模评估，但该数据集存在质量问题，包括任务规范不明确和测试覆盖率不足，这些问题损害了模型评估的可靠性。为了提高数据质量，SWE-bench Verified3 发布了一个人类验证的 500 个任务子集，但这种方法的可扩展性有限。进一步的工作，如 SWE-Bench+（Aleithan 等，2024）发现，由于问题或拉取请求描述中存在解决方案泄露，原始数据集中的大部分解决方案可以被“作弊”，这凸显了数据污染的风险，因为大多数问题的发生时间早于大型语言模型的重要知识截止日期。SWE-Bench+ 通过过滤截止日期后的任务并移除存在泄露解决方案的实例来解决这些问题，从而形成了一个更加稳健的基准测试。

3.1

为了确保任务生成过程的透明性和可复现性，我们设计了一个文档完善且易于获取的收集流水线。该流水线每月执行一次，能够实现任务的系统化和持续收集。通过遵循这一时间表，我们促进了数据集的定期更新和扩展，确保其保持最新状态并反映软件工程领域的持续进展。

该流水线包含以下步骤：

1. **仓库选择**：根据预定义的标准选择 GitHub 仓库，包括至少 10 颗星和 10 个分支的阈值、当年内的近期活动、Python 作为主要编程语言，以及存在开源许可证。

2. **问题-拉取请求映射构建**：根据以下标准构建问题与拉取请求之间的映射：
   - 每个拉取请求恰好与一个问题相关联（通过直接链接或评论）。
   - 每个问题恰好与一个拉取请求相关联。
   - 拉取请求已被合并。
   - 相关问题已被关闭。
   - 拉取请求的合并日期晚于上个月的第一天。

3. **元数据提取与筛选**：对于每个选定的问题及其对应的拉取请求，元数据（包括标题、正文和评论）被下载并解析。如果问题标题和问题正文的组合长度少于 25 个字符，则该问题-拉取请求对将被过滤掉。

**语言识别：该段落为英语（English）。**

---

**中文翻译：**

补丁提取与验证：对于每个拉取请求（Pull Request），生成相应的 Git diff 并进行验证。仅保留同时修改源代码和测试文件的示例。此外，仅考虑修改源文件数量少于 15 个的拉取请求。

仓库构建验证：对于每个任务，我们在 Docker 容器中构建适当的测试环境。如果 pytest 至少返回一个通过的测试，则验证被视为成功。

为扩展多样性和泛化能力，MultiSWE-bench（Zan 等，2025）将覆盖范围扩展至多种编程语言。补充性方法，如 SWE-Gym（Pan 等，2024）和 SWE-smith（Yang 等，2025b），分别专注于自动任务生成和可扩展的合成数据创建，以进一步扩大基准测试的规模和多样性。

尽管这些仓库级基准测试推动了该领域的发展，但它们在很大程度上仍是静态的或需要大量人工维护。相比之下，LiveCodeBench（Jain 等，2024）开创了一个动态、频繁更新的评估框架以应对数据污染问题。然而，它主要针对算法问题，未能捕捉对于真实软件工程评估至关重要的仓库级复杂性。

**方法论概述**

SWE-MERA 任务收集流程基于真实世界软件工程挑战系统性地生成评估任务。为最大化覆盖范围，我们对公开可用的仓库进行了全面解析。对于每个选定的仓库状态，我们识别了在后续开发中引入但在当前版本中尚未通过的测试。

该框架支持客观评估：将仓库回滚至指定状态并纳入这些未来测试用例后，标记为 PASS_TO_PASS 的测试预期将成功，而标记为 FAIL_TO_PASS 的测试预期将失败。

| 步骤 | 仓库总数 | 问题总数 | 时间估算 |
|------|----------|----------|----------|
| GitHub 全量公开 | 255M | 522M | 1 秒 |
| 所有公开仓库 | 21M | 53M | 1 秒 |
| Python 仓库 | 168K | 5.7M | 7 小时 |
| 10+ 星标、10+ 分支 | 97K | 5.7M (4.2M†) | 7 小时 |
| 1+ 已关闭问题 | 110K | 5.5M | 7 小时 |
| **仓库筛选** | | | |
| Python、10+ 星标、10+ 分支、2025 年更新的仓库 | 25K | 339K | 3 天 |
| **PR-问题映射构建** | | | |
| 2025-01-01 至 2025-06-01 期间有更新的问题 | 10K | 98K | 3 天 |
| 问题已关闭、合并请求已关闭 | 8.4K | 55K | 2 分钟 |
| 问题与 PR 一一对应 | 8.2K | 51K | 11 小时 |
| **元数据提取与筛选** | | | |
| 补丁提取与验证 | 6.7K | 30K | 12 小时 |
| 仓库构建验证 | 1.6K | 9K | 3 小时 |
| **端到端任务执行** | | | |
| 基于 LLM 的流水线评估 | 500 | 1.5K | 2 天 |
| | 200 | 300 | 2 小时 |

**表 1：** 2025-01-01 至 2025-06-01 期间任务收集流程的汇总，使用 GitHub GraphQL API 计算。在我们的实验中，仓库构建验证在 PR-问题映射构建之前立即执行，以最小化总处理时间；本表仅供说明目的。

该文本为**英文**。

以下是翻译：

---

仅限已关闭的问题。

6. 端到端任务执行：每个生成的任务都在 Docker 容器内的受控环境中执行，以验证其可复现性和正确性。详细说明见附录 A。
7. 基于 LLM 的流水线评估：为了评估每个候选任务的质量，我们使用 Qwen3-32B 模型（Yang et al., 2025a）从四个维度评估描述、补丁及相关测试：任务正确性、测试正确性、测试完整性和复杂度。该模型被提示返回结构化的 JSON 响应，包含 1 到 10 的评分、置信度值（0.0–1.0）以及每个维度的简要说明（使用的提示见附录 D）。我们过滤掉在以下任一维度上处于分数分布底部四分位数（前 25%）的任务：

• 任务正确性
• 测试正确性
• 测试完整性
复杂度评分不用于过滤，因为我们明确希望保留难易程度不同的任务。此过滤步骤确保保留的任务格式正确、可解决且测试充分。
每个流水线步骤的详细结果汇总于表 1。采集的任务样本见附录 E。

3.2 可用性

整个流水线实现为 Python 包4，可针对任何 GitHub 仓库执行，便于复现和未来研究的扩展。所有任务通常使用标准化基础镜像5在 Docker 容器中执行。然而，相同的执行过程可以在 Conda 环境中复制，无需修改底层代码。

4 评估

为了将 LLM 应用于问题解决场景，我们采用了流行的代理框架 Aider，当应用于相同模型时，其表现与其他最先进框架相似。Aider 为 LLM 提供六次尝试（“tries”）来修复给定问题，每次尝试允许最多四次反思来处理 lint 或测试输出。由于向后兼容性问题，我们略微修改了 Aider 6 和 Aider-SWE-bench 7 仓库。然而，我们希望在不久的将来支持几个能够解决问题的流行框架。Aider 给予 LLM 六次连续独立的尝试来修复问题，但如果 LLM 在早期尝试中成功，则进入下一个问题。由于实验设计，我们报告两个指标：pass@1，表示第一次尝试是否成功；

图 1：两年间模型 pass@1/pass@6 指标对比。误差条表示置信区间，使用二项分布（5% 双侧分位数）计算。

---

**语言识别：**

该文本为**英语**（English），是一篇关于代码生成大语言模型（LLM）基准测试的学术/技术文档。

---

**中文翻译：**

成功与否，并在pass@6中显示六次尝试是否有任何一次成功。

为了评估基准测试的可靠性，我们选取了多个当前流行的先进代码大语言模型，包括Qwen、Devstral、DeepSeek-R1等（详见下一节的完整列表）。我们在8块NVIDIA H100 80 GB GPU上运行这些模型，但DeepSeek-R1和7B模型例外，分别在16块和4块GPU上运行。单个模型的评估平均耗时3±1小时用于Aider运行，测试最终补丁则需半小时。本实验使用了60-1.40亿个提示词tokens和3.20亿个完成词tokens，相当于每次请求使用1.4-2万个提示词tokens和1-4千个完成词tokens。

4.1

**Qwen2.5-Coder**：提供多种参数规模，可在资源消耗与性能之间实现灵活权衡。Qwen2.5 Coder模型在代码生成、代码推理和代码修复方面均有显著提升，并具备更全面的基础能力，适用于Code Agents等现实世界应用。

**Llama-3.3-70B-Instruct**¹：多语言大语言模型（LLM）是一种指令微调的生成模型。Llama 3.3指令微调文本模型针对多语言对话使用场景进行了优化。请注意，它是一款通用聊天模型，并非专为代码任务设计。

**DeepSeek-R1-0528**：由DeepSeek AI（DeepSeekAI, 2025）开发，整合了计算增强和新型后训练优化技术，显著提升了推理、推理能力和问题解决能力。该更新模型在基准测试中实现了最先进的性能，同时降低了幻觉率，并在代码生成和函数调用方面取得了进展。作为开源软件，它使先进的推理能力得以普惠大众。

**DeepSeek-R1-Distill-Qwen-32B**：是基于Qwen2.5 32B模型（Yang et al., 2024），利用DeepSeek-R1（DeepSeekAI, 2025）生成的推理数据进行蒸馏得到的模型。该蒸馏模型在其他基准测试中展现出极为出色的性能。

**QwQ-32B**：是Qwen推出的推理模型。

**Codestral-22B-v0.1**⁸：由Mistral AI训练的多语言代码模型，在80多种编程语言的多样化数据集上进行训练，包括最流行的Python、Java、C、C++、JavaScript和Bash等语言。

**Qwen2.5-Coder-7/14/32B-Instruct**模型（Hui et al., 2024）是最新设计的Qwen大语言模型，专为代码任务设计，提供多种参数规模，可在资源消耗与性能之间实现灵活权衡。Qwen2.5 Coder模型在代码生成、代码推理和代码修复方面均有显著提升，并具备更全面的基础能力，适用于Code Agents等现实世界应用。

**Llama-3.3-70B-Instruct**⁹：多语言大语言模型（LLM）是一种指令微调的生成模型。Llama 3.3指令微调文本模型针对多语言对话使用场景进行了优化。注意：它是一款通用聊天模型，并非专为代码任务设计。

**DeepSeek-R1-0528**：由DeepSeek AI（DeepSeekAI, 2025）开发，整合了计算增强和新型后训练优化技术，显著提升了推理、推理能力和问题解决能力。该更新模型在基准测试中实现了最先进的性能，同时降低了幻觉率，并在代码生成和函数调用方面取得了进展。作为开源软件，它使先进的推理能力得以普惠大众。

**DeepSeek-R1-Distill-Qwen-32B**：是Qwen2.5 32B模型（Yang et al., 2024）利用DeepSeek-R1（DeepSeekAI, 2025）生成的推理数据进行蒸馏得到的模型。该蒸馏模型在其他基准测试中展现出极为出色的性能。

**QwQ-32B**：是Qwen推出的推理模型。

---

**被评估模型**

| 模型 | pass@1 | pass@6 | 文件定位 | 生成补丁 | 回归测试 | token限制触发 |
|------|--------|--------|----------|----------|----------|---------------|
| DeepSeek-R1-0528 | 27.8% | 40.2% | 89.6% | 98.1% | 40.6% | 0.2% |
| Devstral-Small-2505 | 17.2% | 28.2% | 89.1% | 98.7% | 28.5% | 0.4% |
| Qwen3-32B | 12.3% | 26.1% | 91.8% | 98.9% | 26.1% | 1.1% |
| DeepSeek-R1-Distill-Qwen-32B | 13.2% | 23.9% | 87.8% | 98.7% | 23.7% | 0.4% |
| QwQ-32B | 11.6% | 23.7% | 79.6% | 96.6% | 22.9% | 0.6% |
| Qwen2.5-Coder-32B-Instruct | 12.9% | 22.0% | 86.3% | 96.3% | 22.0% | 4.6% |
| Qwen2.5-Coder-14B-Instruct | 7.8% | 16.9% | 86.0% | 93.7% | 16.8% | 1.9% |
| Llama-3.3-70B-Instruct | 8.7% | 14.8% | 77.0% | 70.9% | 14.8% | 0.0% |
| Codestral-22B-v0.1 | 3.8% | 8.7% | 76.7% | 83.4% | 8.4% | 2.9% |
| Qwen2.5-Coder-7B-Instruct | 2.7% | 5.5% | 58.2% | 55.3% | 4.8% | 5.7% |

---

**注释：**

¹ https://www.llama.com/docs/model-cards-and-prompt-formats/llama3_3/

⁸ https://mistral.ai/news/codestral



---

## 论文 13

# HerAgent: Rethinking the Automated Environment Deployment via Hierarchical Test Pyramid

**作者**: Xiang Li, Siyu Lu, Sarro Federica, Claire Le Goues, He Ye

**arXiv**: https://arxiv.org/abs/2602.07871

---

**语言识别 / Language Identification:**

该文本为**英语 (English)**。

---

**翻译 / Translation:**

HerAgent：通过层级测试金字塔重新审视自动化环境部署

arXiv:2602.07871v1 [cs.SE] 2026年2月8日

**作者信息：**
XIANG LI，英国伦敦大学学院
SIYU LU，瑞典乌普萨拉大学
SARRO FEDERICA，英国伦敦大学学院
CLAIRE LE GOUES，美国卡内基梅隆大学
HE YE，英国伦敦大学学院

**摘要**

自动化软件环境搭建是测试、调试和复现故障的先决条件，然而由于复杂的依赖关系、异构的构建系统以及不完整的文档，实际操作中仍然具有挑战性。近期研究利用大语言模型来实现这一过程的自动化，但通常使用依赖安装或部分测试执行等弱信号来评估成功与否，而这些信号并不能确保项目能够实际运行。

在本文中，我们认为环境搭建的成功与否应通过可执行证据而非单一的二元信号来评估。我们提出了环境成熟度层级（Environment Maturity Hierarchy），该层级基于逐步增强的执行要求定义了三个成功等级，最终以项目主入口点的成功执行作为最高标准。

在此层级的指导下，我们提出了HerAgent，这是一种自动化环境搭建方法，通过基于执行验证和修复的方式增量构建可执行环境。我们对HerAgent在四个公开基准上进行了评估，其表现优于所有相关工作，由于其对项目结构和依赖关系的整体理解，实现了高达79.6%的改进。在复杂的C/C++项目中，HerAgent超越了先前的方法达66.7%。此外，HerAgent独特地解决了基准测试中11-30个先前方法无法配置的环境实例。

**CCS概念：** • 软件及其工程 → 应用特定开发环境

**关键词与短语：** 软件项目环境搭建、依赖、构建

**ACM参考格式：**
Xiang Li, Siyu Lu, Sarro Federica, Claire Le Goues, He Ye. 2026. HerAgent：通过层级测试金字塔重新审视自动化环境部署。2026年2月，21页。

---

**1 引言**

自动化软件环境部署[1, 4, 10, 15, 19, 22]是顺利运行软件项目的必要步骤。它包括安装依赖、构建项目以及运行测试用例以确保软件在特定环境中能够正常工作。正确的环境部署是调试[42]、测试[38]和复现故障[20, 33]等任务所必需的。

## 语言识别

这段文字是**英文**（English）。

---

## 中文翻译

英国伦敦，伦敦大学学院，he.ye@ucl.ac.uk。

允许以个人或课堂教学为目的免费制作本作品的数字或纸质副本，无需付费，但前提是副本不得用于盈利或商业目的，且副本须在首页标注本通知及完整引用信息。本作品其他所有者所有的组件版权须予以尊重。经授权可进行摘要及引用。如需复制、发布至服务器或重新分发至列表，须事先获得特定许可和/或付费许可。请联系 permissions@acm.org 了解许可事宜。

© 2026 版权由所有者/作者保留。出版权已授权至 ACM。
ACM XXXX-XXXX/2026/2-ART
https://doi.org/10.1145/nnnnnnn.nnnnnnn
第 1 卷，第 1 期，文章编号：。出版日期：2026年2月。

---

**Xiang Li 等人**

推动项目环境部署自动化的动机主要源于两个方面。

首先，它能够缓解因复杂技术栈和过时文档而导致的手动配置摩擦。实证研究强调了这一挑战，表明由于可复现性问题，38%至60%的Java构建在模拟或变体环境中会失败[27, 28]。此外，自动化解决方案（如CI/CD）对于解决"可复现性危机"至关重要，它能够确保执行上下文的一致性，而非依赖模糊的手动流程[2]。其次，这是编码智能体[3, 5, 29, 47]的先决条件——编码智能体需要首先构建可执行环境，以通过编译和测试提供反馈。没有可用的环境，智能体就无法验证更改或观察失败，因此自动化部署成为基于智能体的软件工程的核心支柱。

大型语言模型（LLM）的最新进展显著提升了自动化环境部署的能力。在这些方法中，LLM主要负责推理所需依赖项和测试执行命令[4, 22]。先前的工作探索构建Docker容器[14, 15]或生成Shell脚本[19]来自动部署项目，并通过静态分析或测试套件执行来评估结果。

先前的工作很有前景，但尚不完善。现有的自动化软件环境部署方法存在三个关键局限性：

**问题1**：先前的工作对环境部署成功的定义缺乏一致性和完整性。先前的工作将环境部署成功等同于成功构建、静态分析（如PIPER[19]和EnvBench[9]），或能够调用测试框架（如Repo2Run[15]和Installmatic[22]）。这些标准未能验证程序的主执行入口点。在实践中，人类开发者[18]和编码智能体[35, 40, 47]都希望了解如何运行该程序。

**问题2**：先前的工作缺乏对项目的整体理解，导致效果有限且可扩展性差。先前的工作通常通过逐一响应个别编译或测试错误来构建环境，rat

**语言识别：**
该段落为**英文**（English）。

---

**中文翻译：**

与将代码仓库作为一个整体进行推理不同，此前的工作[4, 15, 19, 22]仅在观察到编译失败后才会下载缺失的依赖项，而未考虑项目的依赖图或执行工作流。因此，环境部署由本地失败信号驱动，导致重复错误、脆弱的修复方案，且在复杂或异构代码仓库上的可扩展性有限。

问题3：此前的工作依赖于关于特定项目结构的强假设且评估规模较小，降低了外部有效性。此前的工作通常针对具有固定仓库结构或预定义环境构建模式的项目设计，例如围绕pytest[15, 22]组织的Python项目或基于pyright[9, 19]的工作流。因此，评估往往在小规模上进行。例如，ExecutionAgent[4]在50个实例上进行了评估，Installmatic[22]在40个实例上进行了评估。鉴于现实世界代码库在语言、结构和执行工作流方面的多样性，这些方法的外部有效性仍不明确。

我们的解决方案——HerAgent：为解决上述问题，我们提出HerAgent，一个用于软件环境部署的自动化框架。针对问题1，HerAgent引入了环境成熟度层次结构，定义了环境部署的三个成功信号：可安装性（Installability）、可测试性（Testability）和可运行性（Runnability）。该层次结构明确识别项目的主要执行入口点以确保项目能够运行。这比简单地执行pytest或mvn test等命令更具挑战性。针对问题2，HerAgent首先使用知识图谱分析项目结构，以获得对项目组件和依赖关系的整体理解。这种设计与此前的工作根本不同，使HerAgent能够更有效地处理复杂项目。针对问题3，HerAgent设计为能够跨不同项目结构进行泛化，不受特定仓库约定的约束，例如以pytest为中心的布局。

---

*Vol. 1, No. 1, Article . Publication date: February 2026.*

---

**HerAgent：通过层级测试金字塔重新思考自动化环境部署**

**EnvBench统计数据**

**环境就绪状态**

**JVM项目**

| 类别 | 指标 |
|------|------|
| 可运行性 | 用户就绪：主入口、集成测试 |
| | 开发就绪：冒烟测试、单元测试 |
| 可测试性 | 设置就绪：构建命令 |
| 可安装性 | main() 40.2% (265)、Spring Boot 13.7% (90)、web.xml 7.7% (51)、应用配置 8.2% (54)、MANIFEST 5.9% (39) |
| | junit_test_files 88.0% (580)、groovy_spec 2.7% (18)、feature_files 0.0% (0)、integration_tests 0.2% (1) |
| | gradle 63.0% (415)、pom.xml 42.0% (277)、mvnw 14.1% (93) |

**Python项目**

| 类别 | 指标 |
|------|------|
| CLI | keywords 58.6% (190)、__main__.py 22.2% (72)、Web入口 26.5% (86)、框架入口 1.5% (5) |
| 测试框架 | test_folders 88.3% (286)、pytest 63.9% (207)、unittest 3... |

**语言识别 / Language Identification:**

该段落为**英文 (English)**。

---

**翻译 (Translation - English to Chinese):**

1.5%（102）

覆盖率 21.0%（68）

假设 2.2%（7）
pyproject.toml 64.5%（209）

requirements.txt 42.6%（138）

poetry.lock 13.6%（44）

environment.yml 2.5%（8）

图 1. 环境成熟度层级与生态系统现实。左侧：三阶段成熟度模型。右侧：659 个 JVM 仓库和 324 个 Python 仓库在 EnvBench [9] 中的命令分布。玫瑰图展示了在可安装、可测试和可运行层级上的依赖项和测试套件的丰富多样性。

HerAgent 在四个自动化环境部署基准上进行了广泛评估：
EnvBench [9]、Repo2Run-Bench [15]、ExecutionAgent-Bench [4] 和 Installamatic-Bench [22]。
该评估涵盖了 14 种不同的编程语言，并与四种相关方法（PIPER [19]、ExecutionAgent [4]、Repo2Run [15] 和 Installamatic [22]）以及前沿闭源模型（GPT 系列）和开源模型（Qwen 系列）进行了比较。我们的实验结果表明，HerAgent 在所有四个基准上均优于所有相关工作，最高提升达 79.6%。这一性能提升归功于 HerAgent 对项目结构和依赖项的整体理解能力。特别是在复杂的 C 和 C++ 项目中，HerAgent 优于 66.7% 的先前方法。综上所述，我们的贡献如下：

• 概念创新：我们提出了层级测试金字塔，该框架系统地定义了三个评估指标，以表征自动化项目环境部署的成功级别。据我们所知，这是首次在该领域提供统一的成功定义。

• 方法创新：我们提出了 HerAgent，它直接从代码库整体理解项目结构和依赖项，而非依赖反应式的、错误驱动的环境构建。这种整体设计使 HerAgent 能够有效处理复杂项目，尤其是在 C 和 C++ 领域表现优异，优于先前工作最高达 66.7%。

• 最先进性能：我们对 HerAgent 进行了全面的自动化环境部署基准评估，并与代表性先前方法以及前沿闭源和开源模型进行比较，证明了其在所有基准上的最先进性能。

• 成果可用性：我们发布了所有代码、执行轨迹和实验结果，以促进可复现性并支持后续研究 https://github.com/EuniAI/EnvAgent。

2.1

问题陈述
环境成熟度层级

我们首先手动检查了 EnvBench [9] 中的 659 个 JVM 项目和 324 个 Python 项目，以检查真实仓库中构建、测试和执行命令的使用情况（图 1，右侧）。这种手动分析揭示了命令在实践中的明显分层：依赖项安装、测试执行和运行应用程序对系统的考验程度不同，并且提供了关于项目是否真正可运行的不同置信度。

The provided text is in **English**.

---

**Translation (English → Chinese):**

基于这一观察，我们引入了环境成熟度层次结构（图1，左），该结构根据可执行命令所提供的环境就绪程度证据，将它们组织为三个层级。与将所有测试一视同仁不同，该层次结构形成了测试金字塔，其中层级越高代表执行越接近真实使用场景，从而提供更强的保障。现在，我们介绍用于自动化环境部署的这三个成功指标。

**可安装性**：此指标用于判断在给定平台和工具链下，声明的依赖项是否能够成功安装。如果环境能够执行构建或安装命令（如mvn install或pip install -r requirements.txt），则被视为已准备好进行设置。即使在这一基础层面，仓库在构建系统和依赖规范方面也表现出显著的多样性，跨越JVM和Python项目。虽然这些命令确认了依赖项的可安装性，但此层面并未提供程序在运行时能够运行的证据。

**可测试性**：如果环境能够执行面向测试的命令（如冒烟测试或单元测试，例如pytest、mvn test或简单的–version检查），则满足此指标。这些命令会调用运行时，并提供环境功能有限的证据。在实践中，仓库暴露了多样化的测试命令和结构，且测试通常依赖于模拟或避免完整的执行路径。此层面提供了程序能够正确运行的有限证据。大多数先前的工作都聚焦于这一层面，并将其视为成功。

**可运行性**：当程序的主入口点或集成工作流能够成功执行时，便达到此层级，例如运行python main.py、启动命令行界面，或执行与外部服务交互的集成测试。虽然这些执行命令多种多样，但它们具有一个共同属性：它们在真实条件下端到端地运行系统。此层级的成功执行表明依赖项、配置和组件交互能够协同工作。因此，我们将主入口点执行和集成测试视为达到此状态的等效证据。此层级代表完全配置好的环境。

**2.2 成功的形式化定义**

环境成熟度层次结构提供了环境就绪程度渐进的概念视图。为了清晰描述不同状态之间的切换过程，我们现在通过定义状态感知成功标准来形式化这一概念。

设S = {可安装性, 可测试性, 可运行性}表示环境成熟度状态的集合。这些状态按照执行保障的增加形成偏序关系：

可安装性 ⊊ 可测试性 ⊊ 可运行性

该关系表示层级依赖关系：实现更高状态本质上需要较低状态的能力（必要性），而……



---

## 论文 14

# SWE-smith: Scaling Data for Software Engineering Agents

**作者**: John Yang, Kilian Lieret, Carlos E. Jimenez, Alexander Wettig, Kabir Khandpur

**arXiv**: https://arxiv.org/abs/2504.21798

---

## 语言识别

这段文字是**英文**（English），是一篇关于软件工程领域机器学习训练的学术论文摘要和引言部分。

---

## 中文翻译

**SWE-smith：面向软件工程智能体的规模化数据构建**

John Yang¹, Kilier Lieret², Carlos E. Jimenez², Alexander Wettig², Kabir Khandpur³,
Yanzhe Zhang¹, Binyuan Hui⁴, Ofir Press², Ludwig Schmidt¹, Diyi Yang¹

¹ 斯坦福大学
² 普林斯顿大学
³ 独立研究者
⁴ 阿里巴巴通义千问

尽管语言模型（Language Models, LMs）在软件工程领域近期取得了显著进展，但训练数据的收集仍然是一个主要痛点。现有数据集规模较小，最多只包含来自11个或更少GitHub仓库的数千个训练实例。构建此类数据集的流程通常十分复杂，需要数百小时的人工劳动；配套的执行环境也需要占用数TB的存储空间，严重限制了其可扩展性和可用性。为解决这一痛点，我们提出了SWE-smith，这是一种用于大规模生成软件工程训练数据的新型流水线。对于任意Python代码库，SWE-smith会构建相应的执行环境，然后自动合成数百至数千个能够破坏代码库中现有测试的任务实例。利用SWE-smith，我们创建了一个包含5万个实例的数据集，源自128个GitHub仓库，规模比所有先前工作高出一个数量级。我们训练了SWE-agent-LM-32B模型，在SWE-bench Verified基准测试上达到了40.2%的Pass@1解决率，在开源模型中处于领先地位。我们将SWE-smith（采集流程、任务实例、轨迹数据、模型权重）开源，以降低LM系统在自动化软件工程领域研究的准入门槛。所有资源可访问https://swesmith.com。

### 1 引言

语言模型智能体，如SWE-agent（Yang et al., 2024a）或OpenHands（Wang et al., 2024），在自动化软件工程（Software Engineering, SE）任务方面取得了显著进展，如SWE-bench（Jimenez et al., 2024b）基准测试所追踪的那样。然而，最有效的智能体仍然依赖于闭源语言模型。另一方面，构建用于软件工程的开源语言模型仍然受限于缺乏大规模、高质量的训练数据。为确保开源研究在该领域保持相关性，开发大规模收集软件工程训练数据的基础设施至关重要。

图1：SWE-smith在任务实例扩展（左）和性能提升（右）方面的表现。利用SWE-smith，我们可以为任意Python代码库创建数百至数千个实例，从而训练出在SWE-bench Verified上达到40.2%的SWE-agent-LM-32B。左图中的虚线预测了每个策略在最多250个仓库下所能创建的实例数量。

语言：该段落为**英语**（English）。

---

**译文：**

README.rst
setup.py

单元测试
tests/test_api.py
tests/test_auth.py
tests/test_client.py
tests/test_utils.py

环境构建

任务生成策略

SWE-agent

程序化

修改

尝试安装代码库
并执行测试

开发者
基于SWE-agent工作
编写Dockerfile

新任务实例
环境镜像

合成任务

大语言模型生成

生成的问题

组合缺陷

有缺陷的补丁 +20 -12

PR镜像

验证测试

2
9

图2：SWE-smith通过在真实代码库中植入缺陷来为软件工程智能体创建训练数据。给定一个代码库，我们采用多种策略来创建能够破坏现有测试的任务实例。使用SWE-smith，我们从128个真实世界代码库的执行环境中创建了超过5万个任务实例。

当前的开源软件生态系统中存在两类用于训练大语言模型完成软件工程任务的数据来源。一种简单的方法是从GitHub代码库中抓取拉取请求（PR）。然而，由于缺乏执行环境或测试，这些实例无法提供可靠的方式来验证生成的解决方案，大语言模型只能从代码的表面形式学习（Xie et al., 2025a），或通过基于表面字符串相似度的奖励来学习（Wei et al., 2025）。相比之下，SWE-bench通过针对提议的解决方案运行单元测试来提供可靠的验证。另一项工作只是将SWE-bench的采集策略扩展到一组新的代码库用于训练目的（Pan et al., 2024）。这为训练和提炼大语言模型智能体产生了灵活的环境，因为我们可以生成智能体轨迹并根据单元测试结果对其进行筛选。然而，这种方法的可扩展性受到SWE-bench采集策略相关挑战的严重限制。SWE-bench的筛选过程只保留了一小部分PR，这些PR不仅解决了GitHub问题，还对单元测试进行了有意义的修改。此外，为每个实例设置执行环境需要大量人工干预。

在本文中，我们推出了SWE-smith工具包，它将SWE-bench的灵活执行环境与可扩展的实例采集相结合（图1）。SWE-smith具有多种技术来自动合成现有GitHub代码库中的缺陷，例如：（1）使用大语言模型生成错误的函数重写，（2）程序化修改函数的抽象语法树（AST），（3）撤销PR，以及（4）组合缺陷。我们的关键洞察是，基于执行的验证不仅可以验证提议的解决方案，还可以识别导致严重软件回归（即破坏测试）的缺陷候选。

简而言之，SWE-smith提出了如图2所示的任务创建工作流程。给定一个代码库，我们使用SWE-agent（Yang et al., 2024a）自动设置相应的环境。然后在该环境中，我们使用上述技术合成数百到数千个任务实例。最后，我们......

**语言识别：该段落为英语（English）**

**翻译（中文）：**

利用语言模型自动生成逼真的问题描述。SWE-smith的设计显著降低了构建执行环境所需的人力劳动和存储成本。使用SWE-smith，我们在128个真实GitHub仓库中创建了50,000个任务实例的数据集。我们使用SWE-smith训练有效的语言模型。我们在20,000个任务实例上运行SWE-agent（使用Claude 3.7 Sonnet），收集了5,016条专家轨迹。我们基于这些轨迹对Qwen 2.5 Coder Instruct 32B模型进行微调，得到了SWE-agent-LM-32B模型。该模型在SWE-bench Verified上单次尝试即可达到40.2%（+33.4%）的准确率，且无需推理时的规模化扩展。在32B模型规模下，SWE-agent-LM-32B取得了最先进的结果。

SWE-smith数据集的规模和多样性使我们能够开始确立关于软件开发智能体开发的真知，并探究有趣的现象。在更多实例、缺陷类型和仓库上进行训练有所帮助。语言模型生成的问题文本能够有效地逼近真实问题。我们发现，使用SWE-smith可以优化语言模型使其在特定仓库上表现出色，同时仅遭受轻微的泛化损失。

我们将SWE-smith作为开源工具包发布——包括实例、环境和轨迹——以推动更强开源语言模型智能体的发展。

---

**SWE-smith：面向软件工程智能体的规模化数据生成**

SWE-smith收集策略的核心原则是首先定义执行环境，然后在该环境内合成任务实例。从概念上讲，这与SWE-bench的方法简单相反——SWE-bench优先识别任务实例，然后尝试为每个实例构建环境。在本节中，我们将详细描述该过程，并展示在实际中，SWE-smith在仓库、任务实例和存储方面是如何实现显著规模化扩展的。

**2.1 收集**

**构建具有通过测试的仓库执行环境。** 给定一个仓库，我们在最新提交上运行SWE-agent（Yang等人，2024a），最多100步，指示其安装代码库并运行测试套件。然后我们手动验证安装和测试指令，检查是否超过80%的现有测试通过，最终为该仓库创建Docker镜像。我们以2024年11月18日的Python包索引（PyPI）中下载量前5,000的包为目标，按GitHub星数对PyPI包进行排序，然后移除星数少于1,000的所有PyPI包，以及所有12个SWE-bench测试仓库。详见§A.2。

**创建任务实例候选。** 对于每个仓库，我们采用四种不同策略来创建候选实例。如图2所示，每种策略接收一个仓库作为输入，然后生成以.diff文件表示的任务实例候选。详见§B的详细内容。

• **语言模型生成：** 对于每个仓库，我们识别所有编程实体（函数）

这段文字是**英文**。以下是其**中文翻译**：

---

接下来，我们采取两种方法：（1）向语言模型提供该函数并提示其引入错误修改（以下简称"LM Modify"），以及（2）仅给定函数头和文档字符串，要求语言模型重写该函数（以下简称"LM Rewrite"）。详见§B.1。

• 程序化修改：对于每个函数，我们获取其抽象语法树（AST）表示，然后随机执行一个或多个转换（例如，移除条件语句/循环、改变运算符，另有11种更多操作。详见表8）。详见§B.2。

• 组合缺陷：语言模型生成和程序化修改任务实例仅编辑一个函数或类。为了创建需要编辑代码库多个部分的更复杂任务，我们设计了一种"补丁组合"策略，通过聚合来自同一文件或模块的候选来创建任务实例。详见§B.3。

• 反转PR（或"PR镜像"）：对于每个仓库，我们收集所有修改Python文件的PR。对于每个PR，我们尝试撤销其在仓库当前版本中的修订。为此，我们向语言模型提供PR的代码更改（.diff纯文本），并提示其重写每个受影响的文件以撤销PR的编辑。与SWEbench不同，我们不会检出PR的基础提交，因为上一步确定的安装规范可能与仓库的旧版本不兼容。详见§B.4。

基于执行的候选验证。我们将每个候选补丁应用到相应的仓库，运行测试套件，并仅保留那些破坏一个或多个现有通过测试的补丁（称为Fail-to-Pass或F2P测试）。为提高效率，我们还将测试运行时间限制为两分钟；导致测试运行时间超过此时间限制的缺陷候选将被丢弃。更多细节见§A.3。

问题陈述的生成。与缺陷相关的问题文本可能会显著改变任务实例的难度和可行性。问题文本中关于"预期"与"观测"行为的详细描述或缺陷复现代码会严重影响智能体定位缺陷或迭代所提出解决方案的能力。我们探讨了§D中全面介绍的几种技术，最终采用了一种简单的策略。对于每个任务实例，我们向语言模型提供.diff补丁、一个随机F2P测试的源代码以及应用缺陷补丁后运行仓库测试套件的执行输出。我们提示语言模型生成包含基于F2P测试的复现代码的GitHub issue风格文本。

还需要哪些人工劳动？需要人工努力的步骤是（1）从智能体轨迹中解析正确的安装设置程序（每个仓库约7分钟），以及（2）

---

**SW E-smith：为软件工程智能体扩展数据**

系统工具（24）
数据管理（52）
代码工具（15）
网页开发（18）
数据可视化（6）
机器学习/人工智能（14）

300 600 900 1200 1500

缺陷类型

产出率%

#安装数

成本

F2P

代码行数

组合

LM修改

LM重写

PR镜像

程序化

96.9%

56.0%

35.0

**语言识别**：该段落为**英语**（English）。

---

**翻译（简体中文）**：

%
33.8%
40.2%

10, 092
17, 887
4, 173
2, 344
15, 641

0.00¢
0.38¢
3.93¢
5.53¢
0.00¢

15
4
4
3
7

11
3
24
14
5

总计

50.1

50,137

2.32¢

6

5

表1：SWE-smith统计数据摘要。"收益率%"表示由某策略生成的候选样本中通过1个以上测试的比例。"成本"表示生成单个候选样本的平均成本。"F2P"（失败转通过）、"修改行数"均为中位数。
图3：128个代码仓库的任务实例分布，按6个类别分组。
实现测试输出解析器需要约每个仓库1分钟。第二步耗时极少，因为具有相同测试基础设施（如pytest）的仓库可以复用解析器。SWE-smith消除了手动确定跨时间轴多个版本代码库的安装规范的需求，这是SWE-bench数据收集中成本最高的步骤。创建SWE-smith花费了一位作者约20小时的人工工作。

我们将SWE-smith应用于128个Python仓库，共生成5万个任务实例。表1总结了关键统计数据。平均每个仓库生成381个任务实例，最多可达2277个（pandas-dev/pandas）。图3展示了每个仓库的任务实例分布，仓库被归类为六个通用类别之一。

错误生成策略在成本和收益率上存在差异。在依赖语言模型的方法中，PR Mirror成本更高，因为任务涉及重写整个文件，而LM Modify和LM Rewrite则仅针对单个函数。收益率受限于测试覆盖不足或错误候选样本未能实际引入相关问题。例如，对于LM Rewrite，语言模型被要求重新实现函数，而非显式引入错误。当明确要求引入错误时（LM Modify），收益率更高。

SWE-smith任务实例的难度如何？作为SWE-bench Verified筛选工作的一部分，Chowdhury等人（2024）对1699个SWE-bench任务实例进行了人工标注，分为四个难度等级：≤15分钟、15分钟至1小时、1-4小时和4小时以上。我们将这些评级映射为三个标签：简单（≤15分钟）、中等（15分钟至1小时）和困难（1小时以上）。然后，我们对Qwen 2.5 32B Instruct模型进行LoRA微调（Hu et al., 2021），以便根据解决方案/错误补丁和issue文本分配难度标签。1699个实例按80/20划分为训练集和测试集，经过监督微调的模型在测试集上达到68.24%的准确率。为量化难度，我们为简单/中等/困难分别分配1/5/9分。使用该模型，我们可以估算SWE-smith及整个数据集的难度，如图4所示。详见附录E。

5.01

SWE-b
(2294)

3.89

3.96

6.04

SWE-b SWE-b SWE-b
Lite Verified MM
(300)
(500)
(510)

5.62

3.3

SWEgym
(2438)

5.27

3.6

4.88

5.72

LM
LM 程序化 PR 组合
修改 重写 (1000) Mirror (1000)
(1000)
(1000) (1000)

图4：现有SWE-bench风格数据集（左起5个柱状图）及SWE-smith任务实例难度（简单/中等/困难）的分布。



---

## 论文 15

# SWE-bench-java: A GitHub Issue Resolving Benchmark for Java

**作者**: Daoguang Zan, Zhirong Huang, Ailun Yu, Shaoxin Lin, Yifan Shi

**arXiv**: https://arxiv.org/abs/2408.14354

---

The provided text is written in **English**.

---

**翻译 (Translation):**

## SWE-BENCH-JAVA：GitHub问题解决基准测试

arXiv:2408.14354v1 [cs.SE] 2024年8月26日

**摘要**

GitHub问题解决是软件工程中的一项关键任务，近年来在工业界和学术界都获得了广泛关注。在这一任务中，SW E-bench[1]被发布用于评估大型语言模型（LLM）的问题解决能力，但目前仅聚焦于Python版本。然而，支持更多编程语言也非常重要，因为工业界存在强烈需求。作为迈向多语言支持的第一步，我们开发了SWE-bench的Java版本，称之为SWE-bench-java-verified。我们公开了该数据集，以及相应的基于Docker的评估环境和排行榜，这些将在未来几个月内持续维护和更新。为验证SWE-bench-java-verified的可靠性，我们实现了经典方法SWE-agent[2]并在其上测试了多个强大的LLM。众所周知，开发高质量的多语言基准测试既耗时又费力，因此我们欢迎通过拉取请求或合作方式来贡献力量，以加速其迭代和完善，为全自动编程铺平道路。

**1 引言**

利用大型语言模型（LLM）实现软件工程任务自动化已获得相当多的关注[3,4,5]。除代码生成外，SWE-bench[1]提出的问题解决任务将LLM的角色从代码助手转变为完全自主的AI程序员。SWE-bench包含来自12个广泛使用的开源Python库的2,294个问题。LLM的任务是根据问题描述和包含缺陷的代码仓库生成补丁。在不到一年的时间里，SWE-bench lite上的解决率从0.33%[1]（RAG+GPT3.5）提高到43.00%[6]（CodeStory Aide+Mixed Models）。SWE-bench lite是从SWE-bench中选取的300个问题的子集，因其描述相对清晰且修复方案较为简单而入选。

SWE-bench[1]以Python为中心，这将其评估限制在Python相关领域（如数据处理和人工智能）的LLM。然而，它未能涵盖其他常见且必要的领域，如Web应用、移动应用和系统编程，这些领域依赖于其他编程语言[7,8]。因此，作为迈向多语言问题解决基准测试的第一步，我们选择开发Java版本的SWE-bench，原因如下：

**语言识别**：该文本为**英文**。

---

**中文翻译**：

两个原因：
1. 受欢迎程度。Java 程序语言广受欢迎，是业界采用最广泛的编程语言之一，尤其是在金融、云服务和 Android 应用开发领域。根据2024年8月的 TIOBE 指数，Java 排名第四，仅次于 Python、C 和 C++。凭借活跃的开发者社区，Java 持续增长 Oracle 的数据显示 [3]，全球已有 1.2 亿开发者，每年新增超过 100 万开发者。

2. 平台独立性。Java 程序在虚拟机上运行，类似于 Python 自动管理内存，并编译成由虚拟机解释的 Java 字节码。虽然 C 和 C++ 因其性能和内存效率优势更适用于系统编程，但我们认为语言模型的主要设计目标并非解决性能问题。因此，我们选择 Java 而非更注重性能的 C/C++ 作为第一步。

本文提出了一个 Java 版本的 SWE-bench，命名为 SWE-bench-java-verified。我们详细描述了数据集构建的主要挑战和潜在问题。利用 SWE-bench-java-verified，我们还评估了 SWE-agent [2] 在包括 GPT-4o [9]、GPT-4o-mini [10]、DeepSeekV2 [11]、DeepSeekCoder-V2 [12]、Doubao-pro [13] 在内的最先进模型上的性能。本文的贡献如下：

• 我们精心创建并手动验证了 SWE-bench-java-verified 基准测试，标志着建立以 Java 为重点的多语言 GitHub 问题解决基准测试的第一步。我们计划在未来几个月支持更多编程语言并持续改进。我们还鼓励社区通过提交拉取请求来贡献力量，共同推进该领域的发展。

• 我们开源了数据集，以及完整的评估 Docker 环境和排行榜，以推动该领域的进一步研究。

• 我们在 SWE-bench-java-verified 上实现了 SWE-agent [2]，并得出了一些有价值的发现，从而加深了我们对 Java 项目问题解决的理解。

2. Multi-SWE-bench

2.1 基准测试构建

2.1.1 工作流程概述

我们参照 SWE-bench [1] 的工作构建 SWE-bench-java-verified。具体而言，构建该基准测试的工作流程包括五个阶段：

1. 候选仓库收集。我们从两个来源收集 SWE-bench-java-verified 构建的候选仓库：(1) GitHub 上的热门 Java 仓库，通过 GitHub API [4] 按星标排序获取 Java 仓库列表；值得注意的是，我们排除了 Java 不是主要语言的仓库。(2) Defects4j [7] 数据库中包含的仓库，该数据集收集了可重现的缺陷。

该文本为**英语**。

---

**翻译（简体中文）：**

多个Java代码仓库。因此，我们从GitHub收集了53个代码仓库，并从Defect4J收集了17个代码仓库，共计获得70个候选Java代码仓库。经过仔细的人工筛选和过滤，我们将范围缩小至19个开源Java代码仓库：来源(1)10个，来源(2)9个。

2. 问题实例抓取。问题实例抓取过程按以下三个步骤进行：(1) 我们抓取了所选19个代码仓库的所有拉取请求。(2) 通过筛选，保留那些至少关联一个问题且涉及测试文件更改的拉取请求。(3) 对于符合选择标准的每个拉取请求，我们进一步抓取其详细信息，包括“实例ID”、“补丁”、“仓库名称”、“基础提交”、“提示文本”、“创建日期”、“测试补丁”、“问题描述”、“环境设置提交”和“失败转通过”。最终，我们为19个代码仓库共抓取了1,979个问题实例。

3. 运行时环境确定。我们通过代码阅读和试运行来确定每个问题的运行时环境。具体而言，我们确定目标问题所使用的构建工具、JDK版本和编译命令。针对与目标问题关联的代码仓库，我们执行以下三个步骤：(1) 从仓库结构中识别构建工具类型（Maven或Gradle）；(2) 通过审查构建配置来确定所使用的JDK版本；(3) 我们编译该仓库以建立其编译命令。在这一阶段，我们还筛选掉那些关联仓库无法被编译的问题，例如依赖于Android SDK等额外开发环境的代码仓库。由此，我们从1,979个问题实例中主要获得了308个问题实例的集合，这些实例经确认可在确定的运行时环境下成功编译。

4. 失败转通过测试提取。我们通过比较应用真实补丁前后的测试结果来为每个问题提取失败转通过测试。具体而言，对于一个给定的问题实例，我们构建两个不同的容器，分别基于已应用修复补丁的代码仓库和未打补丁的代码仓库来运行抓取的测试补丁中提到的测试。然后，我们解析输出的测试日志以获取相关测试的结果，收集那些在问题修复前失败但在修复后通过的测试。基于测试结果，我们进一步对问题实例进行筛选。我们仅保留至少有一个失败转通过测试且没有问题实例具有通过转失败测试的情况，由此获得了137个问题实例的集合。

5. 基于问卷的手动验证。为确保我们基准测试在评估大语言模型问题解决能力方面的可靠性，我们进行了全面的手动验证过程。参照近期发布的SWE-bench验证版。

**语言识别：该段落为英语（English）。**

**翻译（中文）：**

注释指南7，我们邀请了10位精通Java的软件开发者来对上述137个问题实例进行筛选。验证过程涉及对每个问题回答以下三个问题：（Q1）问题描述的清晰度，评分为0至3分，评分越低表示越清晰；（Q2）测试覆盖评估潜在解决方案的全面性，同样评分为0至3分，评分越低反映覆盖越全面；（Q3）是否存在可能需要从数据集中排除的重大缺陷，0表示无此类缺陷，1表示存在此类缺陷。基于这些注释，我们保留了满足以下条件的问题："（Q1为0或Q1为1）且（Q2为0或Q2为1）且（Q3为0）"。这种严格的筛选最终产生了91个高质量的问题实例，涵盖6个代码仓库。

2.1.2 故障排除

在构建SWE-bench-java-verified基准测试的过程中，我们发现并修复了一些对基准测试质量和评估效率产生负面影响的问题。尽管SWE-bench已经建立了一个完整且易于使用的流程，用于挖掘问题并自动评估Python项目中问题解决方案的效果，但在迁移到Java时我们仍然遇到了一些困难。在本节中，我们将介绍已识别的问题并讨论我们的解决方案。

原始SWE-bench脚本中的基础提交爬取错误。我们发现原始SWE-bench脚本8有时会爬取拉取请求的错误基础提交，主要是因为它不加区分地使用前一个提交而未考虑分支差异。我们已使用"git提交图"修复了此bug，以区分不同分支，使脚本更加可靠并可供使用。

仓库和依赖项的冗余下载。我们发现当对不同问题实例运行评估时，反复下载仓库和依赖项是一项负担。具体而言，SWE-bench为每个问题实例构建单独的Docker容器，在线拉取相应的仓库以初始化工作目录；此外，还需安装依赖项以构建运行时环境。然而，某些问题共享相同的代码仓库，其依赖项可能重叠，这意味着会出现冗余下载。为减少仓库下载的冗余，我们预先将所有选定的仓库一次性下载到本地，然后从本地存储将下载的仓库复制到容器中。我们还计划类似地为依赖项维护本地缓存，并将其直接挂载到构建的容器中以避免重复下载。

增量编译过程中可能出现的编译中断。在预编译依赖项以提高缓存时，应用增量补丁有时会导致编译失败。这些失败可能发生在补丁破坏项目的...

The text you provided is written in **English**.

Here is the translation into Chinese:

---

构建过程，尤其是在具有复杂依赖结构的项目中。为了解决这一问题，我们仔细分析了源代码和项目架构，识别了潜在的冲突。然后，我们根据每个项目的独特设置精心设计了特定的测试命令，确保构建过程在应用这些更改后保持稳定。这一 thorough（彻底）过程帮助我们在不同环境中保持了一致的构建完整性。

2.2 数据统计

如图1所示，SWE-bench-java-verified 基准测试共包含6个流行 GitHub 仓库的91个问题。这些仓库之间的问题分布各不相同，其中“fasterxml/jackson-databind”问题最多，达49个，而“apache/dubbo”最少，仅有4个。这6个仓库涵盖多个领域，包括数据序列化（如“fasterxml/jackson-core”、“fasterxml/jackson-dataformat-xml”）、Web服务（如“apache/dubbo”）、数据格式（如“google/gson”）以及容器工具（如“googlecontainertools/jib”），展示了该数据集的广泛覆盖范围。这种多样性强调了数据集的代表性，为评估大型语言模型在 Java 生态系统中自动解决 issue（问题）的能力提供了广泛的测试平台。

---

**Table 1 translation:**

表1提供了 SWE-bench-java-verified 数据集中包含的仓库的关键统计数据摘要，同时突出了其多样性和代表性。这些仓库在受欢迎程度方面差异很大，从 star（星标）数量来看，范围从 0.56K 到...



---

## 论文 16

# SWE-Master: Unleashing the Potential of Software Engineering Agents via Post-Training

**作者**: Huatong Song, Lisheng Huang, Shuang Sun, Jinhao Jiang, Ran Le

**arXiv**: https://arxiv.org/abs/2602.03411

---

该文本为**英文**。以下是学术性的中文翻译：

---

**SWE-Master：释放后训练在软件工程智能体中的潜力**

华通松¹∗，李生黄¹∗，孙爽¹∗，姜金浩¹∗，兰乐²，程代轩¹，陈国欣¹，胡益文¹，陈宗超²， Wayne Xin Zhao¹†，杨松²†，张涛²，纪荣文¹†

¹ 中国人民大学高瓴人工智能学院
² BOSS直聘，北京，中国

**摘要**

本技术报告，我们呈现了SWE-Master，一个开源且完全可复现的后训练框架，用于构建有效的软件工程智能体。SWE-Master系统性地探索了完整的智能体开发流程，包括教师轨迹合成与数据 curation、长程监督微调、基于真实执行反馈的强化学习，以及推理框架设计。从一个初始SWE能力有限的开源基础模型出发，SWE-Master展示了系统性优化方法如何激发强大的长程SWE任务解决能力。我们在SWE-bench Verified上评估SWE-Master，这是一个针对现实软件工程任务的标准基准。在相同的实验设置下，我们的方法使用Qwen2.5-Coder-32B达到了61.4%的解决率，显著优于现有的开源基线。通过进一步整合基于LLM环境反馈的测试时扩展（TTS），SWE-Master在TTS@8下达到了70.8%，展现出强大的性能潜力。SWE-Master为推进软件工程智能体的可复现研究提供了实用且透明的基础。代码可访问https://github.com/RUCAIBox/SWE-Master。

**图1：性能概览与扩展性分析。** 左图：各开源基础模型与SWE智能体在SWE-bench Verified上的表现对比。右图：SWE-Master在不同训练阶段和评估指标下的表现。∗ 等贡献。† Wayne Xin Zhao和杨松的通讯作者。

---

*（注：原文在"Introduction"部分被截断，若需继续翻译后续段落，请提供完整内容。）*

The paragraph is written in **English**.

Here is the Chinese translation:

---

软件工程智能体（又称SWE智能体[1]）作为一种强大的范式，近年来在自动化复杂软件开发任务方面取得了显著进展[2, 3]。与专注于短代码片段或孤立函数生成的传统代码生成模型不同[4, 5]，现代SWE智能体需要理解自然语言需求、导航大型代码库、修改多个文件、执行测试，并迭代优化解决方案直至任务成功完成[6]。通过以端到端自主工作流的方式运行，SWE智能体有望显著降低人工工程成本，并加速现实环境中的软件开发与维护。

近期SWE智能体的进展得益于整个流水线的协同进步，涵盖数据构建、环境反馈训练以及推理阶段的支架架构。在训练方面，主流方法从真实的GitHub问题中构建可执行任务实例，并将模型训练为与环境进行多步交互的智能体——探索代码库、修改文件、执行命令、迭代优化解决方案，直至最终补丁通过单元测试验证，来自容器化执行环境（即Docker）的执行反馈为模型提供了监督信号[7, 8, 9, 10]。在推理方面，现有方法通常采用标准化支架架构和基本能力工作流，如OpenHands[6]。一些研究进一步增强了这些框架的额外工具，以支持扩展能力，包括长上下文管理[11, 12, 13]。通过系统性的训练优化结合精心设计的推理框架，OpenAI和Anthropic等组织开发的近期系统在具有挑战性的真实世界软件工程基准测试中取得了优异表现[14, 15]。

尽管软件工程智能体取得了快速进展，但现有方法在训练数据构建和优化流程的透明度和可复现性方面仍然存在根本性局限。在实践中，许多最先进系统的封闭性掩盖了构建高效SWE智能体所面临的一些关键挑战。在训练数据方面，一个关键困难在于高效构建能够捕捉长程推理和真实环境交互的高质量教师轨迹。在优化方面，智能体训练通常遵循两阶段范式：监督微调（SFT）和强化学习（RL）。前者需要仔细的数据过滤和混合设计，以平衡正确性、多样性和任务难度；后者则需要精细的算法调优和奖励设计，以鼓励充分的探索和稳定的学习，同时避免熵崩溃或奖励黑客等问题。此外，在推理方面，现有方法在很大程度上受限于基础智能体框架，能力有限。

这段文字是**英文**。以下是学术性的中文翻译：

---

针对高级工具和系统设计的探索，特别是在执行效率和长上下文管理方面。这些不透明且相互依赖的组件形成了极高的入门门槛，阻碍了可重复性研究，并限制了更广泛学术社区对SWE智能体开发的无障碍获取。

为应对这些挑战，我们推出了SWE-Master，一个完全开源的软件工程智能体框架，以透明且可复现的方式完整呈现后训练流程。SWE-Master不再将智能体性能视为孤立设计选择的结果，而是系统性地研究软件工程能力如何从数据构建、优化策略与推理时行为的交互中涌现，即便起始于一个开源模型且其初始SWE任务表现有限（例如，使用Qwen2.5-Coder-32B模型在SWE-bench Verified基准测试中得分低于10分）[4]。具体而言，我们分析了轨迹合成过程中不同教师模型和数据过滤策略的影响，并表明控制训练数据的难度分布在塑造SFT后模型的交互深度和决策行为方面起着关键作用。在此基础上，我们进一步通过探索优化算法与奖励设计的组合，在真实执行环境中研究强化学习，从而实现高效探索和有效学习，同时减轻奖励黑客攻击和行为不稳定等常见失败模式。综上，SWE-Master为理解和推进软件工程智能体的后训练提供了一个全面、开源且经实证验证的框架。

基于前述现有推理框架的局限性，我们进一步研究了在推理时赋予智能体高级能力的效果。许多软件工程失败源于对大型代码库的理解不足而非代码生成错误，基于这一观察，我们专注于增强智能体的代码交互和导航能力。具体而言，我们研究了从简单文本搜索向基于语言服务器协议的结构化代码导航的转变，并分析了其对大型仓库中推理和决策的影响。通过系统的实证分析，我们发现基于语言服务器协议（LSP）的工具构成了SWE智能体的新范式。这一方法赋予了智能体IDE级别的代码理解能力，从而便于在现实软件工程场景中对复杂文件系统进行精确检查和修改。为验证所提方案的有效性，我们在SWE-bench Verified[16]上进行了大量实验——这是一个广泛用于评估现实软件工程智能体的基准测试。在相同的实验设置下（包括相同的基模型、训练数据来源），我们开展了实验验证。

**语言识别**：该文本为**英语**（English）。

---

**译文**：

基于推理配置的实验，我们的长期监督微调（SFT）策略显著优于现有开源方法，实现了57.8%的解决率。这些结果表明，仅凭精细的数据整理和轨迹级监督即可大幅提升真实世界软件工程任务的性能。在此强劲的SFT基线之上，我们进一步采用基于真实执行环境的强化学习（RL），持续扩展模型能力，使智能体能够解决更具挑战性的实例，将性能提升至61.4%。此外，受先前利用大型语言模型模拟真实执行反馈的研究 [17, 18] 启发，我们采用了由LLM驱动的环境反馈实现的测试时扩展（TTS）策略 [19]。该方法使智能体能够在无需物理执行开销的情况下探索并排序多个候选解决方案。通过选择最具潜力的候选方案，我们的方法在TTS@8设置下达到了70.8%的得分。此策略避免了真实环境中的直接执行，这在环境交互成本高昂、不可逆或不安全的场景中尤为有价值。最后，通过在推理时集成基于LSP的代码导航框架，SWE-Master在几乎不影响任务成功率的情况下提升了智能体效率，实现了效能与效率之间的实用平衡。

根据我们的实验，主要贡献总结如下：

• 我们发布了首个完全开源的软件工程智能体端到端训练流程，涵盖数据处理、SFT、RL基础设施与策略，以及推理时智能体框架。
• 我们引入了基于LSP驱动代码导航的IDE级能力，实现更高效、结构化的代码库理解，显著提升智能体效率而不牺牲性能。
• 我们大幅推进了开源模型在SWE-bench Verified上的性能表现，使用Qwen2.5-Coder-32B达到61.4%的准确率，通过测试时扩展提升至70.8%，在Pass@8下达到76.2%，展现出强劲的性能潜力。

2

前置知识

2.1

问题定义：SWE任务

我们将软件工程任务定义为自动程序修复或功能实现问题。形式上，令 D = {(Ii, Ci, Ui)}N i=1 表示软件工程问题的数据集。对于特定实例，输入包括：
• 问题描述 I，描述缺陷报告或功能请求；
• 代码库 C，表示代码仓库的初始状态（即文件系统结构）。

真实答案通常包括一个黄金补丁 p* 和由一系列测试用例组成的单元测试套件 U。模型的目标是生成一个补丁 p̂（一组差异），使得将该补丁应用于代码库后能够解决问题 I，定义为通过所有单元测试。令 fapply(C, p) 为将补丁应用于代码库的函数。修改后的代码库...

**语言识别 / Language Identification:**

该段落为**英文**（English），是一篇关于软件工程（Software Engineering）领域的学术/技术论文。

---

**翻译 / Translation (English → Chinese):**

记作 C′ = fapply(C, p̂)。

**2.2 基于智能体的环境交互**

我们将问题求解过程建模为交互环境中的序贯决策过程。第 k 步的环境状态记为 s_k，包括当前文件内容、命令行历史以及之前的执行输出。

智能体作为策略 π_θ(a_k | h_k) 运作，其中 θ 表示模型参数，h_k = ⟨s_0, a_0, o_0, s_1, . . . , o_{k-1}⟩ 为交互历史。智能体生成的动作 a_k 由推理轨迹（Thought）和工具调用（Action）组成。动作空间 A 通常包括：

A = A_nav ∪ A_edit ∪ A_exec   (1)

其中 A_nav 包含导航命令（如 ls、cd），A_edit 包含文件操作命令（如 view、create、str_replace），A_exec 包含执行命令（如 pytest）。

执行动作 a_k 后，环境返回观测 o_k（如标准输出、错误日志或文件内容）并转移至新状态 s_{k+1}。随后智能体继续进行后续交互。此过程持续进行，直至智能体发出终止动作（如 submit）或达到最大步数限制 K_max。轨迹定义为 τ = ⟨I, a_0, o_0, a_1, o_1, . . . , a_K⟩。

**2.3 评估协议**

评估在隔离的 Docker 容器中严格基于执行进行。对于每个问题，智能体与代码库交互以实现解决方案，随后通过 git diff 捕获为补丁。将该补丁应用于原始仓库进行验证。生成的补丁 p̂ 的有效性由单元测试套件 U 决定。测试套件由两个子集组成：U = U_fail ∪ U_pass。具体而言，U_fail 表示用于复现缺陷的"失败转通过"（F2P）测试集，而 U_pass 则由"通过转通过"（P2P）测试组成，用于确保现有功能无回归。

设 V(C, u_j) 为单元测试 u_j ∈ U 的验证奖励函数，定义为：如果 u_j 在代码库 C 上通过，则 V(C, u_j) = 1，否则为 0。当且仅当通过应用预测补丁 p̂ 获得的修改后代码库 C′ 成功通过整个测试套件 U 时，软件工程任务被视为已解决。形式上，解决方案状态 Resolved(p̂) 定义为：

$$\text{Resolved}(p̂) = \mathbb{I}\left[\sum_{u_j \in U} V(C', u_j) = |U|\right]$$

其中 I[·] 表示指示函数。因此，当且仅当每个单独测试用例 u_j ∈ U 的验证奖励均为 1 时，任务级奖励为 1；否则奖励为 0。

**3.1 SWE-Master：训练开源 SWE 智能体 训练框架与环境**

训练有效的代码问题解决智能体需要能够真实反映现实软件工程工作流程的环境。与静态基准测试（如代码生成 [20]、网络搜索 [21]）不同，此类任务需要具有终端访问权限和持久状态的交互式执行环境。



---

## 论文 17

# TOM-SWE: User Mental Modeling For Software Engineering Agents

**作者**: Xuhui Zhou, Valerie Chen, Zora Zhiruo Wang, Graham Neubig, Maarten Sap

**arXiv**: https://arxiv.org/abs/2510.21903

---

The provided text is written in **English**.

Here is the Chinese translation:

---

**审稿中**

**ToM-SWE：面向软件工程的意图建模代理**

徐辉·周（Xuhui Zhou）  
格雷厄姆·纽比格（Graham Neubig）

瓦莱丽·陈（Valerie Chen）  
玛arten·萨普（Maarten Sap）

**佐拉·智若·王（Zora Zhiruo Wang）  
星耀·王（Xingyao Wang）**

卡内基梅隆大学语言技术学院

全人人工智能（All Hands AI）

arXiv:2510.21903v1 [cs.SE] 2025年10月24日

§ All-Hands-AI/ToM-SWE

**摘要**

编码代理的最新进展使其能够对复杂代码库进行规划、编辑、运行和测试。尽管这些系统在编码任务方面的能力不断提升，但在推断和跟踪用户意图方面仍然存在困难，尤其是当指令表述不明确或依赖上下文时。为了弥补这一差距，我们提出了ToM-SWE，这是一种双代理架构，将主要的软件工程（SWL）代理与一个轻量级的心理理论（ToM）伙伴代理配对，后者专门负责建模用户的心理状态。ToM代理从指令和交互历史中推断用户的目标、约束和偏好，维护用户的持久记忆，并向软件工程代理提供与用户相关的建议。在两个软件工程基准测试（模糊SWL-bench和状态化SWL-bench）中，ToM-SWE提高了任务成功率和用户满意度。值得注意的是，在状态化SWL基准测试中（这是一项新引入的评估，为代理提供用户模拟器和之前的交互历史），ToM-SWE实现了59.7%的显著更高的任务成功率，而当前最先进的软件工程代理OpenHands仅为18.1%。此外，在一项为期三周的专业开发者研究中使用ToM-SWE进行日常工作时，参与者发现它在86%的时间内是有用的，这凸显了状态化用户建模对实际编码代理的价值。

---

**引言**

大型语言模型（LLM）的最新进展使编码代理能够执行复杂的软件工程任务，从代码生成（Jiang等人，2024）和调试（Tian等人，2024）到系统设计（Kovacic等人，2025）和优化（Gao等人，2024）。然而，尽管技术能力令人印象深刻，编码代理在软件开发的一个基本方面——与人类开发者的有效沟通和协作——仍然存在困难。

核心限制在于，当前系统缺乏在长周期、多轮交互中建模和预测人类意图的明确机制（Kim等人，2023）。与人类开发者不同，人类开发者会自然地在各种任务中为协作者建立目标、偏好和约束的心理模型（Tomasello，2009），而编码代理缺乏从表面层面推断和获取潜在用户意图的机制。在现实世界的交互中，这些意图往往是模糊的、不完整的或依赖上下文的（Levinson，1983）。此外，当前的编码代理通常以无状态方式运行，将每个会话视为独立的，而不是维护关于用户不断发展的目标和对话历史的持久上下文。这种范式往往导致工作浪费和误解。

这段文字是**英文**。以下是其**中文翻译**：

---

dings，在高风险场景下可能导致错误甚至不安全的结果。

为了弥合当前编码智能体与长程交互中用户意图推断之间的差距，我们引入了ToM-SWE，这是一个将心智理论（ToM）推理整合到软件工程智能体中的概念框架。此处，ToM特指根据用户指令和交互历史建模用户心理状态的能力，包括目标、偏好和意图。如图1所示，ToM-SWE通过双智能体架构实现这一理念：一个主要的软件工程（SWE）智能体专注于编码任务，而一个专门的ToM智能体建模用户心理状态并在需要时为SWE智能体提供支持。这种分离至关重要，原因有两点：它保持了SWE智能体的编码性能，并实现了专门的、持久的用户建模，开发者可以灵活调用和定制，以提高效率和保护隐私。ToM智能体本身以两种互补模式运行，以跟踪用户的偏好、情绪等。在主动编码会话期间（会话内ToM），它会推断用户的潜在心理状态（例如，潜在模糊指令背后的"真实"意图）。在每次会话之后，它会创建用户的心理模型（会话后ToM），整合交互历史，以分层方式完善其对用户心理状态的信念。

为了评估ToM-SWE的有效性，我们引入了Stateful SWE基准测试，这是第一个允许智能体利用跨多个编码会话的真实对话历史来跟踪用户心理状态的基准测试。在此设置中，智能体与基于LLM的用户模拟器交互，并接收综合的交互历史来指导其推理。为了与先前评估保持一致，我们借用了SWE-bench（Chowdhury等人，2024）中的原始问题，将其重新表述为 casual 初始指令，并配以不同的用户档案和交互历史。智能体需要通过与基于LLM的用户模拟器交互或从过去的交互历史中推断来理解用户意图、偏好和约束。与现有的SWE-bench等基准测试不同，后者主要评估技术问题——

这段文字是**英语**。以下是学术性的中文翻译：

---

解决有状态软件工程基准测试评估的是智能体在长时间内维持有意义交互的能力。而模糊软件工程基准测试（Vijayvargiya等人，2025）通过使指令模糊来测试模糊消解能力，但未能捕捉到长期记忆需求。

我们在新推出的有状态软件工程基准测试以及无状态模糊软件工程基准测试（Vijayvargiya等人，2025）上评估了ToM-SWE框架，并使用OpenHands平台（Wang等人，2025）构建了我们的智能体（ToMCodeAct），这是一个用于开发软件工程智能体的开源框架。我们表明，ToMCodeAct在两个基准测试上都优于OpenHands最先进的CodeAct智能体（Wang等人，2024）。在模糊软件工程基准测试上，ToMCodeAct智能体实现了63.4%的问题解决率，而CodeAct智能体为51.9%（提升了11.5%）。在有状态软件工程基准测试上，ToMCodeAct智能体实现了57.4%（+43.9%）的任务解决率，而CodeAct智能体为13.5%。此外，ToMCodeAct智能体实现了显著更高的用户满意度评分3.62，而CodeAct智能体为2.57（提升了41%，通过评估偏好对齐、沟通等内容的用户模拟器自动测量）。结果凸显了在现实软件开发场景中建模用户的重要性，以尊重超出任务完成范围的具体用户偏好和约束。

最后，我们与专业开发人员进行了真实人类研究，以验证我们方法的实际有效性，并发现ToM智能体的建议在86%的情况下是有用的。与之前在预定义任务上进行人类研究的工作不同（Wu等人，2025；Qian等人，2025），我们招募了17名软件开发人员，让他们连续三周使用增强ToM功能的OpenHands CLI（Wang等人，2025）进行日常自主选择的编码任务。每次软件工程智能体与ToM智能体交流时，开发人员可以选择接受、部分接受或拒绝ToM智能体的建议。开发人员也可以在研究期间随时通过公共Slack频道提供反馈。除了高接受率之外，我们从开发人员的反馈中了解到……

---

*注：原文末尾有截断（"that ToM agent's"之后内容未完整），因此翻译在对应位置终止。*

## 语言识别

这段文字是**英文**（English）。

---

## 翻译（中文）

通常能够提供有用且宝贵的建议，从而提高用户的工作效率（例如“请为新功能添加pytest”或“尽量减少代码修改”）。此外，ToM代理甚至可以提供新颖且符合用户偏好的建议（例如“按照Linux开发哲学重构代码”）。

### T O M-SWE：将SWE代理与T O M代理配对

考虑现有的软件工程代理设置，如SWE-agent（Yang等人，2024）和OpenHands CodeAct（Wang等人，2024；2025）。在编码会话i的时间步t，代理接收来自环境（例如终端输出、文件内容、测试结果）或用户（例如用户指令、反馈）的观察ot∈O。然后，代理根据某个策略π(at | cit)执行动作at∈A（例如代码编辑、shell命令），其中cit = (o1, a1, ..., ot-1, at-1, ot)是编码会话i中代理在时间步t之前可用的上下文。

当准确执行任务需要理解隐含的用户偏好时，映射cit → at变得困难，因为这些信息通常存在于过去的会话{cj}j=1^(i-1)中，而不是当前上下文cit中。例如，当用户说“实现一个网页爬虫”时，代理需要推断出库偏好（requests还是httpx），而这些信息只能从之前的交互中获得。为了弥补这一差距，我们引入了一个心理理论（ToM）代理，专门对用户的心理状态进行建模（图2）。在下文中，我们将解释（1）SWE代理如何向ToM代理查询相关信息，以及（2）ToM代理如何对用户的心理状态进行建模。

**SWE代理与ToM代理的交互** 我们通过向SWE代理的工具集中添加两个额外工具来实现SWE代理与ToM代理的交互：（1）咨询tom（会话内）：SWE代理向ToM代理发送查询q和当前会话上下文cit（动作at），ToM代理通过对交互历史{cj}j=1^(i-1) ∪ {cit}进行推理，输出相关的用户心理状态信息muser。然后将这个用户建模信息加入到SWE代理的上下文中，作为ct ∥ [at, muser]，帮助代理做出符合用户隐含偏好和约束的决策。（2）更新记忆（会话后）：SWE代理完成编码会话后，使用此工具通知ToM代理处理当前会话并更新分层记忆系统。

---

### 审稿中

**收集会话**
453个原始人机交互SWE会话
每个会话平均10万token

**用户指令**

SWE
代理

“GBDT出了问题”

原始SWE-Bench数据
GBDT因预热启动导致数据泄露

**创建SWE画像**
从600个偏好中抽样15个画像
每个画像20个会话
GPT-5处理每个会话

**生成的拉取请求（PR）**

（## 简要描述：这是关于非直方图版本的...；
## 如何复现）

**单元测试 + 用户评分**

**代码库**
sklearn/

The text is in **English**.

**Translation (English → Chinese):**

T-5将问题转化为用户指令
用户指令与用户画像相匹配

帖子

PR

测试

❌

✔

join_struct_col

README.rst

❌

✔

vstack_struct_col

reqs.txt

✔

✔

matrix_transform

setup.cfg

用户评分

示例/
问题与画像配对

前置
PR

★★★★✩

图3：有状态SWE基准测试概述。我们首先按照上述三个步骤收集画像。然后通过将不同画像的用户模拟器与SWE-bench问题配对来创建实例。用户模拟器将原始SWE-bench问题描述重新表述为随意、自然的开场指令。智能体在相同环境和测试下完成任务，可访问与用户的先前交互历史，并可与模拟用户交互以获取额外信息。

ToM智能体设计 ToM智能体通过作为外部数据库实现的三级层次记忆系统来建模用户心理状态：（第一级）原始会话存储，保存完整的先前会话历史。（第二级）基于会话的用户模型，维护每个会话的分析，包括会话意图、交互模式和编码偏好。（第三级）整体用户模型，将跨会话模式聚合成偏好集群、交互风格总结和编码风格总结。

在会话阶段，一旦SWE智能体向ToM智能体发送查询和当前会话历史（cit），加载了整体用户模型的ToM智能体可以决定使用搜索文件操作来检索相关上下文，或使用读取文件操作从记忆系统的（第一级）和（第二级）中读取特定文件。检索/读取的内容将被添加到ToM智能体的上下文窗口中。ToM智能体可以在向SWE智能体提供建议之前执行多个操作以获取相关信息。

在会话后阶段，ToM智能体通过结构化工作流程处理新会话数据。原始会话首先自动添加到（第一级）。然后ToM智能体使用分析会话操作，以原始会话数据作为输入，提取用户意图、情绪状态和消息级偏好，以创建结构化的基于会话的用户模型（第二级）。如果没有整体用户模型，ToM智能体将使用初始化用户模型操作来聚合这些基于会话的用户模型，以更新整体用户模型（第三级）。如果已存在整体用户模型，ToM智能体将使用更新操作来更新整体用户模型。（更多操作空间详情见附录A.3。）

这种双智能体设计相比让SWE智能体直接处理用户建模具有两个优势：（1）减少上下文干扰：SWE智能体保持对技术任务的专注，不会被大量用户历史所淹没；（2）专业化优化：每个智能体都可以针对其特定领域（编码 vs 用户建模）进行优化。



---

## 论文 18

# SWE-Sharp-Bench: A Reproducible Benchmark for C# Software Engineering Tasks

**作者**: Sanket Mhatre, Yasharth Bajpai, Sumit Gulwani, Emerson Murphy-Hill, Gustavo Soares

**arXiv**: https://arxiv.org/abs/2511.02352

---

The provided text is written in **English**.

---

**Translation (English → Chinese):**

arXiv:2511.02352v1 [cs.SE] 2025年11月4日

SWE-Sharp-Bench：C#软件工程任务的可复现基准测试

Sanket Mhatre*、Yasharth Bajpai*

Sumit Gulwani、Emerson Murphy-Hill、Gustavo Soares

微软
印度班加罗尔
{t-smhatre, ybajpai}@microsoft.com

微软
美国华盛顿州雷德蒙德
{sumitg, emerson.rex, gustavo.soares}@microsoft.com

**摘要**——AI编码智能体在Python软件工程基准测试（如SWE-Bench）以及Java和C等其他语言的基准测试（如MultiSWE-Bench）上已展现出显著进展。然而，C#——在TIOBE指数中排名第五的主流企业级语言——在此类基准测试中仍属空白。我们推出了SWE-Sharp-Bench，这是一个面向C#的可复现软件工程基准测试，包含来自17个代码库的150个实例。在不同编程语言上评估相同模型-智能体配置的结果揭示了显著的性能差距：SWE-Bench Verified中70%的Python任务得以解决，而我们的C#任务仅有40%被成功解决。我们将SWE-Sharp-Bench及整个数据整理流程开源。

**关键词**——软件工程智能体；AI智能体评估；可复现基准测试；自动化软件工程

**一、引言**

大型语言模型驱动的自动化软件工程因其提升开发者生产力的潜力而受到广泛关注。这些模型现已能够实现代码补全、单元测试生成、文档生成、交互式聊天界面，以及——更近期的——自主编码智能体[1]、[2]。这促使基准测试日益复杂化，从简单的函数级代码补全演进为评估这些模型自主解决真实世界软件工程问题的能力。SWE-Bench[3]和SWE-Bench Verified[4]已成为评估最新模型和智能体在软件工程任务上表现的最广泛使用的基准测试。其他软件工程基准测试——如Multi-SWE-Bench[5]、SWE-Bench多语言版和SWE-PolyBench——涵盖了Python以外的更多编程语言。

然而，审视TIOBE编程社区指数——“编程语言流行度指标”1——可以发现基准测试覆盖存在系统性空白。按流行度排名前八的语言分别是：Python（第一，已被SWE-Bench覆盖）、C++（第二，已被Multi-SWE-Bench覆盖）、C（第三，已被Multi-SWE-Bench覆盖）、Java（第四，已被Multi-SWE-Bench和SWE-PolyBench覆盖）、C#（第五，无覆盖）、JavaScript（第六，已被Multi-SWE-Bench和SWE-PolyBench覆盖）、Visual Basic（第七，无覆盖），以及Go（第八，已被Multi-SWE-Bench覆盖）。值得注意的是，整个.NET生态系统——包括C#和Visual Basic——尽管排名靠前，却始终缺席于软件工程基准测试。鉴于C#对企业软件开发的重要性2，这一空白尤为显著。.NET语言的缺失限制了我们对模型和编码智能体在C#独特特性方面表现的理解。

## 语言识别 / Language Identification

该段落为**英语** (English)。

---

## 中文翻译 / Chinese Translation

在本文中，我们介绍了SWE-Sharp-Bench，这是首个面向C#和.NET生态系统的软件工程任务基准测试集，包含150个经过精心筛选的实例。我们在借鉴SWE-Bench方法论的基础上，构建了一套针对.NET特定挑战的筛选流程，包括复杂的依赖管理、多版本兼容性以及跨平台开发，以确保自动化创建可复现的容器化环境。我们使用流行的智能体框架（SWE-Agent和OpenHands）对来自OpenAI和Anthropic的领先模型进行评估，结果表明C#对当前模型构成了重大挑战，性能差距似乎源于C#项目中典型变更的较高复杂性。

**II. 相关工作**

早期的代码生成基准测试（如HumanEval和MBPP）确立了代码生成评估的标准。随后，这一方法在HumanEval-XL、MBXP和MultiPL-E（包括C#）中得到扩展和推广。然而，这些基准测试仍然主要评估小型、自包含的编程任务。转向仓库级软件工程评估的趋势随着SWE-Bench和SWE-Bench Verified的出现而显现，这些基准测试评估来自开源仓库的真实世界拉取请求。此后，这一方法论在多个维度上得到扩展。在多语言方向上，Multi-SWE-Bench提供了涵盖七种语言（Java、JavaScript、Go、Rust、C和C++）的1632个实例，SWE-Bench Multilingual贡献了涵盖九种语言（C、C++、Java、JavaScript、Go、Rust、TypeScript、PHP和Ruby）的300个实例，而SWE-PolyBench则专注于JavaScript、TypeScript、Python和Java。此外，GitBugJava为Java开发了类似的仓库级基准测试，使用GitHub Actions确保可复现构建。除了纯文本上下文外，SWE-Bench Multimodal将评估扩展到需要视觉理解的问题。除了从开源仓库筛选问题外，SWELancer还引入了来自自由职业平台的任务。最近的研究，如SWE-Smith，也探索了通过LLM合成数据生成来扩展实例创建的方法。尽管取得了这些多语言和方法论方面的进展，C#在仓库级软件工程评估中仍然代表性不足。

**III. 构建SWE-Sharp-Bench**

SWE-Sharp-Bench包含150个解决issue的任务，这些任务是从17个流行且积极维护的C# GitHub仓库中精心筛选出来的。这些任务映射到GitHub问题，这些问题要么报告bug，要么请求新功能。

**A. 基准测试构建**

1. **仓库选择**：从C# GitHub仓库排名前100中，我们保留至少5000颗星、在过去6个月内有活跃维护且验证可构建的项目。
2. **PR抓取与属性筛选**：我们抓取最新的1000个PRs...

这是英文文本。

**翻译（中文）：**

r 选定的仓库，仅保留满足以下条件的（1）至少引用一个 GitHub 问题、（2）修改了测试文件且（3）成功合并到仓库默认分支的拉取请求。

3. 环境确定：对于每个 PR，我们通过解析 .github/workflows、*.sln、*.csproj、global.json 和 .env 文件来自动生成 Dockerfile，以推断 NuGet 依赖项、构建目标和环境变量。该生成器协调版本声明并处理 .NET 特有的问题（NuGet/MSBuild 资产选择、多目标冲突、已弃用的运行时等）。我们通过构建和运行容器来进行验证。

4. 基于执行的筛选：对于每个 PR，我们分别在（1）基础版本、（2）基础版本 + 测试补丁以及（3）基础版本 + 测试 + 修复的情况下运行测试，仅保留出现通过 → 失败 → 过渡的情况；其他所有情况均视为不稳定并被省略。

5. 人工验证：每位候选 PR 均由前两位作者独立标注并参照与 SWE-Bench Verified 类似的标准进行交叉检查。我们标记（1）问题描述不明确和（2）测试不充分（过于狭窄或不一致）的情况。只有通过此审查的 PR 才被纳入最终基准测试。

基准测试构建过程的完整细节在附录中讨论。

B. 基准测试特征

1) SWE-Sharp-Bench 的特点：SWE-Sharp-Bench 展示了多种工具和应用程序。仓库可分为数据与存储（4 个）、API 基础设施（3 个）、用户界面（3 个）、开发工具（5 个）和多媒体处理（2 个）五类。该分类是通过人工检查仓库描述完成的（见附录表 II）。实例被分为三个主要类别：缺陷修复（91 个）、功能请求（47 个）和其他（12 个）。分类过程在附录 C 中讨论。53% 的实例创建于 2024 年，90% 的实例创建于 2023 年之后。

2) SWE-Sharp-Bench 与其他基准测试的特征对比：

语言和基准测试选择：在将性能差异归因于模型或智能体局限之前，我们需要了解当前基准测试的特征差异。我们选择 Python——作为研究中代表性最高的必要基线——以及 Java，因为它是 C# 的自然比较对象，具有相似的特性，如静态类型、复杂的构建系统和依赖管理。我们使用 SWE-Bench-Verified 作为 Python 基准测试，使用 Multi-SWE-Bench 作为 Java 基准测试。

补丁复杂度分析：我们采用 Multi-SWE-Bench 引入的补丁复杂度指标，该指标从三个维度测量静态属性：

- 补丁级指标：修改的文件数（变更广度）、每个补丁的块数（修改粒度）、新增/删除的行数（变更幅度）。
- 仓库指标：总文件数和代码行数。
- 任务规范：问题描述的标记长度。

我们为 SWE-Sharp-Bench 的 150 个实例提取了这些指标。

图 2：跨语言的补丁中 #文件数、#块数和 #行数分布

该文本为**英文**。以下是学术性的中文翻译：

---

以及 500 个 SWE-Bench Verified 实例。对于 Java，我们采用 Multi-SWE-Bench 论文中报告的指标。图 2 展示了跨语言的修改文件数、代码块数以及补丁中添加行数的分布情况。在附录中，表 III、IV 和 V 汇总了仓库级别的统计数据。我们的分析使用这些指标揭示了以下发现：

**仓库规模**：C# 项目的代码行数范围从约 5k 行到 147 万行（中位数约 235k 行），而 SWE-Bench Verified 中最大的 Python 仓库为 383k 行，Multi-SWE-Bench 中最大的 Java 仓库达到 443k 行。更大的仓库意味着代理需要搜索的表面积更广。

**变更局部性**：Python 修复通常较为精准（平均值 = 1.24，中位数 = 1 个文件）；Java 呈现中等程度的分散（平均值 = 2.96，中位数 = 2）；而 C# 的分布最为广泛（平均值 = 4.88，中位数 = 2），结合了许多小型修复和长尾的多文件变更，形成了多样化的混合情况。

**修改粒度**：补丁深度从 Python（平均 2.4 个代码块、14.3 行）到 Java（6.26/89.27）再到 C#（10.0/131.1）逐步增加，如图 2 中间所示。C# 表现出最为多样化的分布：与 Python 和 Java 一样，许多补丁都是小型的，但 C# 还包含大量更大的修改。对高补丁数量的 C# 实例进行人工检查发现，这些通常涉及重构操作和协调的多文件编辑。

**任务规范**：问题陈述的长度因语言而异。Java 问题可超过约 1.6k 个 token，Python 问题通常低于 450 个，而 C# 问题往往低于 150 个。描述较短的补丁可能由于细节不足而难以被代理解决。

**要点**：在三种语言中，C# 展现出最复杂的静态补丁属性，提供了多样化的任务复杂性组合。

**四、实验与结果**

**代理与模型**：我们评估了两个流行的代理系统：SWE-Agent 和 OpenHands。我们在来自 OpenAI 和 Anthropic 的多个领先语言模型上测试每个系统。由于这些框架最初是为 Python 仓库设计的，我们为其适配了 C# 项目的提示词。每个代理每个实例获得一次尝试机会，时间限制为 2 小时。由于预算限制，我们对每个实例进行一次尝试，仅在基础设施故障（如速率限制、API 错误）发生时重试，以确保每个实例至少有一次有效尝试。

**评估指标**：我们使用解决率作为主要指标，即每个代理成功解决的实例百分比。当代理生成的补丁通过所有必要的测试时，该实例被视为已解决。

---

The provided text is written in **English**.

---

**Translation (English → Chinese):**

**测试结果**
表 I 总结了不同 OpenAI 和 Anthropic 模型在使用 SWEAgent 和 OpenHands 时的 SWE-SharpBench 性能表现。在所有配置中，OpenHands + GPT-5 的表现最佳，解决率为 47.3%。表 VI（见附录）展示了在 SWEBench Verified 和 Multi-SWE-Bench' Java 子集上相同模型-智能体组合的性能。更多结果见附录。

**A. 哪些因素影响智能体性能？**

我们观察到，在相同的模型-智能体配置下，Python 与 C# 和 Java 之间的性能存在差异。例如，在 SWE-Agent + Claude Sonnet 3.7 上，Python 的解决率为 62.40%，C# 为 30.67%，Java 为 14.68%。

**六、基准测试与数据可用性**

附录中包含详细分析、基准测试的细分以及性能结果的深入探讨。基准测试数据可在 HuggingFace4 上获取。策划流程代码和智能体轨迹可在 GitHub5 上获取。

**参考文献**

**图 3：逻辑回归系数及其对解决率的影响（基线：C#，GPT-4o）。显著性：*** p < 0.001，** p < 0.01，* p < 0.05。**

更多详情见附录表 5。成功解决实例的可能性取决于多个因素：补丁的分布范围（补丁中的 hunks 和文件）、补丁的大小（补丁行数）、使用的模型或智能体。我们观察到，智能体在 Python 上的表现明显优于 C# 和 Java，但尚不清楚这种性能差异在多大程度上与实例的静态属性相关，而非编程语言本身的选择。为了解哪些因素对解决成功的影响最大，我们使用了逻辑回归分析。我们使用来自 SWEAgent 运行（配合 GPT-4o、Claude Sonnet 3.5 和 Claude Sonnet 3.7 模型）的实例级数据进行了分析，这些组合在所有三个基准测试中具有完整的实例级解决数据。

图 3 展示了以 GPT-4o 和 C# 作为基线类别的回归系数。正如预期，模型选择的影响最大，较新的模型明显优于 GPT-4o。复杂性越高，成功率越低：补丁的 hunks、行数 和文件数对性能产生负面影响，表明在多个位置进行广泛修改对智能体来说更具挑战性。编程语言显示出显著影响，Python 明显比 C# 基线更容易。此分析证实，Python 是最简单的语言，且在控制复杂性和模型因素后，Java 和 C# 难度相当。

**五、局限性**

虽然 SWE-SharpBench 提供了多样化的任务组合，但它存在一些局限性。首先，其 150 个实例少于 SWE-Bench Verified 的 500 个，尽管与 Multi-SWE-Bench 中的单个语言子集相当。其次，与 SWEBench Verified 和 Multi-SWE-Bench 不同，我们未提供难度的手动标注，尽管使用了补丁复杂性指标。



---

## 论文 19

# Automated Benchmark Generation for Repository-Level Coding Tasks

**作者**: Konstantinos Vergopoulos, Mark Niklas Müller, Martin Vechev

**arXiv**: https://arxiv.org/abs/2503.07701

---

该文本是**英文**。以下是翻译：

---

**代码库级编码任务的自动化基准测试生成**

Konstantinos Vergopoulos 1 * Mark Niklas Müller 1 * Martin Vechev 1 2

arXiv:2503.07701v1 [cs.SE] 2025年3月10日

**摘要**

1. **引言**

代码智能体（Code Agent）开发是一个极其活跃的研究领域，可靠的性能指标对于跟踪进展和指导新开发至关重要。SWE-Bench的迅速普及凸显了这一需求。该基准测试要求代码智能体在获得完整代码库上下文的情况下生成解决GitHub问题的补丁。生成的补丁的正确性通过执行从问题解决后提取的人工编写的测试套件来评估。

代码智能体正迅速成为大语言模型（LLMs）最具前景且被广泛研究的应用之一；这部分归因于其变革7000亿美元软件产业（Statista，2025年）的潜力。为了衡量进展，更重要的是为了引导该领域的进一步发展，高质量的数据集和基准测试至关重要。特别重要的是，这些数据集和基准测试必须代表真实世界的使用场景，规模足够大以允许有意义的统计分析，并且足够多样化和最新，以避免无意的过拟合和污染。

然而，构建像SWE-Bench这样的基准测试需要大量人工努力来设置用于测试的历史精确执行环境。关键是，这严重限制了所考虑的代码库数量，例如SWE-Bench仅考虑12个代码库。考虑到如此少的代码库，且选择它们的原因是其流行度，这可能导致分布不匹配问题，即测量的性能可能无法代表真实世界场景，从而可能误导开发工作。

**现有基准测试** 然而，像HumanEval（Chen等人，2021年）这样用于评估LLM编码性能的函数级基准测试不能代表真实世界使用，缺乏多样性，且正变得饱和。为了解决这些限制，SWE-Bench（Jimenez等人，2024年）被提出作为第一个基于真实世界任务的代码库级编码基准测试，即解决GitHub问题。然而，它仍然存在几个局限性。（i）它仅限于少数代码库，可能导致对这些特定代码库的过拟合。（ii）它仅关注库而非应用程序，这引发了泛化性问题。（iii）它对流行代码库的关注不仅使其代表性较低，还增加了一般代码库知识被污染的可能性。（iv）其静态性质导致大多数甚至所有实例都是在最近模型的训练数据截止日期之前创建的，使得实例可能恰好出现在训练数据中。

在这项工作中，我们应对这一挑战并介绍SETUPAGENT，这是一个能够实现历史精确依赖设置、测试执行和结果解析的全自动化系统。使用SETUPAGEN

**语言识别：英语 (English)**

---

**翻译 (中文)**：

T，我们生成了两个新的数据集：
(i) SWEE-Bench——一个涵盖数百个代码仓库的SWEBench扩展版本，
以及 (ii) SWA-Bench——一个专注于应用程序而非库的基准测试。通过将这些数据集与SWE-Bench在特征和代码智能体性能方面进行比较，我们发现了显著的分布差异，包括
较低的issue描述质量和详细程度、
更高的修复复杂度，以及最重要的是，
智能体成功率降低了高达40%。

**创建仓库级基准测试** 为了应对
这些挑战，我们希望创建更加多样化的
基准测试，并使用新任务频繁更新它们。
然而，虽然GitHub Issues和Pull Requests (PRs)
可以作为SWE-Bench类基准测试的任务描述和参考解决方案被自动抓取，
但评估解决方案的正确性需要执行该仓库的测试套件。这反过来又需要
设置历史准确的执行环境、识别正确的测试命令，以及解析
测试结果。先前的工作通过手动方式（Jimenez等人，2024）或通过积极过滤掉
默认命令不成功的实例（Jain等人，2024c）来解决这个问题。然而，这两种方法都产生了
有限的多样性，并且不适合频繁更新。

*

同等贡献 ¹ LogicStar AI ² 苏黎世联邦理工学院计算机科学系。通讯作者：Mark Niklas Müller
<mark@logicstar.ai>。
预印本。版权 2025 由作者所有。

---

**GitHub仓库**

**仓库级编码任务的自动化基准测试生成**

命令提取
上下文

输出
迭代改进

CI/CD文件
文本文档
在线文档

初始
命令
链接

安装
命令
测试
命令

验证

安装
执行
测试
执行

安装
命令

解析
检查
结果

✗

测试
命令

✓

结果
解析器

被拒绝
参考命令
图1. S ETUPAGENT概述，其中

图标表示由大语言模型驱动的步骤，图标表示执行反馈。

**本工作：SETUPAGENT** 为了应对这一挑战，
我们提出了SETUPAGENT，这是第一种自动化
此设置过程的方法，使我们能够从GitHub
仓库列表完全自动地创建仓库级代码基准测试。SETUPAGENT在三个关键阶段工作（如图1所示）：(i) 命令提取（图1中的绿色部分），
(ii) 迭代测试和改进（蓝色部分），
以及(iii) 验证（紫色部分）。在提取阶段，SETUPAGENT分析相关上下文，如README.md
文件、CI/CD配置和引用的网页，以
提出安装和测试命令。在迭代改进阶段，SETUPAGENT随后在干净的环境中执行这些
命令，并利用大语言模型系统地诊断和解决问题。最后，在验证阶段，SETUPAGENT通过根据测试结果验证设置的正确性来确保生成的
命令是可靠的，只接受满足
要求的配置。

The provided text is written in **English**.

---

**Translation (English → Chinese):**

预定义的成功阈值。

**本文的主要贡献包括：**
• 我们提出了SETUPAGENT，这是首个能够自主创建历史准确执行环境的方法。
• 我们利用SETUPAGENT创建了两个用于仓库级代码生成的数据集：SWA-Bench和SWEE-Bench，分别专注于应用程序和多样化项目。
• 我们对SWA-Bench和SWEE-Bench进行了广泛分析，涵盖其特征及相应的代码智能体性能。

**2. 相关工作**
**代码智能体** 为充分发挥大语言模型在代码生成方面的潜力，研究人员为其配备了多种工具，使其能够在无需用户额外输入的情况下与环境进行交互，例如搜索、查看和编辑代码（Wang et al., 2024a）。这些所谓的代码智能体在复杂任务上展现出巨大的潜力（Bouzenia et al., 2024a; OpenDevin, 2024; Zhang et al., 2024; Yang et al., 2024; Xia et al., 2024; Aider, 2024; Ridnik et al., 2024; Wang et al., 2024b）。在本工作中，我们评估了一些表现最佳的开源智能体。

**本工作：生成的基准测试** 我们通过创建SWA-Bench和SWEE-Bench展示了SETUPAGENT从仓库列表生成代码基准测试的能力，这两个数据集分别针对SWEBench的特定不足之处而设计。两者均旨在代表真实世界的使用场景，涵盖众多仓库从而形成多样化的基准测试，并且可频繁更新而无需人工干预，以避免数据污染和过拟合问题。SWA-Bench专注于软件应用程序，包含44个项目；而SWEE-Bench则专注于多样性和较不受欢迎的项目，包含366个Python仓库。通过将SWA-Bench和SWEE-Bench与SWE-Bench进行比较，我们发现两者存在显著的分布差异，包括问题创建时仓库年龄和受欢迎程度较低、更侧重于近期问题，以及参考代码修复明显更复杂（修改文件和代码行数增加2-4倍）。在这些数据集上评估热门代码智能体后，我们发现某些模型存在显著的性能差异，且存在统计学上显著的数据污染迹象，这凸显了在具有代表性的基准测试上进行评估的重要性。

**代码生成基准测试** 随着大语言模型在代码生成领域的成功，越来越多的函数级代码生成基准测试被提出以评估其能力（Chen et al., 2021; Hendrycks et al., 2021; Austin et al., 2021; Jain et al., 2024a; Huang et al., 2024）。然而，这些基准测试不仅日益被最先进模型所饱和，而且其专注于面试风格的函数级编程挑战也无法代表真实世界代码库和软件工程任务的复杂性。

为解决这些限制，近年来提出了多种仓库级代码生成基准测试（Liu et al., 2023; Jain et al., 2024b; Jimenez et al., 2024）。

**3.1 符号与定义**

然而，仓库级

**语言识别**: 该段落为**英语** (English)。

---

**翻译 (中文译文)**:

长上下文不仅使代码生成更具挑战性，也给数据集生成带来了困难，因为它需要设置历史准确的执行环境、运行项目的测试套件，并提取详细的结果。由于需要大量人工操作，现有的数据集通常聚焦于相对较少的主流代码库。因此，这些数据集容易出现过拟合，往往缺乏多样性，并且很容易污染训练数据。

首先，我们引入符号来描述代码库级别的编码任务，该符号改编自 Mündler 等人（2024）。给定一个代码库 R，我们通过应用代码补丁 X 得到 R ◦ X。类似地，我们用 T 表示测试套子，在应用测试补丁 S 后表示为 T ◦ S。单个测试 t ∈ T 在执行环境 E 中针对代码库 R 执行时，可以通过（P）或失败（F）。我们写作：execE (t, R) ∈ {P, F}，并规定顺序 P > F。

一个代码库级别的编码任务可以表示为元组 (R, T, I, E, S ∗ , X ∗ )，其中 R 和 T 分别是原始代码库和测试套件，I 是问题描述，E 是执行环境，S ∗ 和 X ∗ 分别是参考测试补丁和代码补丁。通过在执行环境 E 中执行所有测试 ti ∈ T ◦ S ∗，先针对原始代码库（R），然后针对打补丁后的代码库 (R ◦ X ∗ )，我们得到参考测试行为 b∗i = (execE (ti , R) → execE (ti , R ◦X ∗ ))。我们称，例如 b∗i = F → P 的测试为失败转通过测试，因为它在应用参考修复之前失败，但在之后通过。我们规定偏序关系 F → P > F → F 和 P → P > P → F 成立。

**自动数据集生成** 这些挑战可以通过自动数据集生成来解决。自动数据集生成已成功应用于函数级基准测试，通过从编程挑战网站抓取任务并进行不同程度的人工后处理（Hendrycks 等人，2021；Jain 等人，2024a；Huang 等人，2024）。

Jimenez 等人（2024）将这些想法迁移到代码库级基准测试，自动抓取 GitHub 仓库、问题以及解决这些问题的相应拉取请求，创建了包含 12 个仓库和 2294 个实例的 SWE-Bench。然而，他们仍然需要手动创建所需的执行环境和测试命令。

现在的任务是仅给定 (R, T, I, E) 来生成补丁 X′，使得测试行为 b′i = execE (ti , R) → execE (ti , R ◦ X′) 匹配或优于参考结果，即对于所有测试 ti ∈ T ◦ S ∗，有 b′i ≥ b∗i。

Jain 等人（2024b）创建了 R2E，这是一个具有代码库上下文的函数级合成基准测试，他们通过抓取 GitHub 仓库并掩码要生成的函数来实现。他们通过为具有 setup.py 或 pyproject.toml 文件的项目应用默认方法来自动化设置，自动生成等价测试，并过滤掉此方法失败的所有实例。然而，这种方法会积极过滤掉具有更复杂安装过程的项目，不仅引入了……

**语言识别：** 该文本为**英语**（English）。

---

**翻译（英译中）：**

选择偏差，同时仅产生246个实例。

3.2  Setup Agent的需求
一个针对单个最新代码仓库的通用设置代理只需满足一个主要需求：正确性——它必须在解析测试结果之前提取并运行安装和测试命令。然而，基准测试生成（即根据编码任务的其余组件生成执行环境E）提出了额外的要求：历史准确性——基准测试实例基于特定的、通常是过时的代码仓库版本R。因此，执行环境E必须使用历史准确的依赖版本，以忠实地重现原始问题并避免版本不兼容问题。效率——为了生成数百个实例的数据集，设置代理必须足够高效，以保持总运行时间合理（几小时或最多几天）。粒度——评估代理成功需要从测试套件输出中解析出测试级别的结果。

在本工作中，我们通过引入并利用S ETUPAGENT自动提取每个任务实例的安装和测试程序，从而将更有趣的代码仓库级任务与全自动基准测试生成流程相结合，使我们能够高效地创建更大、更多样的基准测试。
Bouzenia & Pradel（2024）同时提出了E XECU，这是一个自动设置和测试仓库的工具。然而，它比S ETUPAGENT慢60倍，不支持历史状态，且无法提取测试级别的粒度。即使解决后两个缺陷，它仍然不可行地缓慢，例如，生成SWEE-Bench1需要超过4个月。

3.3  S ETUPAGENT
概述 S ETUPAGENT有三个阶段，如图1所示：（1）提取（图中），（2）迭代测试和改进（），以及（3）验证（）。在第一阶段，S ETUPAGENT从所有相关文件、参考网页以及可用的类似版本仓库的成功命令中提取安装和测试命令的初始版本。在第二阶段，S ETUPAGENT迭代执行安装然后测试命令，分析结果并更新命令。在第三阶段，S ETUPAGENT通过执行命令、提取测试结果来验证生成的命令，如果提议的命令则拒绝。

3. 自主环境设置
在本节中，我们首先概述用于基准测试生成的设置和测试代理的要求，然后描述我们为此目的开发的代理。
1

从约150个仓库推断。

3

---

自动生成代码仓库级编码任务的基准测试

输入：
您正在尝试使用以下命令安装和测试<项目名称>。
'''bash
<命令>
'''
修改命令以解决以下错误：
'''
...
ModuleNotFoundError: 没有名为'rustworkx'的模块
'''



---

## 论文 20

# The Atlas Benchmark: an Automated Evaluation Framework for Human Motion Prediction

**作者**: Andrey Rudenko, Luigi Palmieri, Wanting Huang, Achim J. Lilienthal, Kai O. Arras

**arXiv**: https://arxiv.org/abs/2207.09830

---

这段文字是**英文**的。以下是翻译成的中文学术版本：

---

**Atlas基准：人体运动预测自动评估框架**

arXiv:2207.09830v1 [cs.RO] 2022年7月20日

Andrey Rudenko¹'³, Luigi Palmieri¹, Wanting Huang¹'², Achim J. Lilienthal³, Kai O. Arras¹

**摘要**——人体运动轨迹预测是众多领域中自动驾驶系统的一项关键任务，近年来发展迅速。随着不同研究团队提出大量新方法，缺乏标准化的基准测试和客观比较正日益成为评估进展和指导后续研究的主要障碍。现有基准测试在实验设计和环境因素方面缺乏灵活性和全面性，难以满足相关实验的需求。本文提出Atlas基准测试，旨在统一框架下系统性地评估人体运动轨迹预测算法。Atlas提供数据预处理功能、超参数优化、内置流行数据集，并具备灵活配置和开展相关但尚未充分探索的实验的能力，以分析方法的准确性和鲁棒性。在Atlas的应用示例中，我们对五种基于模型和学习的预测器进行了比较，结果表明，在适当应用的情况下，早期基于物理的方法仍然具有相当的竞争力。这一结果证实了开发类似Atlas基准测试的必要性。

**一、引言**

运动预测算法的基准测试是一项具有挑战性的任务。评估结果可能受到多种因素的影响，如数据、参数、超参数和实验设计。为了揭示方法的特定能力或局限性，尤其是对于复杂的学习方法，需要精心设计和详尽的实验。影响因素包括观察期（即需要观察智能体多长时间才能准确预测其运动），以及如何从原始人员检测序列构建测试场景等。即使使用相同的数据集、评估指标和预测视界评估简单的恒定速度运动模型，由于测试场景生成和数据预处理的差异，评估结果可能仍然有所不同，如文献[1]和[2]中所报道的。文献[2]-[4]的多位作者已指出当前评估新预测方法的协议存在的局限性。

本文提出Atlas基准测试，作为在统一框架内对运动预测方法进行自动化基准测试的第一步，并系统性地变化参数。Atlas包含异构的人体运动轨迹数据集，能够自动提取测试场景，并可处理各种...

这段文字是**英文**。

以下是学术翻译（英文→中文）：

---

rmany huangwt1994@163.com
3 Mobile

瑞典厄勒布鲁大学机器人与嗅觉实验室

achim.lilienthal@oru.se

数据集库：
ETH, ATC, THÖR
新数据集导入脚本
自动化参数校准

数据集导入

1

2

方法库：
恒定速度模型，
正则化与预测性社会力模型，
SGAN, Trajectron++

下采样
插值
平滑
训练/验证集划分

预处理

3

4
预测

5

数据集特征：
标注帧率、
障碍物与语义地图、
目标位置

测试场景参数：
观测时长、
预测 horizon、
模拟感知噪声、
最小人数

评估指标：
ADE, FDE, Top k-ADE
与 FDE, NLP
运行时间估算

实验参数：
待变化因素

用于验证和初始校准的
合成场景样本

可视化

绘图
动画

图 1. Atlas 基准测试概述：（1）支持新数据集导入（带标签的检测流），（2）支持环境中的上下文线索，（3）预测超参数自动校准，（4）自动化参数化场景提取，（5）与预测方法的直接接口。

使用数据插值、下采样和平滑技术处理缺失和噪声代理检测[1]。与 TrajNet++ [5] 等现有技术相比，它提供了多个可调参数，如观测时长和预测 horizon，能够导入语义地图以及其他相关信息（如地图中的目标位置），支持概率预测结果评估，并可进行模拟感知噪声的鲁棒性实验。凭借这些特性，我们的基准测试可同时适用于短期和长期预测器。与 TrajNet++ 不同的是，它特别适合研究预测参数如何影响结果，而非像特定挑战中那样固定主要参数来生成排名分数。此外，我们的基准测试可直接对接超参数估计框架 SMAC3 [6]，以便在特定数据集上校准预测器。这一特性对于基于模型的预测器尤为有用，正如我们将在实验中展示的，与最新的基于学习的预测器相比，它们仍能表现出色。

我们通过评估几种流行的基于模型和基于学习的方法 [7]-[10] 来展示 Atlas，考察它们的预测准确性、在新环境中预测的能力以及对感知噪声和有限观测的鲁棒性。本文的结构如下：Sec. II 中我们定义运动预测基准测试问题并回顾现有技术，Sec. III 中我们介绍我们的基准测试。方法、实验设置和结果在 Sec. IV 中展示，Sec. V 总结全文。

II. 背景

轨迹预测方法旨在估计移动代理在未来某个时间范围内位置的概率分布。通常，运动预测器 u

该段落为**英文**。以下为学术性中文翻译：

---

将智能体当前或过去的运动状态（可能经环境当前或过去状态的增强）作为输入。环境由其他移动智能体的状态、静态障碍物的拓扑度量地图，以及可能与地图的部件、位置或对象相关联的语义信息来表示。

对于运动预测器的评估，我们考虑以下要素：数据集（如参考文献[11]–[16]所示的常用案例）、测试场景提取策略以及评估指标。所谓测试场景提取，是指将（智能体检测的）连续流进行转换：过去检测构成时间窗口为O_s ∈ R⁺秒（或位置数为O_p ∈ Z⁺）的观测历史，未来智能体状态在预测时域T_s ∈ R⁺秒（对应位置数T_p ∈ Z⁺）内形成需与真实值（GT）比较的预测结果。所用评估指标包括预测位置与GT位置之间的几何和概率距离估计量[4]。

Alahi等人首次提出了用于人体轨迹预测的基准测试，称为TrajNet[17]。TrajNet已被多位作者采用[18]–[26]，并实现了文献[1]中的评估策略：它使用ETH和UCY数据集，固定O_s = 3.2秒、T_s = 4.8秒，并采用几何指标ADE和FDE。TrajNet未涵盖主要参数O_s和T_s的变异性、环境中的障碍物，也未包含预测不确定性或鲁棒性的相关概念。

TrajNet++[5]通过纳入额外数据集对TrajNet进行了改进，并可进一步扩展新的数据集（以json格式存储）。该基准测试侧重于评估智能体交互建模方法，并提供将场景分类为运动类别的方法。它支持为每个行人在每一步预测多个离散位置，但不支持其他概率分布表示形式。然而，其主要局限性在于严格定义的测试参数，这使得评估仅限于固定参数O_s = 3.2秒和T_s = 4.8秒。此外，测试场景提取策略仅保证在每个场景中有一个目标行人具有请求的O_p + T_p个连续位置的完整轨迹。这与许多作者在预测时假设所有行人的历史轨迹均可获取的常见假设相矛盾[26]–[29]。从方法论角度而言，考虑具有完整观测轨迹的场景可以研究为所有智能体提供有限（或充足）观测的影响。这种方法能够隔离出因周围智能体观测不足而导致的预测误差，即使对目标智能体的观测时长已足够。

最后，TrajNet++不支持障碍物和环境语义信息。

基于以上认识，我们开发了Atlas基准测试，并采用自动化程序从数据集中提取具有灵活O_p和T_p参数的测试场景。

**语言识别：** 该段落为**英语**（English）。

---

**中文翻译：**

参数。Atlas 接受占用图和语义地图作为输入，支持多种参数化和非参数化不确定性表示形式，并包含针对观测轨迹添加噪声的鲁棒性实验。

运动预测基准测试领域的其他最新进展包括：基于 Argo 数据集和 Interaction 数据集，在 NeurIPS 2019²、NeurIPS 2020³ 和 CVPR 2020⁴ 自动驾驶研讨会中发布的挑战赛任务。NuScenes 也基于其数据集发布了独立的挑战赛⁵。这些挑战赛仅专注于自动驾驶场景且仅针对特定数据集。

在机器人领域，Hug 等人 [30] 提出了单一轨迹完整性检查基准（Single Trajectory Sanity Check Benchmark），目前仍在建设中⁶。虽然这些工作与 Atlas 在某些方面存在相似之处，例如均可用于研究数据预处理的影响，但其范围和灵活性有限，仅聚焦于单一用例、单一数据集以及排行榜的生成，且主要参数保持不变。

**三、Atlas 基准测试**

Atlas 包含五个核心要素：数据导入、预处理、实际预测阶段、评估与可视化，见图 1。该设计允许对不同预测算法进行接口对接和参数化配置，以实现灵活且高度自动化的评估与分析。

首先，数据集以及可用的环境信息（如目标点、障碍物和语义信息）被导入基准测试系统。随后，对原始数据进行预处理，包括降采样至用户定义的频率、漏检插值以及轨迹平滑处理。数据集准备完毕后，系统提取具有用户指定观测时长和预测时长、以及最少观测人数的测试场景。所有测试场景中人员的观测历史轨迹连同环境数据被显式接口传输至预测算法作为输入。预测结果根据真实值采用多种指标进行评估。最终，预测结果可通过图表或动画进行可视化展示。用于控制数据处理和基准测试设置的元参数存储在独立的 yaml 文件中，并通过 Jupyter notebooks 访问基准测试系统。

**A. 数据集**

基准测试用户可以导入任意符合 TrajNet++ [5] 定义的特定 json 文件格式的数据集，其中包含每次检测的时间戳、人员 ID 和位置信息。json 数据集格式还支持障碍物和语义信息。

² https://ml4ad.github.io/2019/
³ http://challenge.interaction-dataset.com/prediction-challenge
⁴ http://cvpr2020.wad.vision/
⁵ https://www.nuscenes.org/
⁶ https://stsc-benchmark.github.io/

关于观测时长（尤其是对于意图估计或人员检测存在噪声的情况），关键在于确保在每个 Op 帧中观测到测试场景中的所有人员。随后，测试场景连同环境信息一同传递至预测步骤。

**C. 预测**

图 2. Atlas 中的数据预处理流程。

这段文本是**英文**。以下是其**学术性的中文翻译**：

---

来自ATC数据集的示例轨迹，包含噪声和缺失检测（原始轨迹，位于上方）。同一轨迹展示了插值缺失检测、平滑处理以及添加受控噪声后的效果。

网格地图[31]，以及环境中的目标位置（即人们可能的目的地），这些因素可能会影响人们可能的行动目的地。

我们的基准测试目前包含以下三个数据集：
i) ETH [11]：该数据集包含在ETH校园两个位置（ETH和HOTEL）户外视频数据中的人员检测。
ii) ATC [14]：该数据集在日本一家购物中心录制，覆盖了一个人员密集的大型室内环境。
iii) THÖR [16]：该数据集捕捉了人员在带有静态障碍物的房间内的运动情况。它包含一个障碍物的设置（记为THÖR1，见图11）和三个障碍物的设置（我们称为THÖR3，见图12）。

这三个数据集来自不同国家并在不同环境中录制，这提高了场景的多样性，使得能够对不同社会和文化背景下的预测方法进行比较。关于数据集的深入分析，我们建议读者参考文献[16]和[4]。此外，我们还提供了导入任何带标注检测数据集的可能性，详见第二节。

**B. 数据预处理**

原始数据集通常包含噪声和标注伪影（例如缺失检测）[16]。因此，我们的基准测试在预处理步骤中提供了插值和平滑处理。此外，作为评估预测算法鲁棒性的一种方式，可以向每个检测添加高斯白噪声。图2展示了应用于ATC数据集中示例轨迹的预处理步骤。在根据平均标注频率检测到原始轨迹中的缺失帧后，我们对轨迹缺失部分进行线性插值。然后，使用移动平均滤波器来平滑噪声。最后，可以向每个检测添加服从N(0, σ²)分布的随机噪声，其中σ可自行设置。

预处理完成后，我们生成测试场景，包含观察长度Op和后续Tp帧的真实轨迹。由于预测性能可能强烈依赖于...

我们的基准测试提供了与预测模块的直接接口，在给定测试场景的此步骤调用该模块。这允许根据前几步定义的参数进行系统化变化，从而实现自动化评估。为了优化预测方法的超参数，例如[7]、[8]、[32]-[34]，Atlas包含了SMAC3优化器[6]的接口。

在用真实数据对预测模型进行基准测试之前，用户可以首先使用多个合成场景来验证其方法，这些场景模拟了人员与环境之间的基本交互，例如：个人和群体反向行走、路径交叉以及绕开障碍物（见图3中的几个示例）。例如，图3（左下）展示了两个...



---

## 论文 21

# Automated Composition of Agents: A Knapsack Approach for Agentic Component Selection

**作者**: Michelle Yuan, Khushbu Pahwa, Shuaichen Chang, Mustafa Kaba, Jiarong Jiang

**arXiv**: https://arxiv.org/abs/2510.16499

---

The text is written in **English**.

---

**Translation (English → Chinese):**

arXiv:2510.16499v1 [cs.CL] 2025年10月18日

自动化智能体组合：一种面向智能体组件选择的背包问题方法

Michelle Yuan∗† , Khushbu Pahwa∗ , Shuaichen Chang
Mustafa Kaba, Jiarong Jiang, Xiaofei Ma, Yi Zhang, Monica Sunkara
AWS Agentic AI
michelle.yuan@oracle.com
{pahwakhu,cshuaich,mdkaba,jiarongj,xiaofeim,yizhngn,sunkaral}@amazon.com

**摘要**

设计有效的智能体系统需要在动态且不确定的环境中实现智能体、工具和模型的无缝组合与集成。大多数现有方法依赖静态的语义检索方法来发现工具或智能体。然而，由于能力描述不完整以及检索方法的局限性，现有组件的有效复用和组合仍然面临挑战。组件选择过程中存在的问题在于：决策未能基于能力、成本和实时效用。为解决这些挑战，我们引入了一种受背包问题启发的结构化自动化智能体系统组合框架。我们的框架使组合智能体能够通过综合考虑性能、预算约束和兼容性，系统地识别、选择和组装最佳的智能体组件集合。通过动态测试候选组件并实时建模其效用，我们的方法简化了智能体系统的组装过程，并促进了资源的可扩展复用。使用Claude 3.5 Sonnet在五个基准数据集上进行的实证评估表明，我们基于在线背包的组合器始终位于帕累托前沿，在显著降低组件成本的同时实现了更高的成功率。在单智能体设置中，与检索基线相比，在线背包组合器的成功率提升高达31.6%。在多智能体系统中，当从包含100多个智能体的智能体库中选择智能体时，在线背包组合器将成功率从37%提高到87%。显著的性能差距证实了我们的方法在各种领域和预算约束下具有强大的适应性。

**1 引言**

有效智能体系统的设计处于人工智能研究的前沿，有望实现能够进行复杂推理、工具操作和协作问题解决的自主智能体。然而，随着人工智能组件生态系统的扩展——模型、API和专业智能体激增——一个关键的瓶颈随之出现：选择悖论[23]。虽然模块化复用相较于从头构建系统具有明显优势，但开发者面临的是可能的配置组合爆炸，每种配置都包含隐藏的约束和不可预测的交互。依赖人工策展或基于元数据的检索[29]的传统方法受到三个根本性局限的困扰：能力描述不透明、很少与实际性能相匹配，以及忽视成本-效用权衡的短视选择标准。

**语言识别 (Language Identification):**
该段落为**英语** (English)。

---

**中文翻译 (Chinese Translation):**

本文将智能体组合问题形式化为背包问题，人工智能系统设计者需在成本约束下选择性能最优的组件。我们提出了作曲家智能体（composer agent），负责通过迭代的发现与适应过程来选择组件。作曲家并非将现有工具或智能体视为固定的构建块，而是通过有针对性的测试持续探测其实际能力。沙盒试验不仅测量组件声称能做什么，更重要的是衡量其在不同条件和交互下的可靠表现。对于单智能体系统，这意味着为任务选择合适的工具，同时在准确性与计算成本之间取得平衡；对于多智能体团队，则涉及编排技能互补而非重复的子智能体。

我们方法的独特之处在于基于实时性能来评估智能体组件的价值。此前的研究将组件选择视为基于静态元数据的一次性决策，而作曲家则通过实证验证不断优化其选择。例如，在组合信息检索智能体时，作曲家可能最初为科学查询选择专用搜索工具，但通过测试发现单一的通用搜索工具可以处理跨领域的查询。另一方面，某些任务可能需要医学领域的深度专业知识，因此科学搜索工具比通用网络搜索更有用。这种动态评估能力在需求和组件变化不可预测的真实部署场景中尤为珍贵。我们的工作做出了以下三项贡献：

1. 将智能体组合形式化为联合考虑能力、成本和兼容性的约束优化问题，填补了模块化人工智能设计 [10] 与运筹学 [41] 之间的空白。

2. 建立了作曲家智能体的工作流程，将任务描述解析为技能，基于实时测试评估智能体组件的效用值，然后为特定领域返回最优的智能体组件。

3. 在多样化领域的实证验证表明，经过成本调整的性能持续提升（相比基于检索的基线方法，性能提升高达 80%）。

机器学习系统的设计至关重要，因为糟糕的组织会导致不稳定的依赖、管道丛林以及许多其他类型的隐藏技术债务 [28]。我们的工作是优化这一过程、减少这些问题并维护良好组合的智能体系统的一步。正如我们在后续章节中探讨的，我们的方法适用于组合单智能体和多智能体系统。

2

相关工作

工具检索

**语言识别：该段落为英语（English）。**

---

**翻译（中文简体）：**

AI智能体的 novelty 源于API与大型语言模型（LLM）的集成[27, 24]。由于工具是智能体的关键组件，此前的研究工作重点关注检索方法以选择最合适的工具。Qin等人[25]基于RapidAPIs收集了大规模的工具数据集，并训练了基于BERT的工具检索器。Shi等人[29]指出，工具检索是一个困难的问题，因为常用的检索器难以捕捉用户意图并选择最相关的工具。RAG-MCP[9]将RAG与模型上下文协议（MCP）相结合，以改进LLM选择外部工具的方式。近年来，更多研究强调由于工具选择不当而导致的智能体脆弱性问题[6, 22]。

**智能体系统设计与优化**

Hu等人[10]引入了智能体系统自动化设计（Automated Design of Agentic Systems，ADAS）问题，其目标是"自动创建强大的智能体系统设计，包括发明新颖的构建模块和/或以新方式组合它们"。从概念上讲，ADAS的构建模块包括新颖的提示词、工具使用和工作流。在本文中，我们专注于ADAS的一个子集，即智能体系统的自动化组合。我们的设定假设底层构建模块已存在，算法挑战在于选择这些组件的最优子集。解决这一问题的常用方案包括工具检索和智能体选择。在智能体选择方面，现有研究将问题视为图优化，其中节点代表各个智能体，边代表通信通道。DyLAN[17]引入了智能体选择问题，并训练前馈网络来优化选择过程。AgentPrune[40]将选择扩展到目标减少通信冗余。多智能体架构搜索[39]优化了一个智能体超网络，然后以查询相关的方式对多智能体架构进行采样。Wu等人[35]联合优化智能体提示词和工具描述，以提高工作流效率。组件选择问题还涉及分布式约束优化问题（DCOP）[8]，其中传统智能体为变量分配值以在满足约束条件的前提下最小化全局成本函数。DCOP技术已被应用于服务选择和组合问题，特别是面向QoS的Web服务组合[4]。

**背包算法**

背包问题作为经典的优化挑战，在算法研究中已被广泛研究[19, 7, 14]。离线算法，如动态规划[2]和分支限界法[18]，假设预先完全了解所有物品信息，从而能够获得最优解。

这是英语（English）写的学术论文文本。

以下是中文翻译：

---

对于静态输入的离线背包算法。然而，在线背包算法则在没有未来信息的情况下逐个处理物品。一种常见的方法是ZCL算法[41]，该算法提出了价值与重量的比值阈值，并根据背包的容量进行动态配置。其他理论上具有竞争力的方法进一步扩展了ZCL算法[11, 16]。

**问题定义**

胡等人[10]提出了智能体系统自动设计（Automated Design of Agentic Systems，ADAS）问题，其目标是自动构建和配置智能体系统的组件。ADAS旨在通过算法发现自动完成整个过程，而不是手动设计智能体架构。智能体的组件包括工具、模型、提示词和工作流。我们的工作解决了这一愿景中的一个关键子问题：智能体组合，其重点不是从零开始发明新的构建块，而是从库存中最佳地选择和组合现有组件以满足特定的任务需求。

我们如下定义智能体组合问题（图1）。假设存在一个目标任务τ，其描述为x，智能体系统的预算为B。假设存在一个组件集合A = {a_i}，其中每个组件具有成本c_i和描述d_i。令p_τ(S)表示基于子集S为任务τ构建的智能体系统的成功率。成功率应反映各组件的性能及其协同工作时的整体效果。智能体组合的目标是为该领域找到最佳的组件子集S*：

S* = arg max p_τ(S)

s.t. S ⊆ A

∑ c_i ≤ B

a_i ∈ S

该 formulation 直接类似于经典的背包问题，其中组件对应于具有关联权重（成本）和价值（成功率）的物品。然而，在实际设置中出现了三个关键区别。首先，真实的成功概率最初是不确定的，必须通过迭代测试来估计，从而将问题转化为在线背包优化的变体。其次，组件经常表现出非加性的相互作用，要么是正向的协同效应，要么是有害的冲突，这可能会在目标函数中引入二次耦合项。第三，库存本身会随着新组件的添加或现有组件的更新而动态演变，这就需要支持增量重计算的求解方法。

图2：我们提出的在线背包组合器的概述。与离线基线类似，工作流程从根据给定的任务描述生成技能和查询开始（附录A.1）。然后，组合器根据技能描述从库存中检索组件。对检索到的组件分别进行测试。如果价值与价格之比满足在线背包阈值，则将其添加为智能体系统的一部分。否则，继续搜索。

这段文字是**英语**（English）。

下面是翻译：

---

尽管存在这些复杂性，背包问题类比既提供了概念清晰性，也带来了算法上的优势。其实际应用场景涵盖企业内部开发团队从内部工具注册库中进行选择的场景，以及市场环境中自动化智能体从商业API清单中组合解决方案的场景。在所有情况下，核心挑战始终保持一致：在可能的配置组合爆炸中进行导航，以识别满足严格性能要求的具有成本效益的组合。第4节中介绍的编排智能体通过将约束优化与经验验证技术进行新型综合来应对这一挑战。

**4. 编排智能体：从语义检索到背包选择**

编排器可以广义化为一个函数f，该函数从清单A中返回组件的一个子集S：对于给定的任务τ和预算B，f(A, τ, B) = S ⊆ I。理想情况下，我们希望f返回方程1的最优解S*。然而，我们注意到由于这些组件的动态行为以及评估其真实价值的困难，寻找该解决方案并非易事。下面，我们列出并解释我们提出的编排器。

恒等编排器：原始编排器的第一个示例是简单返回清单中的所有组件，其中f(A, τ, B) = A。我们称之为恒等编排器，因为它类似于一个恒等函数。

检索编排器：其次，在设计更智能的编排器时，我们应该考虑利用自然语言描述。回顾第3节，假设任务τ有一个描述，清单中的每个组件ai都有一个描述di。许多现有工作依赖嵌入模型进行语义检索，以获取类似工具的组件（第2节）。然而，此处的挑战在于确定检索的查询内容。我们还希望确保检索到的组件不冗余。为此，我们为检索编排器增加了将任务描述解析为技能列表的能力。每个技能都应是任务完成所需的核心能力。表1展示了我们使用Claude 3.5 Sonnet在GAIA任务描述运行中生成的技能示例。

给定任务τ及其描述x，检索编排器首先生成技能列表m ∈ M，每个技能包含名称和描述。然后，检索编排器使用技能名称和描述查询清单，以找到最相关的组件。选定的子集S将基于技能的前1检索结果。请注意，语义匹配已在先前的工作中用于智能体发现[5]，更广泛地用于服务发现[20]。因此，我们在实验中纳入了一个仅检索的基线以进行公平比较。

**表1**：基于GAIA任务描述使用Claude 3.5 Sonnet生成的技能和测试查询示例。生成的信息随后用于为智能体选择每个工具。

名称 | 描述 | 测试



---

## 论文 22

# SWE-QA: Can Language Models Answer Repository-level Code Questions?

**作者**: Weihan Peng, Yuling Shi, Yuhang Wang, Xinyun Zhang, Beijun Shen

**arXiv**: https://arxiv.org/abs/2509.14635

---

**语言识别**：该段落为**英文**（English）。

---

**中文翻译**：

理解并推理整个软件仓库是智能软件工程工具的一项基本能力。现实世界的开发很少涉及对孤立函数或小型代码片段的推理；相反，开发人员必须导航大型、相互关联的代码库，追踪多个文件之间的依赖关系，并综合架构知识来回答复杂问题。

大型语言模型（LLMs）的最新进展已在代码理解方面展现出潜力[6, 11, 34, 43]，然而大多数现有评估[10, 13, 16, 17, 25]针对的是孤立的代码片段。这些设置未能捕捉现实世界仓库的复杂性，在现实世界中，有效的理解和推理通常需要浏览多个文件、理解软件架构，并将答案基于长程代码依赖关系。

在本文中，我们提出了SWE-QA，这是一个仓库级代码问答（QA）基准测试，旨在促进现实代码环境中自动化QA系统的研究。SWE-QA包含576个高质量问答对，涵盖 diverse categories，包括意图理解、跨文件推理和多跳依赖分析。为构建SWE-QA，我们首先从11个热门仓库爬取了77,100个GitHub issue。基于对这些issue中提取的自然发生的开发者问题进行分析，我们开发了一个仓库级问题的两级分类法，并为每个类别构建了一组种子问题。对于每个类别，我们人工整理并验证了问题及其相应答案。作为原型应用，我们进一步开发了SWE-QA-Agent，这是一个智能体框架，其中LLM智能体通过推理和行动来自动寻找答案。我们在不同上下文增强策略下对六个先进的LLM进行了评估。实验结果突出了LLM在解决仓库级QA方面的潜力，尤其是我们的SWE-QA-Agent框架，同时也揭示了开放性挑战并指明了未来的研究方向。

The language of the provided text is **English**.

---

**Translation (Chinese):**

作者联系信息：彭伟涵，上海交通大学，上海，中国，peng-weihan@sjtu.edu.cn；施玉凌，上海交通大学，上海，中国，yuling.shi@sjtu.edu.cn；王宇航，上海交通大学，上海，中国，lingbo_2022@sjtu.edu.cn；张新云，上海交通大学，上海中国，xinyunz@nvidia.com；沈北均，上海交通大学，上海，中国，bjshen@sjtu.edu.cn；顾晓东，上海交通大学，上海，中国，xiaodong.gu@sjtu.edu.cn。

这些基准测试未能捕捉到真实代码库的复杂性，包括架构、跨文件依赖关系、生命周期流程和设计理念，这些需要更深层次的、多跳的代码结构、语义和意图理解[23, 41]。虽然近期像CoReQA[3]这样的代码库级研究已开始解决代码库级理解问题，但它们专注于问题解决而非真正的代码模块理解，缺乏对现实软件开发场景中必不可少的多样化推理模式和多跳依赖关系的全面覆盖。

为解决这一局限性，我们提出了一个代码库级代码问答（QA）基准测试，旨在评估大语言模型回答基于真实代码库问题的能力。SWE-QA包含576个高质量的问答对，涵盖多样化类别，包括意图理解、跨文件推理和多跳依赖分析。为了捕捉真实软件开发中固有的多样化推理需求，我们从SWE-bench[12]使用的11个流行代码库中爬取了77,100个GitHub问题。基于对这些issue中自然产生的问题的分析，我们开发了一个代码库级问题的两级分类法，并为每个问题类别构建了一组种子问题。在我们的分类法和种子模板的指导下，我们使用大语言模型从12个代码库实例化候选问题，然后进行人工筛选和细化，最终每个代码库获得48个高质量的问答对。然后，基于检索到的上下文回答每个问题，以获得强 大语言模型的初步答案。随后对这些初步答案进行人工审查和细化，确保正确性、完整性和清晰度。这一过程产生了基于代码上下文的高质量参考答案，形成了具有多样化推理要求的可靠且可扩展的基准测试。作为原型应用，我们进一步开发了SWE-QA-Agent，这是一个智能体框架，其中大语言模型智能体通过推理和行动自动寻找答案。该智能体利用各种工具辅助推理和检索信息，使其特别有效。

该文本是**英语**。以下是中文翻译：

---

用于跨文件和复杂推理问题的评估。

我们使用多种上下文增强方法，在 SWE-QA 数据集上评估了六种先进的大语言模型，包括 Devstral-Small-1.1 [20]、Qwen2.5-Coder-32B-Instruct [24]、Qwen2.5-72B-Instruct [30]、DeepSeek-V3 [5]、GPT-4o [21] 和 Claude 3.7 Sonnet [2]。我们设计了一个基于评分标准的评估系统 [18,33]，其中高性能 LLM（如 GPT-5 [22]）从五个维度对模型输出进行评分：正确性、完整性、相关性、清晰度和推理过程。为减少评估偏差，我们对候选答案进行匿名化处理，随机排列答案顺序，并结合人工抽查和校准提示进行验证。

结果表明，大语言模型在仓库级代码问答任务中展现出良好的潜力。尽管无上下文的直接提示效果较差，但标准检索增强生成（RAG）方法显著提升了性能。特别是我们提出的 SWE-QA-Agent 智能体框架配合 Claude 3.7 Sonnet 取得了最佳表现，总体得分达到 47.82。人工评估也验证了这一发现，SW E-QA-Agent 在所有维度上均获得最高评分。为进一步分析性能表现，我们根据问题分类体系和不同仓库类型对结果进行了细分。模型在概念性的"What"和"Why"问题表现优异，但在需要多跳推理的 Procedure性"How"和定位性"Where"问题上表现欠佳。不同仓库的性能也存在差异，例如"pytest"仓库尤其具有挑战性。

总体而言，实验结果突显了大语言模型在解决仓库级问答任务方面的潜力，特别是我们的 SWE-QA-Agent 框架，同时亦揭示了尚未解决的挑战，并指明了未来的研究方向。

综上所述，我们的贡献如下：

---

**期刊信息：** 期刊第 1 卷第 1 期，文章编号。出版日期：2025 年 9 月。

---

**SWE-QA：语言模型能否回答仓库级代码问题？**

**第一阶段：种子问题收集**

**第二阶段：问题实例化**

GitHub 问题 → 用户

GitHub 仓库

**第三阶段：答案收集**

Tree Sitter → 人工

**第四阶段：数据验证**

人工 → LLM

LLM 分析仓库 → 问题收集 → LLM 模板优化

种子问题 → 生成问题

收集答案 → 修订答案

问题 → 问答对

仓库元数据 → GitHub 仓库

过滤问答 → SWE-QA 基准

问题

**图 1. 基准构建工作流程。**

• 我们构建了 SWE-QA，这是一个仓库级代码问答基准，包含来自 12 个不同开源 Python 仓库的 576 个高质量问答对，用于评估全面的仓库级代码理解能力。

• 我们提出了 SWE-QA-Agent，这是一种智能 ReAct 风格智能体，旨在回答仓库级问题。该智能体利用多种工具辅助推理和信息检索，特别适用于跨文件和复杂推理问题。

• 我们不仅构建了基准，还提供了一个灵活的管道，允许用户使用种子问题高效地为任何新仓库生成问答数据集。我们的基准、代码和实验结果可供复现。

The paragraphs are written in **English**.

---

**Translation (English → Chinese):**

n 可通过以下地址公开获取：
https://github.com/peng-weihan/SWE-QA-Bench。

SWE-QA：代码库问答新基准

在本节中，我们介绍 SWE-QA，这是一个专为代码库级问答设计的新型基准。如图 1 所示，我们的基准构建流程包含四个主要阶段：种子问题收集、问题实例化、答案收集和数据验证。以下各小节将详细阐述每个阶段。

2.1 种子收集与分类体系构建

为确保 SWE-QA 反映真实世界软件工程的复杂性，我们首先进行了一项实证研究，以了解开发者在大型代码库中工作时所提问的类型。我们系统地收集并分析了大量来自 GitHub issues 的问题，以构建一个全面的代码库级问题分类体系。该分类体系是我们基准构建的基础。

我们的数据收集过程始于从 SWE-bench [12] 中使用的 11 个热门代码库（不包括已禁用 issues 功能的 Django）抓取 77,100 个 GitHub issues。为了聚焦于实质性讨论，我们筛选了正文字符数至少为 1,000 的 issues，最终得到包含 41,955 个 issues 的数据集。鉴于 issues 通常包含大量描述性文本，我们采用大型语言模型解析每个 issue，并提取与代码理解相关的显式问题。这一过程产生了 127,415 个独立问题，平均每个 issue 有 3.04 个问题。提示词的详细信息见补充材料 [1]。收集到的 issues 在各代码库中的分布如图 2 所示。

我们首先手动分析了一个包含 1,000 个问题的随机样本，通过迭代开放编码过程识别重复模式并理解开发者意图。这产生了一个结构化的两级分类体系，用于代码库级问答，如表 1 所总结。第一级根据问题的主要疑问词进行分类：What（事实性询问）、Why（因果解释）、Where（位置识别）和 How（过程性解释）。第二级进一步将问题细分为 12 个精细的用户意图，例如依赖追踪、设计原理澄清等。

图 3 显示了问题类型的分布。

该文本是**英文**。以下是中文翻译：

---

在软件工程活动中，设计与算法分析是常见的研究主题。

在确定分类体系后，我们使用了一个强大的语言模型（GPT-5）来对剩余的126,415个问题进行分类，将其归入一级/二级类别，并采用简洁的标签提示和一致性检查。结果分布如图3所示：“如何”（How）类问题最为常见（35.2%），主要关注系统设计和算法等实现细节。“在哪里”（Where）类问题次之（28.4%），表明开发者经常需要定位功能、数据流或特定标识符。“为什么”（Why）类问题占语料库的23.1%，这类问题探讨设计原理和目的。“是什么”（What）类问题寻求定义或架构摘要，占其余的13.3%。这一分布表明，开发者的大量查询集中在程序性知识和定位性知识上，这凸显了问答系统需要具备对代码结构和实现进行深度推理的能力。

**2.2 问题实例化与扩展**

基于该分类体系，我们为每个用户意图类别创建了一套抽象的种子模板。例如“哪些子类继承了<Class>类？”和“<模块>如何在<条件>情况下实现<功能>？”等模板，捕捉了重复出现的问题的本质。如表1所示，这些模板作为生成多样化、上下文具体的问题实例的蓝图，针对各个代码仓库进行定制，确保我们的基准测试覆盖了全面的推理挑战。

本阶段的目标是生成针对目标代码仓库的上下文具体问题实例。为了提取相关的上下文信息，我们使用树解析器（tree-sitter）[31]——一种语言无关的解析工具——来解析每个代码仓库的结构。这一过程生成了核心元素及其关系的类型化图（图4），其中节点包括仓库（Repository）、文件（File）、代码片段（Code Snippet）、类（Class）、方法（Method）、属性（Attribute）、函数（Function）、参数（Parameter）和变量（Variable）。边表示包含关系（例如，类→方法/属性，文件→代码片段）。此外，每个函数追踪它调用的函数和调用它的函数，而每个文件则记录其导入信息，揭示了文件间的依赖关系。总体而言，该结构同时捕获了类型级和调用级的依赖关系，为多跳推理提供了必要的基础。

我们通过围绕焦点元素（例如一个类或方法）选择一个紧凑的子图，并将其与第一阶段的种子模板相结合来实例化问题。子图提供了签名、定义、类成员资格、文件路径、导入信息以及传入/传出调用，确保问题实例包含充分的上下文信息。

，第1卷，第1期，文章。出版日期：2025年9月。

---

**SWE-QA：语言模型能否回答**



---

## 论文 23

# SWE-bench Goes Live!

**作者**: Linghao Zhang, Shilin He, Chaoyun Zhang, Yu Kang, Bowen Li

**arXiv**: https://arxiv.org/abs/2505.23419

---

**语言识别**：该段落为**英文**。

---

**中文翻译**：

SWE-bench 上线！

arXiv:2505.23419v1 [cs.SE] 2025年5月29日

Linghao Zhang1∗ Shilin He†1 Chaoyun Zhang1 Yu Kang1 Bowen Li2
Chengxing Xie2 Junhao Wang1 Maoquan Wang1 Yufan Huang1 Shengyu Fu1
Elsie Nallipogu1 Qingwei Lin1 Yingnong Dang1 Saravan Rajmohan1 Dongmei Zhang1
1
Microsoft 2 上海人工智能实验室
排行榜

GitHub

HuggingFace

**摘要**

问题解决任务，即模型生成补丁以修复真实世界中的bug，已成为评估大语言模型（LLM）能力的关键基准测试。尽管SWE-bench及其变体已成为该领域的标准基准，但它们存在关键局限性：自首次发布以来未更新，覆盖的代码库范围狭窄，且实例构建和环境配置高度依赖人工操作。这些因素阻碍了基准测试的可扩展性，并带来过拟合和数据泄露的风险。本工作提出了**SWE-bench-Live**3，这是一个可实时更新的基准测试，旨在克服上述挑战。我们的首次发布包含1,319个任务，这些任务来源于2024年以来创建的真实GitHub问题，覆盖93个代码库。每个任务都配有专用的Docker镜像，以确保可重复执行。我们的基准测试核心是**REPO LAUNCH**，这是一个自动化筛选流程，可简化从实例创建到环境配置的整个过程，消除人工瓶颈并实现可扩展性和持续更新。我们对一系列最先进的代理框架和大语言模型在SWE-bench-Live上进行了评估，结果显示与SWE-bench等静态基准测试相比，即使在受控评估条件下也存在显著的性能差距。为了更好理解这一差异，我们对代码库来源、问题时效性和任务难度进行了详细分析。通过提供基于实时代码库活动的全新、多样化且可执行的基准测试，SWE-bench-Live能够对动态真实软件开发环境中LLM和代理进行严格且抗数据泄露的评估。

**1 引言**

大语言模型（LLM）从根本上重塑了软件工程领域[1]，为Cursor[2]和GitHub Copilot[3）等工具提供了核心动力，这些工具现已成为现代开发工作流程的组成部分。这些模型已改变了软件开发生命周期的关键阶段——自动化代码生成、bug检测和问题解决——从而显著提升了开发者生产力。为了系统评估LLM在这些任务中的能力，人们开发了各种精心设计的基准测试，包括HumanEval[4]、MBPP[5]、SWE-bench[6]、DI-bench[7]和OpenRCA[8]。这些基准测试在识别LLM在不同编程和维护场景中的优势和局限性方面发挥着重要作用。

其中，SWE-bench[6]及其变体（如多模态SWE-bench[9]和Multi-SWE-bench[10]）已成为评估LLM在问题解决任务上的标准基准。

该文本为英文。

**翻译：**

模型需要理解复杂的代码库、与执行环境交互，并生成能够修复实际问题的补丁。然而，随着大语言模型的快速发展，现有基准测试存在几个关键局限，削弱了它们的持续实用性：

| 数据集 | 日期 | 实例数 | 仓库数 | 真实/合成 | 整理方式 |
|--------|------|--------|--------|------------|----------|
| SWE-bench [6] | 2023年10月 | 2,294 | 12 | 真实 | 手动 |
| SWE-bench-Verified [11] | 2024年8月 | 500 | 12 | 真实 | 手动 |
| SWE-Gym [12] | 2024年12月 | 2,438 | 11 | 真实 | 手动 |
| Multi-SWE-bench [10] | 2025年4月 | 1,632 | 39 | 真实 | 手动 |
| SWE-smith [13] | 2025年4月 | 50,000 | 128 | 合成 | 半手动 |
| **SWE-bench-Live（本文）** | **2025年4月** | **1,319（自2024年起）** | **93** | **真实** | **自动** |

表1：与现有问题解决基准测试的对比

1. **过时性**。SWE-bench及其衍生版本自最初发布以来（2023年10月）从未更新，使其成为静态基准测试。由于大语言模型是在海量难以追溯的语料库上训练的，这些静态数据集存在数据泄露风险，因为它们可能已被无意纳入模型训练数据。这引发了对以下问题的担忧：较新的模型是在取得真正可泛化的进展，还是仅仅在记忆基准测试内容，从而降低了基准测试区分模型能力的效果。

2. **仓库覆盖范围有限**。这些基准测试仅源自12个仓库的少量集合，限制了代码库、领域和编程实践的多样性（详见表1）。这种狭窄的范围削弱了评估的泛化性和鲁棒性。

3. **对人工的严重依赖**。构建SWE-bench类任务的实例需要大量人力：识别适当的问题-解决配对、定位相关测试、配置可运行环境、编写测试命令，以及验证整个工作流程。⁴ 这一过程资源密集型，并造成了可扩展性瓶颈。

为了应对这些挑战，我们推出了SWE-bench-Live，这是一个动态、自动化且可扩展的基准测试，旨在评估大语言模型在真实世界问题解决任务上的表现。

这是英文（English）。

**翻译（Chinese Translation）:**

与近期专注于算法编程问题的LiveCodeBench [14]等努力不同，SWE-bench-Live是首个持续更新的基准测试，专门针对复杂的仓库级任务，这些任务需要多文件推理、环境配置和可复现执行。图1展示了SWE-bench-Live的构建流程。该框架的核心是REPO LAUNCH，一个完全自动化系统，通过简化从问题挖掘到环境打包的整个流程来消除人工瓶颈。具体而言，REPO LAUNCH采用代理式端到端工作流程，通过识别相关指令文件、选择合适的基础镜像、安装依赖、构建项目并验证其测试套件来设置基于Docker的环境。这种自动化实现了持续更新、广泛的仓库覆盖和可扩展的数据集扩展。SWE-bench-Live的当前版本包含1,319个问题解决任务，这些任务来源于2024年以来创建的93个真实GitHub仓库。与现有基准测试相比，这在新鲜度、多样性和规模方面取得了显著进步（见表1）。

例如，Multi-SWE-bench [10]花费约一年时间才创建了1,632个基准测试实例，参与的专家标注者达68人。

我们评估了三个领先的代理框架（即OpenHands [15]、SWE-Agent [16]和Agentless [17]），并结合四种最先进的LLM（即GPT-4.1、GPT-4o、Claude 3.7 Sonnet和DeepSeek V3）进行测试。我们的结果显示，SWE-bench-Live与之前的静态基准测试之间存在显著的性能差距。例如，表现最佳的代理-模型组合——OpenHands配合Claude 3.7 Sonnet——在SWE-bench-Live上仅实现了19.25%的解决率。即使在相同评估协议的控制条件下，同一设置在SWE-bench Verified上实现了43.20%的解决率，是其在SWE-bench-Live上表现的两倍多。按仓库来源和实例难度的进一步分析表明，这种差异不仅源于基准测试的熟悉度，还源于SWE-bench-Live更大的多样性。这些发现凸显了静态、人工策划的基准测试的局限性，并强调了对动态、自动更新的测试平台的需求，以推进强大且可泛化的代码代理系统。

我们的主要贡献总结如下：
• 我们推出了SWE-bench-Live，这是一个抗污染、可复现且可持续更新的基准测试，专门针对现实世界的问题解决任务。它反映了软件开发的动态特性，并与之前的基准测试相比提供了更广泛的仓库覆盖。
• 我们提出了REPO LAUNCH，这是一个完全自动化的基准测试构建流程，将数据策划、环境配置和测试验证无缝集成到一个 cohesive且可扩展的系统中。
• 通过实验评估，我们观察到领先的代理框架在处理真实世界问题解决任务时表现不佳，这表明需要更先进的代理能力和更接近实践的评估标准。

**语言识别**：该文本为**英语**（English），是一篇关于代码大语言模型（Code LLM）评估基准的学术论文片段。

---

**中文翻译**：

在SWE-bench-Live上的结果表明，相较于现有基准，该方法存在显著的改进空间，同时也为无污染基准的提升提供了重要契机。

综上所述，这些贡献确立了一个全新且稳健的标准，用于评估代码大语言模型及基于智能体的系统的真实能力。

2

相关工作

编程基准测试。早期关于程序合成和缺陷修复的基准测试主要聚焦于单文件、合成任务，如HumanEval[4]和MBPP[5]，这些任务并不能反映真实代码库的复杂性。为了更贴近实践应用，SWE-bench[6]引入了问题解决任务，要求模型为GitHub仓库生成经验证的补丁以解决相应问题。此后出现了众多扩展版本，包括用于JavaScript和UI截图的多模态SWE-bench[9]，以及支持Java和Rust等多种语言的MultiSWE-bench[10]。尽管这些数据集具有重要影响，但它们均为静态数据集：一次性收集，最多覆盖数十个依赖人工密集型环境构建的仓库。这产生了两个局限性。首先，模型可能会对固定测试集产生过拟合，从而高估表面上的进展。其次，公开任务可能导致数据污染，即基准测试实例泄露到预训练语料库中[18,19]。近期出现的“动态”数据集（如LiveCodeNBench[14]）通过在问题发布后持续流入算法问题来缓解污染问题，但它们未能解决更具挑战性的仓库级场景——这需要多文件推理并在真实环境中执行。SWE-bench-Live是首个满足这些要求的开放性、持续更新的基准测试。

代码智能体。基于上述基准测试，近期研究致力于创建能够搜索、编辑和测试大型代码库的自主代码智能体。代表性系统包括SWE-Agent[20]、OpenHands[15]、Agentless[17]，以及合成数千个类SWE-bench实例的训练框架[21,13,22]。这些智能体报告了令人瞩目的 headline 数据，但其评估几乎完全依赖于静态离线数据集。因此，性能提升可能部分源于对泄露解决方案或配置细节的记忆，而非真正的技术进步。SWE-bench-Live通过促使智能体修复此前未见、持续涌现的真实世界缺陷来弥补这一差距，这些修复在完全可复现的Docker镜像中执行，从而揭示了因过时测试套件而被隐藏的失败模式，并为代码智能体和大语言模型提供了可信的评估标准。

3

SWE-bench-Live

针对真实世界GitHub仓库的问题解决任务，SWE-bench作为评估基于大语言模型的系统的编码能力的实用代理。问题解决任务定义如下：给定一个代码仓库及其关联问题，要求一种方法（如大语言模型智能体）生成能够解决问题并通过测试用例的补丁。

虽然SWE-bench-Live采用了与SWE-bench相同的任务定义，但它引入了一种新颖的完全自动化流水线，实现了可扩展的持续更新。

这段文字是**英文**。以下是翻译成的中文学术版本：

---

**可扩展且持续更新的基准测试构建流程。** 该自动化机制能够产生更多最新的实例以及更广泛的代码库覆盖范围。SWE-bench-Live的首次发布包含了1,319个任务实例，创建于2024年1月至2025年4月期间，涵盖了93个真实世界的代码库。

**流程概述。**如图1所示，SWE-bench-Live的构建遵循三阶段流程。首先，从热门代码库开始，我们识别那些通过拉取请求（PR）解决的GitHub问题。随后，我们应用所提出的REPO LAUNCH——一种代理式方法，可为每个候选实例自动设置基于Docker的执行环境。最后，我们对每个实例进行多轮测试执行，以验证其是否持续展现预期的解题测试行为，并最终确定有效实例。

得益于其完全自动化的流程，SWE-bench-Live可以在最少（理想情况下为零）人工干预的情况下进行维护。我们计划每月更新SWE-bench-Live，持续为社区提供最新的评估数据集。这使得能够在不断演变的真实世界环境中对AI系统的问题解决能力进行无污染、严格的评估。

**3.1 任务定义**

生成补丁
amoffat/sh

评估补丁
问题 #744 于5天前开启

执行环境

当使用 sh 2.x 时……

docs
src/sh.py

它应该是一个 kwarg 来保留……

补丁

② 问题描述

diff

git a/sh.py b/sh.py

解析测试日志

tests

测试项

README.rst
...

> git apply patch
> pytest -rA

前置

后置

test_async_return_cmd
test_space_sep

LLM x 代理

test_bool_values

😺

① 代码库内容

图2：问题解决任务要求模型生成一个补丁来解决给定问题，其正确性通过测试执行来评估。

问题解决是由SWE-bench [6]引入的任务，用于基准测试AI编码能力。简而言之，它模拟了开发者提交拉取请求来解决问题这一过程。图2说明了问题解决任务的定义。

**生成补丁。** 任务输入包括问题的陈述，即问题报告者所写的描述，以及问题提交时代码库的快照（通过重置到base_commit获得）。模型可以访问代码库的完整内容，其任务是生成一个修复给定问题的补丁，类似于拉取请求中提交的文件更改。在实践中，预期输出为.diff格式。

**评估补丁。** 一旦模型提出补丁，我们通过将其应用到目标代码库并执行仓库的测试套件来评估其正确性。测试执行的输出使用日志解析函数进行解析，以提取每个单独测试用例的状态。然后将这些结果与预期的测试用例转换进行比较。



---

## 论文 24

# Automated Mapping of CVE Vulnerability Records to MITRE CWE Weaknesses

**作者**: Ashraf Haddad, Najwa Aaraj, Preslav Nakov, Septimiu Fabian Mare

**arXiv**: https://arxiv.org/abs/2304.11130v1

---

**语言识别：** 该文本为**英语**（English）。

---

**翻译（英译中）：**

CVE漏洞记录到MITRE CWE弱点的自动映射

Ashraf Haddad †
Najwa Aaraj †∗
Septimiu Fabian Mare ∗

MBZUAI- 阿布扎比人工智能大学，阿联酋阿布扎比
∗

Preslav Nakov †

TII - 阿联酋阿布扎比技术创新研究所

arXiv:2304.11130v1 [cs.CR] 2023年4月13日

**摘要**

近年来，网络安全威胁的扩散与多样性持续上升，导致相关报告和分析不断增加。为应对这一挑战，许多非营利组织在该领域应运而生，例如MITRE和OSWAP，它们积极追踪漏洞并以标准化格式发布防御建议。由于人工生成此类格式的数据极为耗时，因此已有部分实现自动化的提案。然而，采用监督机器学习解决这一问题的主要障碍是缺乏公开可用的专业数据集。在此，我们旨在弥合这一差距。

具体而言，我们专注于将CVE记录映射至MITRE CWE弱点，并向研究社区发布了用于此任务的4,012条人工标注记录数据集。考虑到人机交互框架，我们将该问题作为排序任务来处理，并计划在后续工作中结合强化学习以利用人类反馈。我们的实验结果表明，经过微调的深度学习模型（Sentence-BERT和rankT5）相较于BM25、BERT和RoBERTa取得了显著的性能提升，这表明该任务需要具备良好语义理解能力的架构。

**图1：** 我们的漏洞记录映射至结构化输出的框架，集成人类反馈循环。

网络威胁情报（CTI）的类型取决于受众群体以及分析的技术深度。部分报告面向非技术受众，旨在提供战略性和影响分析；而另一些则属于技术性报告，包含妥协指标（IoCs）以及受影响软件/硬件的具体版本等详细信息。无论技术深度如何，高效共享CTI报告需要广泛采用的通用标准化规范。MITRE前25种常见弱点枚举（CWE）是社区开发的已知弱点类型，描述了影响最大的漏洞。这些漏洞（如开源情报社区在国家漏洞数据库CVE上报告的漏洞）需要网络安全专家进行分析以推断其底层弱点。这是一项繁琐且耗时的任务，但却是人工智能工具辅助的绝佳候选。因此，我们此项工作的动机是将报告的CVE漏洞映射到对应的CWE弱点，这是生成CTI报告的关键步骤。

**引言**

网络安全是一个主要的技术关切，体现在不断变异的众多威胁之中。

这段文字是**英文**。以下是翻译成**中文**的学术翻译：

---

并在众多系统中被报道[21]，包括操作系统、物联网（IoT）、物理网络以及其他层级技术，触及经济和社会的各个方面。例如，2021年OWASP Top 10[26]报告显示，十大最严重安全漏洞中有三个在几年前并不那么严重或尚未出现。网络威胁情报（CTI）报告旨在识别和组织日志漏洞中的信息。非营利组织和私人机构都热衷于向最终用户制作此类报告，从而教育系统的设计者和开发者如何应对此类弱点。

https://www.mitre.org/

1

---

图2：我们的重点任务：将非结构化输入映射到MITRE CWE Top 25弱点。
在开源研究的理念下，我们的重点是利用公开可用的NVD CVE中存在的记录，并利用自然语言处理（NLP）完成标准化任务，从而惠及网络安全和人工智能社区。这将我们的方法与现有方法区分开来，团队的努力在于构建一个数据集以及维护该数据集所需的工具，考虑到不断变化的网络安全领域。此外，我们在该数据集上验证了最先进的排序模型。我们在使用基于Transformer的架构时取得了最佳结果，具体采用了两种配置：语义相似度匹配设置和序列到序列排序设置，以检索描述该问题的最合适的CWE类型。我们的贡献可总结如下：

我们将结果与更通用的预训练模型BERT[7]和RoBERTa[16]进行了比较。BM25[30]作为基线模型。此外，我们还使用了T5作为Seq2Seq生成[32]模型以及排序器。图2阐明了我们工作的总体目标，这与MITRE对CVE记录标准化的愿景一致，这对社区来说是一项极具价值的任务[3]。
本文的其余部分结构如下：第2节提供了相关工作的摘要。第3节简要总结了MITRE弱点类型及其使用方法。第4节解释了注释和维护发布的网络安全人工智能数据集的基本原理。第5节介绍了我们的方法论。第6节描述了我们的实验并讨论了结果。最后，第7节总结了工作并指出了未来可能的研究方向。

• 新问题 formulation：使用人工智能辅助人工注释者简化NVD数据集中日志漏洞的分析和标注，如图1所示。模型输出可以利用注释者反馈的强化学习[25]，这可以在未来的工作中探索。

---

相关工作

近年来，将NLP应用于CTI的研究课题引起了广泛关注。这与人工智能兴起相关的一般原因有关：尽管数据并不总是适合人工智能处理[...]

---

**译注：** 文中最后一句被截断，译文中"[...]"表示原文未完整部分。

**语言识别**：该段落为**英语**（English）。

---

**翻译（中文）：

_ready, and the advances in algorithms and their execution. NLP is necessary to extract the relevant information and the intrinsic relationships between entities. The type of research in this field is divided into two area based on the model design. Some rely on traditional Machine learning (ML) methods such as Support Vec-

• 一个新的数据集：上述公式的实现必须首先创建一个合适的数据集。我们发布了我们的数据集2。
• 实验结果。我们将此任务作为排序问题来处理，使用文本相似度SBERT [29]，针对句子（而非文档级别）进行了优化，并在该数据集上使用了最新发布的排序T5 [24]模型。

3A
call-to-action
framework

by

MITRE

using

ATT&CK

https://medium.com/mitre-engenuity/
cve-mitre-att-ck-to-understand-vulnerability-impact-c40165111bf7

2 https://github.com/ahadda5/annotate_cve

2

tor Machines (SVM) [10, 23]，或概率推理[1]，而另一些则使用深度学习模型，如LSTM（长短期记忆网络）和Transformer[8, 36]。在如此高维度的特征空间中进行表示学习，对于像网络安全这样动态变化的语料库，深度学习方法更为适用，尤其是在高性能计算（HPC）机器的支持下。

Zhou等人[37]使用带有条件随机场（CRF）层的LSTM进行序列标注，专注于IoC（指标妥协）识别。虽然达到了较高的准确率，但这只是当前任务的一部分，因为标准化需要更高层次的描述，而不仅仅是IoC，例如缺失授权或服务器端请求伪造。此外，CRF作为判别式标签序列模型需要进行特征工程，需要过度指定的手动设计特征，相比之下，例如简单地在新数据和标签上训练Transformer模型则更为简便。Zhu和Dumitras[38]认识到了特征工程的挑战，并提出了使用语义网络模型解析句子，旨在模仿人工特征设计中的人类推理过程。他们的模型通过计算语义相似性，为重要概念和关系类型分配权重。概念首先通过依存分析提取为主语、动词和对象的行为元组，然后根据动词和名词短语的最大句法得分进行加权。语义网络随后对候选特征池进行排序，以确定描述漏洞最相关的特征。Zhu和Dumitras[39]继续他们的工作，采用精心设计的管道模型进行IoC识别和分类。网络爬取的报告经过句法和语义解析后，通过对可能的IoC字符串进行命名实体识别（NER），最后通过二分类器进行处理。上述所有工作仅解决了诸如IoC等特定内容的NER，因此无法满足我们自动化映射的目标。

此前，信息检索（IR）使用BM25已应用于_

这段文字是**英文**。

以下是翻译成的中文学术版本：

---

我们从Symantec威胁报告[11]的大量语料库中提取了相关信息。词性标注（POS）以及结合TF.IDF加权的BM25算法被用于识别和表征网络威胁情报报告中发现的恶意行为。为了克服这些文档中呈现的海量信息，我们训练了一个SVM分类器来过滤掉文档中相关性较低的内容。随后，对经过清洗的内容进行词性标注分析，通过主语、动词和宾语三元组来识别潜在的威胁行为。最后，将结果与ATT&CK模式进行匹配评分，以找到最佳匹配项。Ayoade等人[2]则采用了一种不同的方法，他们将威胁分为战术、技术和杀伤链进行独立分类，并由于“标注训练数据有限”而应用了偏差校正。该偏差通过使用协变量偏移方法（如核均值匹配KMM）得以缓解。训练数据有限是我们认同的观察结果，也是我们标注和发布该数据集的主要动机之一。

Legoy等人[15]将问题定义为多标签文本分类任务，从有限的带注释开源报告中提取ATT&CK TTP（战术、技术和程序）。他们对多种传统分类器进行了全面比较，包括逻辑回归、朴素贝ayes、K近邻、AdaBoost决策树等。他们针对技术和战术分别进行了独立训练和评估。此外，由于一个技术可能属于多个战术，关联的战术得分可与现有技术得分相结合，以提升技术的集成置信度。对于发现技术之间的关系（类似于图2底部所示），该论文采用了统计方法，计算技术对之间的联合概率，如稀有关联规则或Steiner树关联规则。然而，他们并未在这些方法上取得良好效果，因为这些方法“可能更适合于具有已知条件概率的分层环境”。

在深度学习领域，有多篇论文表现突出。Gasmi等人使用LSTM进行命名实体识别（NER）和关系抽取（RE）[8]。第一项任务采用分层结构实现，首先是词嵌入层，随后是双向LSTM，最后是CRF层来执行NER标签（改编自[14]）。对于关系抽取，他们探索了结合最短依赖路径（SDP）[35]的LSTM以及序列和树结构LSTM[18]。该论文的明显不足在于使用了机器标注的数据，而这些数据从一开始就并不精确。对于NER标注数据，他们借用了Stucco项目[9]，该项目未采用CWE或ATT&CK TTP等标准。此外，他们还使用了一种自举算法[12]来生成用于关系抽取训练的关系标签。令人鼓舞的是，最近发布的TCENet[36]在人工标注的安全报告上实现了高精度。该模型能够发现并分类TTP描述，并进一步提取描述中的元素特征，定位至句子级别。

---

**备注**：文中包含多个学术引用编号（如[2]、[8]、[9]、[11]、[12]、[14]、[15]、[18]、[35]、[36]），这些编号在翻译中得到保留，以便于读者查阅原始文献。



---

## 论文 25

# GitTaskBench: A Benchmark for Code Agents Solving Real-World Tasks Through Code Repository Leveraging

**作者**: Ziyi Ni, Huacan Wang, Shuo Zhang, Shuo Lu, Ziyang He

**arXiv**: https://arxiv.org/abs/2508.18993v2

---

该文本为**英语**。以下为翻译：

---

GitTaskBench：一个用于评估代码智能体通过代码仓库解决实际任务能力的基准测试

摘要

除了从零开始编码之外，利用大规模代码仓库（如GitHub）来完成实际任务对现实世界的软件开发至关重要，但目前的基准测试很少在这样真实的工作流驱动场景下评估代码智能体。为弥补这一空白，我们推出了GitTaskBench，这是一个通过7种模态和7个领域的54个真实任务来系统评估该能力的基准测试。每个任务都配备了一个相关仓库以及一个自动化、人工筛选的评估框架，用于指定实际的成功标准。除了衡量执行情况和任务成功率之外，我们还提出了alpha-value指标来量化智能体性能的经济效益，该指标整合了任务成功率、token成本和开发者平均薪资。针对三个最先进的智能体框架与多个高级大语言模型的实验表明，利用代码仓库解决复杂任务仍然具有挑战性：即使表现最佳的系统OpenHands+Claude 3.7也仅能解决48.15%的任务（近期进展进一步推动了技术前沿，RepoMaster+Claude 3.5创下了62.96%的新纪录）。错误分析表明，超过一半的失败原因在于看似平凡却至关重要的步骤，如环境配置和依赖解析，这凸显了更稳健的工作流管理和更充足的_timeout_准备时间的必要性。通过发布GitTaskBench，我们旨在推动对仓库感知型代码推理、执行和部署的关注与进展——使智能体更接近于解决复杂的端到端实际任务。

基准测试与代码：https://github.com/QuantaAlpha/GitTaskBench
项目主页：https://gittaskbench.github.io/

---

*注：部分专业术语（如token、timeout）保留英文形式以确保准确性。*

这段文字是**英文**。以下是其中文学术翻译：

---

语法修复（Jimenez et al. 2023）——未能评估智能体在真实世界问题解决中的能力。

近期研究已开始致力于开发更加实用、全面的基准测试。部分工作仍聚焦于代码生成，要求智能体生成日益复杂的代码，甚至从零开始生成整个代码库（Yu et al. 2024; Ihle 2025; Chan et al. 2025; Miserendino et al. 2025; Starace et al. 2025）。然而，这一沉重负担对当前大多数智能体系统而言仍极其困难（Li et al. 2024; Starace et al. 2025）。此外，仅专注于代码生成忽视了真实世界开发者实践的更广泛范畴（Masood 2024），且对智能体能力的洞察日益减少（Gao et al. 2024）。另一研究路线重新审视评估范式（Ishibashi and Nishimura 2024），将代码生成与外部工具或API调用相结合（Li et al. 2023; Wang et al. 2024; Ye et al. 2024; Zhuo et al. 2024; Tang et al. 2025; Dong et al. 2025），从而减轻了生成负担，却仍回避了理解并重构完整代码库这一更具挑战性的任务。然而，真实世界的程序员通常利用开源库1来应对各种真实世界任务，而非从头“造轮子”。此前的代码智能体基准测试忽视了自主环境配置以及利用开源代码库解决复杂端到端任务的能力——这在实际软件工程中是一种更具用户导向的设定（Lyu et al. 2023; Tang et al. 2023; Wang et al. 2025a）。

为此，我们设计并开发了GitTaskBench（GitTaskBench 2025），系统评估智能体在现实场景中利用代码库自动解决真实世界端到端任务的能力，重点关注以下三个关键维度：

• **整体编码精通度**：导航海量文档、理解代码依赖关系，并动态生成、修改或调试代码。

• **任务导向执行**：高效理解用户意图，通过多轮推理和适当的工具使用完成任务。所有生成的代码均以任务为中心。

• **自主环境配置**：在沙盒环境中独立管理环境配置和依赖解析，无需预建支持。

GitTaskBench的构建遵循严格的四步流程：任务与代码库选择、完整性验证、执行框架设计和评估框架开发，每一步均由人工完成，部分步骤辅以LLM辅助。最终基准测试涵盖54个真实多模态任务，分布于7个领域和24个子领域，远超传统机器学习任务的技术狭窄范畴（Liu et al. 2018; Tang et al. 2023; Chan et al. 2025）。每个任务均配有人工设计的自动化评估脚本，用于评估执行完成度和任务通过率。

---

**译注**：原文中的"1"指代脚注内容"Current GitHub has 28 million repositories and 190 million public projects to be exploited."（当前GitHub拥有2800万个代码库和1.9亿个公开项目可供利用），已在译文中保留标注。

这段文字是**英文**。以下是学术性的中文翻译：

---

**实际应用成功标准**

除上述核心指标外，我们进一步引入了alpha指标，该指标综合考量成本与效益。既往研究较少对智能体应用的实际收益进行分析或量化，尤其是在多模态场景中（Yang et al. 2024; Maslej et al. 2025; Chen et al. 2025）。我们的alpha指标将任务完成质量、智能体token使用量以及市场人力成本整合至统一框架中，实现智能体与人工效率之间直接、可解读的比较。

我们在多个配备先进大语言模型的代码智能体上开展实验，结果表明以下发现：(1) 复杂的仓库中心任务仍具挑战性，最高成功率仅为48.15%（OpenHands，Claude3.7）。(2) 用智能体替代人工并非总是具有成本效益；评估成本效益对于实际应用至关重要。(3) 智能体在纯文本任务中表现优于多模态任务。(4) 实验工作流中更好的环境配置与依赖管理对于加速实际代码智能体部署至关重要。

我们的主要贡献总结如下：

1. 我们提出了GitTaskBench，这是首个开源基准测试，通过以类人方式利用开源仓库来测试智能体解决现实复杂任务的能力，包含来自18个GitHub项目的54项任务，涵盖7种模态。
2. 每项任务都包含手工编写的测试脚本及相应的实际成功标准，以实现严格且自动化的评估。
3. 我们提出了一种新颖的领域特定"alpha值"公式，用于定量评估智能体的经济效益，为智能体部署提供可操作的见解。
4. 我们对基于开源和闭源大语言模型的最先进智能体框架进行了基准测试，执行了超参数敏感性分析，并开展了详细的错误分析以凸显当前面临的挑战。

**相关工作**

现有代码智能体基准测试大致可分为两类：代码生成类和任务解决类。

**代码生成基准测试**

第一类基准测试评估复杂度与粒度递增的代码生成任务——从早期的单函数级任务（如HumanEval（Chen et al. 2021）和MBPP（ustin et al. 2021a）），到类级别补全（Du et al. 2023）、程序合成（Austin et al. 2021b）以及算法生成（Hendrycks et al. 2021; Li et al. 2022），进一步扩展至仓库级补全（如RepoBench（Liu, Xu, and McAuley 2023））。

---

（原文中的图表部分因缺乏完整上下文，部分内容保留英文）

The provided text is primarily written in **English** with some Chinese translations interspersed (likely from a bilingual interface or document).

Here is the translation into Chinese:

---

**处理为CSV格式。**

**处理EDA数据（采样率250）并存储为CSV文件。**

**思考中…**

**✓ 清晰的提示**
**✓ 输入格式**
**✓ 输出格式**

**AnimeGANv3**

**Scrapy**

**GitTaskBench**

**将给定视频转换为漫画风格。**

**以用户为中心、真实世界、多模态任务与自动化实践评估**

**成功标准**

**1**

**成功标准**

**CIEDE2000≥2.0,
NIQE≤7.0**

**PESQ≥1.5**

**执行完成：**
**任务通过：**
**实际收益：$-5.53**

**执行完成：**
**任务通过：**
**实际收益：$6.68**

**2**

**3**

**成功标准**

**成功标准**

**所有列的字段级准确率≥95%**

**SSIM≥0.7,
FID≤400**

**执行完成：**
**任务通过：**
**实际收益：$28.73**

**4**

**5**

**执行完成：**
**任务通过：**
**实际收益：$-6.80**

**完整输出。**

**6**

**执行完成：**
**任务通过：**
**实际收益：$56.95**

**成功标准**
**100%列准确率，**
**允许微小数值误差**

**仓储利用**

**格式错误。**

**入口文件**

**… 成功标准**

**100%列准确率**

**自动化环境设置**

**执行完成：**
**任务通过：**
**实际收益：$86.5**

**成功标准**

**无输出。**

**输出**

**心率MAE ≤ 1.0 BPM；**
**峰值MPTD ≤ 0.1s，**
**匹配率 ≥ 0.8，**
**计数相对误差 ≤ 0.1**

**执行完成：**
**任务通过：**
**实际收益：$-2.97**

**…**

**Config.yaml**
**README.md**
**Requirements.txt**

**… 工具**

**生成代码/命令**
**修改给定仓储的代码**
**调试代码，修复错误**

**图2：GitTaskBench概览。展示了来自不同模态的7个真实任务及其评估结果。**

**GitTaskBench**

**CrossCodeEval (Ding et al. 2023)。近期，**
**更具挑战性的基准测试如SWE-Bench (Jimenez et al.**
**2023) 已将目标瞄准解决GitHub问题，但已接近饱和（Claude 4-sonnet：80.2%）。SWELancer (Miserendino et al. 2025) 扩展到有报酬的真实**
**软件工程工作，但其90%的个人贡献者任务仍局限于预配置环境中的**
**小范围bug修复。这些基准测试存在两个主要局限：(1) 任务相对孤立，粒度较小，(2) 评估通常在简化或合成环境中进行，而非动态、真实的**
**条件下。我们的GitTaskBench基准测试通过真实、仓储感知的任务和反映真实用户场景的实践编码环境来解决这些问题。**

**GitTaskBench在真实、仓储中心化的任务上严格评估代码智能体，这些任务与常见用户查询紧密对齐（见图2）。智能体必须自主分析和复用现有仓储来完成反映真实用户工作流程的任务，无需人工干预即可处理任何错误。该基准测试主要由五位计算机科学博士手工制作并验证，以确保质量。每个任务都配有一个代表性的大型GitHub仓储，附有明确输入输出要求的特定自然语言指令，以及定制的任务特定评估指标，同时反映正确性和实用性，实现有意义的自动化评估。**

**语言识别：该段落为英语（English）。**

---

以下为中文翻译：

## 智能体性能评估

下文阐述了我们构建该基准测试的方法。

### 编程任务基准测试

#### 任务与仓库选择

第二类基准测试为面向任务的评估，主要考察智能体运用工具及外部API调用的一般编程能力。相关示例包括：涉及代码库的任务（如Odex（Wang等，2022））、数据科学专用评估（如PandasEval（Jain等，2022）、NumpyEval（Zhang等，2023）、DS-1000（Lai等，2023））、基于API的任务（如CodeAct（Wang等，2024）、ToC（Ni等，2024）），以及封闭环境下的机器学习（ML）挑战（Liu等，2018；Tang等，2023；Chan等，2025；Wang等，2025b）。然而，这些任务仍以技术导向为主，缺乏一项在实践中广泛应用的关键能力：利用GitHub仓库（"轮子"）来解决现实生活中的日常问题。

我们首先通过广泛的文献综述、深入的LLM驱动研究以及与领域专家的咨询来确定目标领域，并将这些见解与日常实践经验相结合。针对每个领域，我们选取了反映用户频繁需求的子领域，涵盖广泛的模态。我们优先选择具有挑战性的任务，通常需要整合或重用现有工具或代码库，以确保基准测试对代码智能体既有意义又具有挑战性。这些任务的人类完成时间最长可达三小时，平均每个任务1.34小时（见附录A）。

针对每个领域，我们运行了有针对性的深度研究（提示词见附录C）以定位合适的GitHub仓库。候选仓库须满足以下条件：（1）基于Python开发；（2）拥有≥50颗星标，且过去五年内有活跃更新（包括issue更新）；（3）提供可直接使用的权重文件及简易的安装配置。随后，我们检查了关键统计数据，如星标数、分支数、许可证、提交历史，并手动验证了其功能。由此形成的集合构成了潜在仓库池。

任务与仓库的选择是一个迭代且紧密耦合的过程。

---

| 基准测试 | 任务数 | 任务类型 | 多模态 | 仓库使用 | 仓库级代码生成 | 自动环境配置 |
|:------:|:-----:|:------:|:-----:|:-------:|:-------------:|:-----------:|
| RepoBench（刘、徐、麦考利，2023） | 7778 | 代码补全 | - | ✓ | ✓ | ✓ |
| Swe-Bench-Verified（希门尼斯等，2023） | 500 | 程序修复 | - | ✓ | ✓ | ✓ |
| LiveCode（贾因等，2024） | 584 | 编程竞赛 | - | ✓ | ✓ | ✓ |
| MLAgentBench（黄等，2023） | 13 | ML任务 | - | - | ✓ | ✓ |
| MLE-Bench（陈等，2025） | 72 | Kaggle（ML）任务 | - | - | ✓ | ✓ |
| PaperBench（斯塔拉斯等，2025） | 20 | 论文代码复现任务 | - | - | ✓ | ✓ |
| **GitTaskBench（本文）** | **54** | **用户中心、日常任务** | ✓ | ✓ | ✓ | ✓ |

**表1：GitTaskBench（本文）与现有同类复杂度和综合性基准测试的比较。**

---

| 类别 | 实例 | 仓库 | 指标 | （均值）数值 |
|:---:|:---:|:---:|:---:|:----------:|
| 领域数 | 7 | - | 域数量 | 7 |
| 子领域数 | 24 | - | 子域数量 | 24 |
| 任务数 | 54 | - | 任务数量 | 54 |
| 模态数 | 7 | - | 模态数量 | 7 |
| 规模 | 18 | - | 规模 | 18 |
| 文件数 | 204 (7-1157) | 仓库 | 文件数量 | 204 (7-1157) |
| 类数 | 263.61 (2-1130) | - | 类数量 | 263.61 (2-1130) |
| 函数数 | 1274.78 (25-4915) | - | 函数数量 | 1274.78 (25-4915) |
| 依赖数 | 1242.72 (33-6979) | - | 依赖数量 | 1242.72 (33-6979) |
| 调用数 | 8651.28 (180-40552) | - | 调用数量 | 8651.28 (180-40552) |
| 代码行数 | 52.63 (0.575-351.42)千 | - | 代码行数 | 52.63 (0.575-351.42)千 |
| Token数 | 448.95 (4.87-2888.35)千 | - | Token数 | 448.95 (4.87-2888.35)千 |

**表2：GitTaskBench统计摘要。**

---

*注：原文在结尾处被截断（"tightly coup"），因此翻译内容亦止于此。*



---

## 论文 26

# Observation of the rare $B^0_s\toμ^+μ^-$ decay from the combined analysis of CMS and LHCb data

**作者**: The CMS, LHCb Collaborations,  :, V. Khachatryan, A. M. Sirunyan

**arXiv**: https://arxiv.org/abs/1411.4413v2

---

The paragraph is written in English. Here is the Chinese translation:

---

欧洲核子研究组织（CERN）

arXiv:1411.4413v2 [hep-ex] 2015年8月17日

CMS-BPH-13-007
LHCb-PAPER-2014-049
CERN-PH-EP-2014-220
2015年5月13日

CMS和LHCb数据联合分析观测到的稀有Bs0 → µ+µ−衰变

CMS和LHCb合作组†

†

参与者名单及其所属机构列于本信件末尾。

---

粒子物理学的标准模型通过强相互作用、电磁相互作用和弱相互作用来描述基本粒子及其相互作用。它为可实验测量的量提供了精确的预测。奇异B介子（Bs0）和B0介子衰变为两个带相反电荷的 muon（µ+和µ−）的概率，即分支比，由于其对扩展标准模型的理论具有敏感性而特别有趣。标准模型预测Bs0 → µ+µ−和B0 → μ+μ−衰变极为稀有，每产生十亿个Bs0介子约有四个发生上述衰变，而每产生一百亿个B0介子只有一个发生上述衰变1。观测到的分支比与标准模型预测之间的差异将为标准模型应如何扩展提供方向。在欧洲核子研究组织（CERN）的大型强子对撞机（LHC）2开始运行之前，这两种衰变模式都未被发现。分支比的上限比标准模型预测高出一个数量级。CMS（紧凑μ子螺旋管）合作组和LHCb（大型强子对撞机 beauty）合作组对他们在2011年质子-质子对撞数据（质心能量为7太电子伏特）和2012年（质心能量为8太电子伏特）进行了联合分析。在此，我们报告首次观测到Bs0 → µ+μ−衰变，其统计显著性超过六个标准差，并获得了迄今为止对其分支比的最佳测量。此外，我们获得了B0 → µ+μ−衰变的证据，统计显著性为三个标准差。这两项测量在统计上均与标准模型预测相符，并对标准模型之外的理论施加了严格的约束。LHC实验将于2015年恢复数据采集，记录质心能量为13太电子伏特的质子-质子对撞，这将使Bs0和B0介子的产生率大约翻倍，并进一步提高这些标准模型关键检验的精度。

自20世纪70年以来，实验粒子物理学家一直在以越来越高的精度检验粒子物理学标准模型（SM）的预测。理论发展随着实验精度的提高而同步推进，不断提升SM预测的准确性。在过去的几十年中，SM通过了实验的关键检验，但它并未涉及关于自然界某些深刻问题的答案。

**语言识别：该段落为英语（English）。**

**翻译（中文）：**

例如，暗物质的存在虽然已由宇宙学数据3得到证实，但标准模型无法容纳它。标准模型同样无法解释物质与反物质不对称性的起源，而在 Big Bang 之后，这种不对称性导致了目前宇宙中仅存的微量物质的存留3,4。许多理论已被提出试图修改标准模型以解决这些悬而未决的问题。

Bs⁰ 和 B⁰ 介子是通过弱相互作用衰变的不稳定粒子。测量这些介子衰变为双μ子（μ⁺μ⁻）末态的分支比具有特别重要的意义。

在基本粒子层面，弱力由"带电流"和"中性流"组成，分别由 W± 和 Z⁰ 玻色子介导。带电流的一个例子是 π⁺ 介子的衰变，它由一个电荷为质子电荷 +2/3 的上（u）夸克和一个电荷为 +1/3 的下（d）反夸克组成。这一过程的图示表示（即费曼图）如图 1a 所示。u 和 d 夸克是"第一代"或质量最低的夸克。本文中任何指定的衰变模式均隐含其电荷共轭模式。

B⁺ 介子与 π⁺ 类似，但轻的 d 反夸克被重的"第三代"（质量最高的夸克）美（b）反夸克所取代，其电荷为 +1/3，质量约为 5 GeV/c²（约为质子质量的五倍）。图 1b 所示的 B⁺ → μ⁺ν 衰变是允许的，但由于角动量方面的考虑（螺旋度抑制）以及涉及不同代夸克之间的转变（CKM 抑制），即第三代与第一代夸克之间的转变，该衰变被强烈抑制。所有 b 强子，包括 B⁺、Bs⁰ 和 B⁰ 介子，主要通过 b 反夸克向"第二代"（中间质量夸克）粲（c）反夸克的转变而衰变，这种转变的 CKM 抑制较弱，衰变末态中含有粲强子。许多允许的衰变模式通常涉及粲强子和其他粒子，其角动量构型不受螺旋度抑制。

中性 Bs⁰ 介子与 B⁺ 类似，但 u 夸克被第二代奇异（s）夸克取代，其电荷为 -1/3。Bs⁰ 介子衰变到两个μ子（如图 1c 所示）在基本粒子层面是被禁止的，因为 Z⁰ 不能直接耦合到不同味夸克，即不存在直接的"味变中性流"。然而，遵守这一规则的同时仍可能发生此类衰变，即通过图 1d 和 1e 所示的"高阶"转变。这些过程被强烈抑制，因为每增加一个相互作用顶点都会显著降低其发生概率。它们还受到螺旋度和 CKM 抑制。因此，Bs⁰ → μ⁺μ⁻ 衰变的分支比预期非常小。

**语言识别：** 该段落为**英语**（English）。

---

**中文翻译：**

与占主导的b夸克到c夸克的转换相比，这一过程非常渺小。B⁰介子（其中d夸克取代了s夸克）的衰变甚至受到更强的CKM抑制，因为它需要跨越两个夸克代，而不是仅一个。

这两个衰变的分支比B已经包含了高阶电磁和强相互作用效应，并使用格点量子色动力学计算了Bₛ⁰和B⁰介子的衰变常数5–7，在标准模型中可以得到可靠计算1。它们的值为B(Bₛ⁰ → μ⁺μ⁻)SM = (3.66 ± 0.23) × 10⁻⁹和B(B⁰ → μ⁺μ⁻)SM = (1.06 ± 0.09) × 10⁻¹⁰。

许多寻求超越标准模型（BSM）的理论8,9包含新的现象和粒子，如图1f和g所示，这些可以显著改变标准模型的分支比。特别是包含额外希格斯玻色子10,11的理论预测对分支比可能有增强。这两个分支比测量中任何一个与标准模型预测的显著偏差都将为如何扩展标准模型提供洞察。或者，与标准模型相符的测量结果可以对BSM理论提供强约束。

两个衰变模式的分支比之比为BSM理论12提供了强大的区分能力。它在标准模型中被预测为1,13–15：R ≡ B(B⁰ → μ⁺μ⁻)SM / B(Bₛ⁰ → μ⁺μ⁻)SM = 0.0295⁺⁰·⁰⁰²⁸₋₀.₀₀₂⁵。值得注意的是，具有最小味破坏特性的BSM理论16预测该比值与标准模型相同。

LHCb合作组在2012年首次给出了Bₛ⁰ → μ⁺μ⁻衰变的证据17。随后，CMS和LHCb都发表了基于2011年7 TeV和2012年8 TeV质子-质子碰撞收集的全部数据的结果。这些测量具有相当的精度并且吻合良好18,19，尽管个别结果尚不足以构成对Bₛ⁰衰变到两个μ子的首次确定性观测。

在这篇快报中，我们同时组合并分析了这两组数据。

**语言识别 / Language Identification:**

该段落为**英语**（English）。

---

**翻译 / Translation:**

以充分利用数据的统计能力，并考虑它们之间的主要相关性。数据对应于CMS和LHCb实验分别25 fb⁻¹和3 fb⁻¹的总积分亮度，相当于两个实验共同产生约10¹²个B⁰_s和B⁰介子。假设标准模型（SM）给出的分支比，并考虑探测效率，预计两个实验共同可观测到的衰变数目约为100个B⁰_s → μ⁺μ⁻和10个B⁰ → μ⁺μ⁻。

CMS²⁰和LHCb²¹探测器旨在高精度测量标准模型现象并寻找可能的偏差。两个实验组采用不同且互补的策略。除了进行广泛的精确标准模型检验和研究新发现的希格斯玻色子²²·²³之外，CMS还设计用于搜索和研究质量约为100 GeV/c²至几 TeV/c²的新粒子。由于许多这些新粒子能够衰变成b夸克，且许多标准模型测量也涉及b夸克，因此b-强子衰变的探测成为CMS设计的关键要素。LHCb合作组优化了其探测器，以研究含b夸克的粒子的物质-反物质不对称性和稀有衰变，旨在探测与精确标准模型预测的偏差，从而指示超出标准模型（BSM）效应。这些不同的方法反映在探测器的设计中，导致相对于LHC束流在互补的角度区域进行仪器化，以不同的质子-质子碰撞率运行，并以不同的效率选择b夸克事件（实验细节见方法）。一般来说，CMS以更高的瞬时亮度运行，但对低质量粒子的重建效率较低，从而对B⁰或B⁰_s（以下记为B(s)⁰）衰变到两个μ子的灵敏度与LHCb相当。

μ子没有强核相互作用，且质量足够大以致于无法通过电磁辐射显著发射能量。这赋予了它们穿透 dense materials（如钢）并在其中嵌入的探测器中记录信号的独特能力。两个实验都利用这一特性来识别μ子。

实验遵循类似的数据分析策略。通过合并重建的相反电荷粒子（被识别为μ子）的轨迹（径迹），找到与B(s)⁰ → μ⁺μ⁻（候选衰变）相兼容的衰变。区分真正的B(s)⁰ → μ⁺μ⁻衰变与两个μ子的随机组合（组合本底），后者最常见于两个不同b-强子的半轻子衰变，利用双μ子不变质量m_μ⁺μ⁻以及B(s)⁰介子衰变的已知特性来实现。例如，由于其寿命约为1.5 ps，且在LHC中以介于几 GeV/c至约100 GeV/c之间的动量产生，

**语言识别：** 该段落为**英语**（English）。

---

**中文翻译：**

B(s)介子在衰变前可以传播从零到几厘米的距离。因此，源自B(s)介子的μ⁺μ⁻“衰变顶点”相对于“产生顶点”——即两个质子碰撞的点——必须存在位移。此外，B(s)候选介子动量矢量的反向必须指向产生顶点。这些标准（以及其它一些能够区分已知信号事件和本底事件的标准）被组合成提升决策树（BDT）。BDT是由多个决策树组成的集成方法，每个决策树对各个变量施加不同的选择要求，以实现对“类信号”和“类本底”事件的最大区分能力。两个实验都评估了许多变量及其区分能力，并各自选择了约十个最佳变量用于其相应的BDT。这些变量包括与μ子重建径迹质量相关的变量；运动学变量，如候选介子的横向动量（相对于束流轴）；与衰变顶点拓扑和拟合质量相关的变量，如候选介子衰变长度；以及孤立性变量，用于测量两个μ子或其位移顶点附近其他粒子的活动程度。BDT必须在已知本底和信号事件的集合上进行“训练”，以生成对变量的选择要求和每个决策树的权重。对于CMS，训练中使用的本底事件来自数据中信号区域上方和下方的双μ子质量区间，而信号则使用模拟事件。数据被分成互不相交的子样本，在一个子样本上训练的BDT应用于另一个子样本，以避免任何偏差。LHCb在其BDT的训练中同时使用模拟事件作为本底和信号。训练后，相关的BDT被应用于数据中的每个事件，返回该事件的单一数值，高数值更趋向于类信号。为避免可能的偏差，两个实验都将包含Bs⁰和B⁰信号的小质量区间设为盲态，直至所有选择标准都已确定。

除了组合本底外，某些特定的b-强子衰变也可能模拟B(s)介子的双μ子衰变，例如B⁰ → π⁻μ⁺ν（其中中微子无法探测，带电π介子被误识别为μ子）或B⁰ → π⁰μ⁺μ⁻（其中衰变中的中性π介子未被重建）。对于这些过程（半轻子本底），重建的双μ子候选介子的不变质量通常小于Bs⁰或B⁰介子的质量，因为中微子或其它粒子未被探测到。还有一类本底来源于强子两体Bs⁰衰变（峰值本底），如B⁰ → K⁺K⁻，当衰变产生的两个强子都被误识别为μ子时。这些误识别的衰变可能在质量谱中产生峰值。



---

## 论文 27

# Performance Evaluation of Virtualized Hadoop Clusters

**作者**: Todor Ivanov, Roberto V. Zicari, Sead Izberovic, Karsten Tolle

**arXiv**: https://arxiv.org/abs/1411.3811v1

---

**语言识别：该段落为英语（English）。**

---

**翻译（中文简体）：**

虚拟化Hadoop集群性能评估

技术报告编号：2014-1
2014年11月14日
Todor Ivanov, Roberto V. Zicari, Sead Izberovic,
Karsten Tolle

法兰克福大数据实验室
数据库与信息系统系
信息与数学研究所
法兰克福大学
Robert-Mayer-Str. 10,
60325 Bockenheim,
德国法兰克福
www.bigdata.uni-frankfurt.de

---

版权所有 © 2014，归作者所有。
允许以个人或课堂教学为目的制作本作品的数字或纸质副本，无需付费，但前提是副本不得用于盈利或商业目的，且须在第一页注明本通知和完整引用。未经事先许可，不得以其他方式复制、出版、张贴于服务器或分发至列表，需事先获得特别授权。

---

**目录**
1. 引言........................................................................................................................... 1
2. 背景........................................................................................................................... 2
3. 实验环境................................................................................................................... 4
3.1. 平台.......................................................................................................................... 4
3.2. 设置与配置............................................................................................................ 5
4. 基准测试方法论........................................................................................................... 6
5. 实验结果................................................................................................................... 7
5.1. WordCount................................................................................................................. 8
5.1.1. 准备阶段............................................................................................................. 8
5.1.2. 结果与评估......................................................................................................... 8
5.1.2.1. 不同集群配置对比......................................................................................... 8
5.1.2.2. 不同数据规模处理....................................................................................... 10
5.2. 增强型DFSIO.......................................................................................................... 12
5.2.1. 准备阶段........................................................................................................... 13
5.2.2. 结果与评估......................................................................................................... 13
5.2.2.1. 不同集群配置对比....................................................................................... 14
5.2.2.2. 不同数据规模处理....................................................................................... （未完）

**语言识别：**

该文本为**英语**（English）。

---

**中文翻译：**

1. 引言

Apache Hadoop [1] 已成为大数据应用的主流平台。意识到这一潜力，云服务提供商已迅速将其纳入服务范畴（IaaS、PaaS和SaaS）[2]。例如，Amazon凭借其Elastic MapReduce（EMR）[3]网络服务，已成为提供Hadoop即服务的先驱之一此类云服务的主要优势在于能够快速自动部署并以成本效益方式管理Hadoop集群，这一目标通过按需付费模式得以实现。这些功能的实现均依赖于虚拟化技术，而虚拟化技术正是大多数公共和私有云基础设施的基本构建模块[4]。然而，虚拟化带来的好处是以额外的性能开销为代价的。在虚拟化Hadoop集群的场景中，面临的挑战不仅在于大规模数据集的存储，还包括处理过程中的数据传输问题。此前的研究对虚拟化Hadoop集群与物理集群的性能进行了比较，结果显示虚拟化开销因应用类型不同而在2-10%之间波动[5]、[6]、[7]。然而，在某些情况下，虚拟化Hadoop的性能反而优于物理集群，这是因为虚拟化技术实现了更好的资源利用率。

尽管Hadoop存在虚拟机管理程序开销，但在云环境中部署Hadoop仍具有诸多优势[5]、[6]、[7]，包括增强的可扩展性、故障恢复、高效的资源利用、多租户安全性等。此外，虚拟化层能够将Hadoop的计算层和存储层分离到不同的虚拟机（VM）中。图1展示了在虚拟机管理程序上部署Hadoop集群的多种组合方式。方案（1）将工作节点托管在虚拟机中，在单一主机上同时运行TaskTracker和NameNode服务。方案（2）利用虚拟化层提供的多租户能力，在同一物理服务器上托管两个Hadoop工作节点。方案（3）展示了计算层（MapReduce服务）和存储层（HDFS服务）在不同虚拟机中的功能分离示例。在这种情况下，虚拟集群由两个计算节点和一个存储节点组成，均托管在同一物理服务器上。最终，方案（

这段文字是**英文**的。以下是中文翻译：

---

4) 给出了一个在不同主机上运行的两个独立集群的示例。第一个集群由一个数据节点和一个计算节点组成。第二个集群由一个计算节点组成，该节点访问第一个集群的数据节点。这些部署选项目前由 Serengeti [8]（VMware 发起的一个项目）和 Sahara [9]（OpenStack [10] 云平台的一部分）支持。

物理主机
虚拟机
计算
(MapReduce)
存储
(HDFS)

选项 (1)
虚拟机中的 Hadoop 节点

物理主机

物理主机

虚拟机
计算
(MapReduce)
存储
(HDFS)

虚拟机
计算 (MapReduce)

物理主机
虚拟机
计算 (MapReduce)
物理主机

虚拟机
计算 (MapReduce)

虚拟机
计算 (MapReduce)
存储
(HDFS)

虚拟机

虚拟机

选项 (2)
一台主机上的多个 Hadoop 节点（虚拟机）

虚拟机
计算 (MapReduce)

存储 (HDFS)

存储 (HDFS)

选项 (3)
每个虚拟机独立的存储和计算服务

选项 (4)
每个租户独立的 Hadoop 集群

图 1：虚拟化 Hadoop 集群部署选项

第 1 页

---

在本报告中，我们研究了在管理单个物理主机的虚拟机管理程序上部署的、存储与计算分离的 Hadoop 集群（第 3 选项）的性能。我们通过运行 CPU 密集型和 I/O 密集型工作负载，对不同的 Hadoop 集群配置进行了分析和评估。

本报告的结构如下：第 2 节简要介绍了本研究涉及的技术。第 3 节概述了实验平台、测试设置和配置。第 4 节定义了基准测试方法。第 5 节介绍了所执行的实验及结果评估。第 6 节总结了经验教训。

2. 背景
大数据不仅在 IT 领域，已成为医疗、制造、交通、零售和公共管理部门 [11]、[12] 等众多其他行业的新兴术语，并迅速变得相关。目前仍没有单一的定义能够充分描述大数据的各个方面 [13]，但"V"特征（Volume（容量）、Variety（多样性）、Velocity（速度）、Veracity（真实性）等）是目前最广泛使用的特征。正是这些新的大数据特征对传统数据管理和分析系统的能力提出了挑战 [13]、[14]。这些挑战也激励研究人员和行业开发 Hadoop 和 NoSQL 数据库 [15] 等新型系统。

Apache Hadoop [1] 是一个用于使用 map 和 reduce 编程模型在计算机集群上分布式存储和处理大规模数据集的软件框架。该架构允许从单台服务器扩展到数千台机器。同时，Hadoop 通过在应用层检测和处理故障来提供高可用性。数据复制确保了数据可靠性和快速访问。Hadoop 的核心组件是 Hadoop 分布式文件系统（HDFS）[16]、[17] 和 MapReduce 框架 [18]。

HDFS 采用主/从架构...

该文本为**英文**。以下是中文（简体）学术翻译：

---

该架构采用NameNode作为主节点，多个DataNode作为从节点。NameNode负责存储和管理所有文件结构、元数据、事务操作以及文件系统日志。DataNode以文件形式存储实际数据。每个文件被分割为预配置大小的块。每个块会被复制并存储在多个DataNode上。块副本的数量取决于副本因子（Replication Factor）。

MapReduce是一个软件框架，提供通用编程接口，用于编写在集群节点上通过分布式文件系统并行处理海量数据的应用程序。MapReduce的工作单元称为作业（job），由输入数据和MapReduce程序组成。每个作业被划分为map任务和reduce任务。map任务接收输入数据的一个分片（split），并根据MapReduce程序中用户定义的map函数对其进行处理。reduce任务收集所有map任务的输出，并根据用户定义的reduce函数对它们进行合并。reducer的数量由用户指定，与输入分片或map任务的数量无关。并行应用程序执行通过在每个节点上运行map任务处理本地数据，然后将结果发送到产生最终输出的reduce任务来实现。

Hadoop通过使用两种类型的进程来实现MapReduce模型——JobTracker和TaskTracker。JobTracker负责协调Hadoop中的所有作业，并将任务调度到每个集群节点上的TaskTracker。TaskTracker负责运行由JobTracker分配的任务。

许多其他应用程序在Hadoop核心组件（也称为Hadoop生态系统）之上开发，以使其更易于使用并适用于各种行业。例如此类应用程序包括Hive [19]、Pig [20]、Mahout [21]、HBase [22]、Sqoop [23]等。

VMware vSphere [24]、[25]是用于云基础设施的领先服务器虚拟化技术，由计算、网络、存储、可用性、自动化、管理和安全功能等多个软件组件组成。它虚拟化并聚合底层跨多个系统的物理硬件资源，并为数据中心提供虚拟资源池。

Serengeti [8]是由VMware发起并现已属于vSphere大数据扩展[26]的开源项目。该项目的目标是在虚拟化环境中实现Hadoop的快速配置和自动化部署。该项目的主要贡献是Hadoop虚拟扩展（HVE）[27]，它使Hadoop能够感知自身处于虚拟化状态。这个集成hypervisor功能的新层使用钩子（hooks）触及所有Hadoop子组件（Common、HDFS和MapReduce），称为节点组层（Node Group layer）。此外，还引入了新的数据局部性相关策略：副本放置/移除策略扩展、副本...

---

该文本是**英文**。下面是翻译成的中文学术版本：

---

choosing policy extension和balancer policy extension。根据VMware报告[28]，虚拟化Hadoop的优势包括：(i)实现快速配置；(ii)由虚拟机管理程序提供额外的高可用性和容错能力；(iii)通过更高的服务器整合提高数据中心效率；(iv)通过保障虚拟机资源实现高效的资源利用；(v)多租户允许在同一租户上混合工作负载，同时仍能保持服务质量(QoS)和SLA；(vi)提供虚拟机之间的安全性和隔离性；(vii)通过将作业调度到硬件使用率较低的时段来实现时间共享；(viii)便于环境的维护和迁移；(ix)能够在云环境中运行Hadoop即服务。Serengeti首次引入的另一个主要功能是能够将Hadoop的计算层和存储层分离到不同的虚拟机上。

第3页

---

3. 实验环境
3.1 平台

我们用于执行测试的实验平台抽象视图如图2所示。该平台由以下四个逻辑层组成。
应用层（HiBench基准测试）
平台层（Hadoop集群）
管理层（虚拟化）
硬件层
CPU

内存

存储

图2：实验平台层次结构

**硬件**
它由一台标准的Dell PowerEdge T420服务器组成，配备两颗Intel Xeon E5-2420（1.9 GHz）CPU，每颗CPU有六个核心，32 GB内存，以及四块1 TB、西部数据（SATA，3.5英寸，7200 RPM，64MB缓存）硬盘。

**管理层（虚拟化）**
我们在物理服务器上安装了VMware vSphere 5.1 [24]平台，包括ESXi和vCenter Server，用于自动化的虚拟机管理。

**平台层（Hadoop集群）**
集成在vSphere大数据扩展（BDE）（版本1.0）[26]中的Serengeti项目安装在一个独立的虚拟机中，用于自动部署和管理Hadoop集群。硬盘作为独立的数据存储部署，并由BDE用作共享存储资源。标准集群和数据计算集群配置的部署均使用BDE/Serengeti Server的默认选项完成，如[29]中所述。在所有实验中，我们使用了Serengeti Server虚拟机模板（托管CentOS）中包含的Apache Hadoop发行版（版本1.2.1），采用默认参数：200MB Java堆大小、64MB HDFS块大小和复制因子为3。

**应用层（HiBench基准测试）**
HiBench [30]基准测试套件由Intel开发，用于对Hadoop系统进行压力测试。它包含10个不同的工作负载，分为4类：
1. 微基准测试（Sort、WordCount、TeraSort、增强型DFSIO）
2. 网页搜索（Nutch索引、PageRank）
3. 机器学习（贝叶斯分类、K-means聚类）
4. 分析查询（Hive Join、Hive聚合）

对于我们的实验，我们从HiBench微基准测试中选择了两个具有代表性的MapReduce应用程序，即WordCount（CPU密集型）和Test...



---

## 论文 28

# SyncMind: Measuring Agent Out-of-Sync Recovery in Collaborative Software Engineering

**作者**: Xuehang Guo, Xingyao Wang, Yangyi Chen, Sha Li, Chi Han

**arXiv**: https://arxiv.org/abs/2502.06994v2

---

这段文字是**英语**（English）。

以下是中文翻译：

---

SyncMind：协作软件工程中智能体失同步恢复的测量

薛航郭 1 王星耀 1,2 陈阳毅 1 李莎 1 韩驰 1 李曼玲 3 姬恒 1

伊利诺伊大学香槟分校 2 全员智能 AI 西北大学
通讯作者：薛航郭 <xuehangg@illinois.edu>，王星耀 <xingyao@all-hands.dev, xingyao6@illinois.edu>，陈阳毅 <yangyic3@illinois.edu>，李莎 <shal2@illinois.edu>，韩驰 <chihan3@illinois.edu>

**摘要**

软件工程（SE）日益呈现协作性，开发者们共同在复杂的共享代码库上工作。共享环境中的有效协作要求参与者——无论是人类还是人工智能智能体——能够随着环境的演变保持同步。当协作者的理解与当前状态产生偏差时——我们称之为失同步挑战——协作者的行动可能会失败，从而导致集成问题。在本工作中，我们引入了 SyncMind，这是一个系统性地定义大型语言模型（LLM）智能体在协作软件工程（CSE）中面临的失同步问题的框架。基于 SyncMind，我们创建了 SyncBench，这是一个基准测试，包含来自 21 个热门 GitHub 仓库的 24,332 个真实协作软件工程场景实例，并配有可执行的验证测试。对 SyncBench 的实验揭示了现有 LLM 智能体能力与局限性的关键洞察。除了智能体之间存在显著的性能差距（从 Llama-3.1 智能体的 ≤3.33% 到 Claude-3.5-Sonnet 的 ≥28.18%），其持续偏低的协作意愿（≤4.86%）表明现有 LLM 在协作软件工程中存在根本性局限性。然而，当协作发生时，它与失同步恢复成功呈正相关。智能体在资源感知的失同步恢复方面表现出极小的性能差异，进一步揭示了它们在资源感知和适应性方面的显著不足，为资源高效协作系统的未来发展提供了启示。我们的代码和数据已在项目网站上公开可用：https://xhguo7.github.io/SyncMind/。

arXiv:2502.06994v2 [cs.SE] 2025年6月9日

**1. 引言**

图1. 失同步挑战。在 Ti 时刻，智能体和人类分别执行各自的任务。在智能体完成任务从 Ti 到 Tk 期间，人类在 Tj 时刻更新了智能体因其忙于自身任务而不知情的 <repo>。这导致智能体在 Tk 时刻因 Sk ≠ Bk 而失同步。

协作系统——无论涉及人类、人工智能智能体或两者兼有——通过结合互补优势来提升效率和能力。近期的进展已展示了人工智能智能体在协作任务中的令人印象深刻的能力（Wang et al., 2024c），从有效的日常问题解决助手（如 ChatGPT（OpenAI，2022）、Claude（Anthropic，2023）），到能够与人类积极协作进行软件开发的编码智能体（如 Devin（Cognition AI，2024）、OpenHands（Wang et al., 2024a））。

The provided text is written in **English**.

Here is the translation into **Chinese**:

---

du>，Manling Li <manling.li@northwestern.edu>，Heng Ji <hengji@illinois.edu>。
3

这些协作编码智能体通常在静态环境中进行设计和评估，在任务执行过程中工作空间保持不变（Jimenez et al., 2023；Yang et al., 2024a）。然而，现实世界的协作软件工程（CSE）本质上在动态环境中运作，有效的团队协作依赖于团队成员对工作空间状态保持同步感知——这是该领域的核心挑战（Yang et al., 2024b）。虽然版本控制系统（Torvalds, 2005）能够检测到表面层面的代码冲突，但无法识别需要人工解决的语义不一致问题。这包括智能体必须解决依赖更新、修改现有函数以与新导入的模块对齐等情况（图2）。

在这项工作中，我们引入了SyncMind（第2节），这是一个系统性地定义CSE中智能体不同步问题的框架（图3），其中多个协作者频繁修改和更新共享代码库。这发生在协作者的信念状态

---

**备注**：该文本为学术论文摘要部分，涉及软件工程和人工智能领域的专业术语。翻译时保留了学术文献的规范表达风格。

The provided text is in **English**.

Here is the Chinese translation:

---

**(Bk)** 表示智能体在时间 Tk 时的信念状态，与实际世界状态 **(Sk)** 存在偏差，从而导致因信息过时而产生协作失败。考虑图 1 中的人机协作场景：智能体在时间 Ti 根据其理解实施修改，而人类在时间 Tj（Ti < Tj < Tk）修改了代码库。由于信念状态 Bk 已过时，智能体在时间 Tk 的后续更新与当前状态 Sk 不兼容。这提出了一个关键挑战：协作者如何有效识别自身信念与实际状态不同步（Bk ≠ Sk）、诊断根本原因，并将其信念 Bk 恢复至与世界状态 Sk 匹配？

**SyncMind** 促进对协作编码智能体的多维度评估：

- **同步恢复有效性**（第 4.2 节）：我们评估智能体如何通过探索环境和咨询其他开发者来检测和解决状态错位，使其能够理解系统变更并在故障后重新同步。
- **协作倾向和有效性**（第 4.5 节）：我们测量智能体与协作者进行有效互动的倾向，这是协作软件工程（CSE）中的一个关键问题。通过分析独立工作与协作设置下的协助寻求率和绩效差异，我们测量智能体在 CSE 中的恢复有效性。
- **环境感知和资源分配**（第 4.7 节）：我们研究智能体如何在独立问题解决（即探索环境）与协作支持之间取得平衡。过度依赖自我调试会消耗计算资源，而过度依赖同伴支持则会给协作者带来重复的协助请求、修改和测试循环。我们通过分析恢复效率（考虑计算时间和费用预算）来评估同步恢复中的资源分配策略。

---

## 2. SyncMind：智能体同步恢复

为了解决智能体同步失调问题（第 2.1 节），我们引入了 **SyncMind** 框架（图 1），从两个关键维度系统性地衡量智能体同步恢复：通过两种恢复类型的**恢复有效性**（第 2.2 节）和**资源感知的同步恢复**（第 2.3 节）实现的**资源效率**。

### 2.1 智能体同步失调的定义

在协作环境中，当协作者的信念状态因错过其他团队成员的更新而与项目状态产生偏差时，就会出现“同步失调”状态（图 1）。我们提出了“同步失调”状态的正式定义，适用于人类和人工智能智能体。

令 Si 为时间 Ti 时的真实世界状态，Bi 为智能体的信念状态。从智能体开始执行任务的 Ti 时刻开始，若满足以下任一条件，则智能体在时间 Tk（Ti < Tk）时处于同步失调状态：

---

*[注：原文在此处中断]*

**语言识别：** 该段落为**英语**（English），涉及人工智能代理（agent）、软件工程协作及基准测试等计算机科学领域的学术技术文档。

---

**中文翻译：**

满足以下条件时：
（1）知识差距：存在更新U在时刻Tj（Ti < Tj < Tk），且代理缺乏对U的了解。
（2）状态不匹配：Bk ≠ Sk。
（3）任务失败：基于Bk的任务完成未能实现Sk中的预期结果。

量化财务资源消耗的假设成本，包括恢复过程中所消耗的计算资源（例如调试和测试所需的计算资源）、人类回答代理问题所付出的时间和精力。这种资源感知的异步恢复框架能够衡量代理在不同资源约束下如何利用和调整其策略，从而能够比较代理系统中成功恢复尝试与失败恢复尝试之间的效率。

因此，从异步状态恢复需要：
（1）识别状态不匹配的根本原因U。
（2）获取缺失更新U的信息。
（3）在未来某个时刻Tn（Tn > Tk）更新其信念状态，使得Bn = Sn。
图4. 资源感知的异步恢复。我们通过将资源消耗映射到每个异步恢复任务来引入资源感知恢复。

2.2 代理异步恢复
在SyncMind（图3）中，代理通过两种类型的恢复来更新其异步信念状态以实现Bn = Sn：
• **独立恢复**。独立代理自主运行，除对先前经验和反馈的反思外，还通过与环境（Env）交互并提出解决方案来更新其世界信念。
• **协作恢复**。协作代理还可以利用协作者的支持，通过与其他协作代理交互来更新其信念状态。

3. SyncBench：代理异步基准测试
3.1 基准测试构建
与现实世界的异步场景一致，我们的基准测试构建方法适用于具有现有单元测试的基于Python的GitHub仓库。SyncBench利用了21个流行的GitHub仓库，并可按照我们的基准测试构建方法（第B.2节）扩展以包含更多仓库。根据代理异步的定义（第2.1节），我们的基准测试构建实现了一个系统化的流水线，综合考虑以下三个条件：

2.3 资源感知恢复
为反映协作环境中的真实资源约束，我们在SyncMind（图4）中集成了一个资源感知模块。该模块跟踪并约束两个维度的资源：（1）恢复时间，衡量代理恢复所需的轮次；（2）……

环境配置。我们采用Docker（Founadi等，2013）来配置隔离、可复现且可执行的测试环境，专为我们的异步恢复任务量身定制。每个源代码仓库被打包成专用的Docker镜像，包含完整的代码库……

图5. 代理异步基准测试构建。系统化的基准测试构建方法（第3.1节）。

这段文字是**英文**。

以下为中文学术翻译：

依赖项以及用于单元测试执行的验证基础设施。每次执行验证（§3.4）都会自动创建一个隔离的容器实例，并在完成时自动移除，从而为可靠的恢复评估提供一致且干净的测试环境。

在对所有采样数据保持原始补丁分布的同时，对15个实例进行下采样，从而将相同的任务复杂度分布应用于所有降采样实例。

3.2 基准数据集

SyncBench由两个互补的数据集构建而成——调用方和被调用方（图5），初步提取得到24,332个实例（表B2）。通过多级过滤将原始数据集剪枝至8,461个实例，评估子集进一步通过加权降采样缩减。最终，我们将评估样本确定为300个实例，调用方和被调用方样本均匀分布（各150个）。

异步模拟。我们首先从源代码仓库中提取Python函数和类方法（以下简称函数）。对于每个提取的函数，我们使用其最新状态作为真实值（S2），同时通过逆向追踪其Git历史直到识别出执行失败的提交（B2）来获取异步信念状态（B2）。以此方式，调用方和被调用方分别通过模拟单元测试异步和被测依赖项异步来构建：（1）调用方：我们回滚测试函数直到其变为异步状态；（2）被调用方：我们回滚被测模块的导入依赖项以实现异步，从而呈现更高的任务复杂度——智能体需要理解依赖关系并定位有问题的导入模块。

3.3 大语言模型模拟协作者

我们利用大语言模型来模拟进入异步状态（B2）的智能体和全知协作者（S2）。

智能体异步。我们使用大语言模型为处于异步状态的AI智能体提供动力，使信念状态在整个恢复过程中变得可处理和可控制。同时，这也有助于精确测量智能体的资源消耗并系统性地评估智能体的恢复模式。

多级质量过滤。对于每个异步实例，我们在异步发生前后执行单元测试，并使用解析的测试输出来过滤高质量实例。我们基于解析的执行测试（§3.4）要求失败到通过的状态发散（B2 ≠ S2）：（1）更新后的仓库（S1）通过测试以证明真实值的有效性，（2）包含异步函数的仓库（B2）未通过测试以形成异步场景。为增强数据质量，我们额外应用一个过滤器，仅保留其执行输出包含以下内容的实例：（1）B2中至少有一个执行错误或单元测试失败，（2）S1中有多个通过的测试，（3）S1和Sn之间的解析结果相同。

全知协作者模拟。经单一来源验证后，协作者被设定为对被测代码和测试代码的所有变更都了如指掌，这意味着他们能够为智能体提供完美且全面的上下文信息。



---

## 论文 29

# PIPer: On-Device Environment Setup via Online Reinforcement Learning

**作者**: Alexander Kovrigin, Aleksandra Eliseeva, Konstantin Grotov, Egor Bogomolov, Yaroslav Zharov

**arXiv**: https://arxiv.org/abs/2509.25455v1

---

The paragraph is written in **English**.

Here is the translation:

---

**审稿中**

**PIP ER：基于在线强化学习的设备端环境配置**

arXiv:2509.25455v1 [cs.LG] 2025年9月29日

Alexander Kovrigin¹'², Aleksandra Eliseeva¹, Konstantin Grotov¹*, Egor Bogomolov¹'³, Yaroslav Zharov¹  
¹ JetBrains Research  ² Constructor University  ³ 代尔夫特理工大学  
konstantin.grotov@jetbrains.com

**摘要**

环境配置——即配置系统以适配特定软件项目的过程——是软件工程（SE）领域一个持续存在的挑战。自动化环境配置方法可以通过为任意代码仓库提供完整配置的环境来协助开发者，从而无需人工干预。这也有助于SE研究人员扩展基于执行的基准测试。然而，近期研究表明，即使是当前最先进的大型语言模型（LLMs）在自动化这一任务方面也成果有限。为解决这一局限，我们针对环境配置任务微调了一个专用模型。我们将监督微调（用于生成正确的Bash脚本）与可验证奖励强化学习（RLVR）相结合，以使其适应环境配置任务。在EnvBench-Python基准测试上，我们的方法使得Qwen3-8B（一个可在消费级硬件上运行的模型）能够与更大的模型——Qwen3-32B和GPT-4o——表现相当。训练代码和模型检查点已在线提供：https://github.com/JetBrains-Research/PIPer。

**引言**

大型语言模型（LLMs）在软件工程（SE）任务中展现出巨大潜力（Liu et al., 2024）。虽然闭源通用模型在基准测试中占据主导地位（Jain et al.; Jimenez et al., 2024），但开源模型仍具竞争力（DeepSeek-AI, 2025; Qwen Team, 2025; Kimi Team et al., 2025）。近期研究表明，由开源模型驱动的任务专用自主智能体能够解决各种SE问题，包括代码生成（Hasan et al., 2025）、缺陷定位（Ma et al., 2025; Chang et al., 2025; Reddy et al., 2025; Chen et al., 2025b）以及问题修复（Luo et al., 2025; Wang, 2025; Pan et al., 2025; Zeng et al., 2025; Ma et al., 2025; Chang et al., 2025）。

开发高效任务专用智能体的一种常见策略是在精心整理的数据集上对其进行训练（Pan et al., 2025; Zeng et al., 2025）。然而，在SE领域，瓶颈已从复杂的数据过滤策略转向首先获取足够的数据。由于智能体以交互方式运作，这需要扩展交互式环境的构建。而这往往需要正确配置系统以能够执行示例代码。在本文中，我们将这一配置过程称为环境配置。

这一局限性对SE基准测试具有深远影响。例如，SWE-Bench（Jimenez et al., 2024）是SE智能体最具代表性的基准测试之一，其仅包含12个Python代码仓库，而收集和维护它需要大量人工工作。扩展此类数据...

The language of the provided paragraph is **English**.

**Translation (English → Chinese):**

环境设置通常依赖于手动配置（Pan等人，2025）或合成数据增强（Pham等人，2025），以牺牲真实性为代价换取规模。自动化环境设置方法（Guo等人，2025；Badertdinov等人，2025；Zhang等人，2025；Vergopoulos等人，2025）承诺使用真实数据实现可扩展性，但仍然存在局限性——例如，SWEBench（Badertdinov等人，2025）报告在Python仓库上的总体成功率为31%，而在EnvBench（Eliseeva等人，2025）这一专门针对困难仓库的环境设置基准上，最佳结果仅为6.69%（329个仓库中成功22个），由GPT-4o在智能体工作流中取得。

我们致力于改进小型开源模型，以普及LLM在环境设置中的应用。为此，我们分析了强LLM在EnvBench上生成的环境设置脚本，并采用监督微调（SFT）和强化学习（RL）来解决发现的问题。所提出的方法相比基础模型实现了超过9倍的提升，与四倍大小的开源模型相当，并接近强闭源基线水平。具体贡献包括：（1）首次将基于轻量级可验证奖励的在线强化学习应用于环境设置；（2）适用于设备端的PIP ER模型，性能与强基线相当，同时提供更优的性能成本比；（3）严格的评估表明，使用该方法训练的模型在不同数据集上具有泛化能力，表明脚本编写能力得到了实质性提升。为促进可复现性和未来研究，我们公开提供了代码、模型权重和生成的脚本。

本论文的其余部分安排如下：第2节描述用于训练和评估的数据集；第3节介绍并描述训练方法；第4节说明实验设置；第5节概述实验结果。

**数据集**

我们工作的重点是普及LLM在环境设置中的应用。为衡量在这一任务上的进展，我们选择了两个环境设置基准：EnvBench（Eliseeva等人，2025）和Repo2Run（Hu等人，2025b）。此外，为检验训练对更广泛任务的影响，我们采用了Terminal-Bench（Terminal-Bench团队，2025）。本节概述我们使用的每个数据集的具体信息——评估方法的输入和输出，以及任务完成的定义。

EnvBench-Python包含来自GitHub的329个Python仓库。作为输入，环境设置方法可以访问完整的仓库上下文和基础环境配置。具体如何利用此上下文取决于方法定义：它可以是预定义的提示词、交互式智能体工作流等。作为输出，环境设置方法应生成一个安装所有依赖项的shell脚本。

**语言识别**：该段落为**英语**（English）。

---

**翻译（中文）**：

他需要在基础环境中安装依赖项。环境配置脚本的正确性首先通过执行该脚本进行评估，然后调用 Pyright2——一种静态分析工具，用于检查代码库中的导入是否成功解析。如果脚本以退出码 0 结束，且后续的 Pyright 检查未报告任何导入问题，则认为该仓库配置正确。

Repo2Run 包含 420 个来自 GitHub 的 Python 仓库，与 EnvBench-Python 无重复。原工作主要关注智能体设置，其中环境配置智能体可通过终端界面及其他专用工具访问基础环境和仓库。随后，智能体需要通过与环境交互来自主配置仓库。与 EnvBench-Python 基于静态分析的指标不同，Repo2Run 通过 pytest3 运行测试收集以验证环境配置的正确性。我们引入 Repo2Run 是为了验证我们的实验结果能否跨不同仓库和成功标准迁移。此外，我们将 Repo2Run 适配到智能体设置之外的环境，采用与 EnvBench-Python 更为通用的任务形式（第 4.2 节详细讨论）。

Terminal-Bench 包含 80 个专注于命令行环境配置任务（我们使用该基准的 0.1.1 版本），用于评估 AI 智能体处理真实世界端到端终端操作的能力，包括编译代码、训练模型和搭建服务器。每个任务由自然语言描述的问题（传递给大语言模型）、Docker 环境以及用于验证智能体是否成功完成任务的测试脚本组成。我们使用原始实现 4，并采用多轮智能体框架 Terminus 1。成功标准取决于智能体能否在沙箱环境中完成指定的终端目标。我们使用 Terminal-Bench 来评估我们的训练管道（主要针对单轮 Python 包配置）是否能够泛化到更复杂的终端操作。

**复制包**：https://github.com/JetBrains-Research/PIPer
https://microsoft.github.io/pyright
https://pytest.org
https://github.com/laude-institute/terminal-bench

**审稿中**

（图 1 展示了所提出训练管道的概述。（a）SFT 训练：对于第 i 个样本（一个仓库），教师和学生大语言模型均收到提示 qi，其中包括任务描述和仓库上下文。它们分别生成补全 oti 和 osi，预期包含 shell 脚本。通过最小化学生模型输出分布与教师模型输出分布之间的交叉熵损失来更新学生模型的权重……）

The provided text is written in **English**.

Here is the accurate academic translation into Chinese:

---

以及教师模型的完成。(b) 强化学习训练：对于每个样本，大语言模型π_θ生成一个完成o_i，预期包含一个shell脚本。该完成由基于规则的奖励函数R进行评估，输出得分R_i。REINFORCE++算法随后利用奖励R_i和响应o_i来更新大语言模型的权重。

安装场景，能够泛化到更广泛的分布外、多轮终端命令执行任务，超出依赖管理的范围。

**方法**

为了训练模型，我们采用文献中广泛采用的两阶段过程（Liu等人，2025b；Yoshihara等人，2025；Golubev等人，2025）。首先，我们以监督方式在同一家族更大模型采样的可执行脚本上对模型进行微调。然后，我们运行强化学习训练的第二阶段，以在监督微调更新后进一步提升模型的能力。我们采用RLVR技术，因为据报道该技术在软件工程领域的任务上表现出有前景的结果（Luo等人，2025；Golubev等人，2025）。在本文的整个notation中，我们用q表示提供给模型的提示，用o表示模型响应，用s表示从模型输出中提取的shell脚本，用π_θ表示参数为θ的模型。我们使用正则表达式从模型输出中提取shell脚本，如果解析失败，则认为s为空。训练阶段的示意图如图1所示。此外，我们详细介绍该方法。在3.1节中讨论监督微调训练，在3.2节中介绍强化学习训练。

**3.1 监督微调**

监督微调涉及在被认为是真实标签的数据点集上训练模型。然而，对于环境设置任务，特别是对于困难的仓库，获取此类真实标签脚本代价高昂。EnvBench的作者仅提供了少量由专家生成的脚本，即使是最强的模型也只能解决该数据集的一小部分（Eliseeva等人，2025）。因此，我们采用蒸馏技术（Hinton等人，2015），即较小的模型（称为学生模型）学习模仿较大模型（称为教师模型）的行为。我们的设置如图1(a)所示，详述如下。

我们使用在评估更大Qwen3-32B模型期间收集的可执行脚本实现监督微调阶段。我们首先从评估rollout中收集样本{q_i, o_i^t}。然后，我们过滤掉o_i^t不包含脚本或脚本返回非零退出码的样本。最后，我们随机选择2500对{q_i, o_i^t}形成蒸馏数据集。学生模型π_θ在此数据集上以监督方式进行训练，无进一步修改或掩码。由于这些样本源自不同的、更大的模型而非π_θ，生成的解决方案与我们的模型自然输出分布之间可能存在分布偏移，这可能

---

**翻译说明：**

1. 术语处理：
   - REINFORCE++ → REINFORCE++（保留英文算法名称）
   - SFT (Supervised Fine-Tuning) → 监督微调
   - RL (Reinforcement Learning) → 强化学习
   - RLVR → RLVR（保留技术缩写）
   - distillation → 蒸馏
   - Student/Teacher → 学生模型/教师模型

2. 学术表达规范化：使用了"据报道"、"旨在"、"详述"等学术书面表达方式。

3. 符号保留：公式符号如π_θ、q_i、o_i等保留原始形式。

**语言识别：** 该文本为**英语**（English）。

---

**翻译（英译中）：**

影响模型的泛化能力（Shenfeld et al., 2025; Chu et al.）。然而，这种方法使我们能够利用更高质量的可执行解决方案，这些方案展示了成功的任务完成模式。生成的SFT检查点作为后续RLVR训练的基础。

**3.2 强化学习**

奖励设计是RLVR训练的关键组成部分。常见的选择是为每个模型响应使用基于二元结果的奖励（Luo et al., 2025）。对于环境设置任务，这意味着评估每个脚本是否成功配置了相应的代码库。为了安全起见，每个脚本必须在隔离的容器中运行，再加上高效RLVR训练所需的大规模（例如，最近的研究并行运行多达512个容器（Luo et al., 2025）），这带来了巨大的计算和技术开销。为了应对这些挑战，我们转向轻量级的免执行LLM-as-a-Judge奖励（记为$R_{LLM}$），它通过模拟基于规则的评估标准来作为可验证的奖励。其总体方案如图1(b)所示。

为了设计奖励，我们对GPT-4o为40个代码库样本生成的脚本进行了定性研究。总体而言，我们发现失败是由于模型无法完全理解代码库的上下文、其运行的系统以及它们需要使用的工具。具体来说，我们发现了模型生成的脚本中的11种失败模式，以及GPT-4o无法克服的代码库提出的3种配置挑战。这些失败分为两类：一类产生非零退出代码，主要由语法错误（占代码库的10%）和模型未能解决冲突的依赖版本（7.5%）引起；另一类导致Pyright报告的未解决的导入问题，最常见的是模型未能安装代码库中存在但未在配置文件中指定的依赖项（25%），以及开发所需的可选依赖项，如测试包或linter（22.5%）。分析过程和所有发现的详细描述见附录B。

奖励$R_{LLM}$接收提取的脚本$s$以及相应代码库的全面上下文，并模拟EnvBench评估套件。评判者的指令受我们对典型错误的发现所驱动，并提示它预测shell脚本执行的退出代码和Pyright问题的数量（num_issues）。更多实现细节见第A.4节。形式上，奖励计算如下：

$$
R_{LLM}(s) = 
\begin{cases}
-1.0, & \text{如果 } s \text{ 为空} \\
0.0, & \text{如果 } \text{exit\_code}(s) \neq 0 \\
\max(1.0 - \text{num\_issues}(s), 0.0), & \text{否则}
\end{cases}
$$

---

**4 实验设置**

**4.1 训练设置**

数据。根据最近关于代码基准测试的工作（Gehring et al.; Jain et al., 2025; Le et al., 2022），代理通过在评估所用的相同问题上进行试错来学习，我们的



---

## 论文 30

# Skywork-SWE: Unveiling Data Scaling Laws for Software Engineering in LLMs

**作者**: Liang Zeng, Yongcong Li, Yuzhen Xiao, Changshi Li, Chris Yuhao Liu

**arXiv**: https://arxiv.org/abs/2506.19290

---

The provided text is written in **English**.

Here is the Chinese translation:

---

Skywork-SWE：揭示大语言模型软件工程领域的数据缩放法则

曾亮、李永康、肖宇晨、李常诗、刘宇浩、闫睿、田文伟、何 Jujie、宋旭晨、刘阳、周雅慧

arXiv:2506.19290v1 [cs.AI] 2025年6月24日

Skywork AI，昆仑万维

软件工程（SWW）近期已成为下一代大语言模型（LLM）智能体的关键测试平台，其要求在两个关键维度上具备内在能力：持续迭代式问题解决（例如超过50轮交互）以及长上下文依赖解析（例如超过32k tokens）。然而，软件工程领域的数据整理过程仍以耗时著称，因为这主要依赖于人工标注进行代码文件过滤，以及搭建专用的运行时环境来执行和验证单元测试。因此，现有的多数数据集仅包含数千个来自GitHub的实例。为此，我们提出了一种增量式自动化数据整理管道，系统性地扩展了软件工程数据集的规模和多样性。我们的数据集包含来自2,531个不同GitHub仓库的10,169个真实世界Python任务实例，每个实例都配有自然语言描述的任务说明和用于自动化单元测试验证的专用运行时环境镜像。我们从所提出的软件工程数据集中精心整理了超过8,000条成功通过运行时验证的训练轨迹。在使用这些轨迹对Skywork-SWE模型进行微调后，我们揭示了一个显著的数据缩放现象：训练模型在软件工程能力方面的表现随着数据规模的增大而持续提升，未见任何饱和迹象。值得注意的是，我们的Skywork-SWE模型在SWE-bench Verified基准测试上实现了38.0%的pass@1准确率，且未使用验证器或多轮采样，在基于OpenHands智能体框架的Qwen2.5-Coder-32B大语言模型中建立了新的技术最优（SOTA）水平。此外，通过引入测试时缩放技术，性能进一步提升至47.0%准确率，超越了此前32B以下参数模型的最优结果。最后，我们提炼出一套实用指南，旨在进一步推动学术界和工业界在大语言模型驱动软件工程领域的发展。我们发布了Skywork-SWE-32B模型检查点，以加速未来研究。

关键词：软件工程、数据缩放法则、大语言模型
日期：2025年6月20日
博客：https://quixotic-sting-239.notion.site/eb17f379610040ceb54da5d5d24065bd
模型权重：https://huggingface.co/Skywork/Skywork-SWE-32B
联系方式：liang.zeng@kunlun-inc.com

1. 引言
空谈误国，实干兴邦。
——林纳斯·托瓦兹

两个核心能力定义了大语言模型智能体的新兴潜力：进行多轮交互的能力以及在长上下文输入上进行推理的能力（OpenAI，2025，Team等，2023，Guo等，2025，Weng，2023）。在众多现实世界应用中，软件工程（SWW）任务（JIMenez Correspon

这段文字是**英语**（English）。

**翻译如下：**

---

**Skywork-SWE：揭示LLM软件工程领域的数据扩展规律**

图1.（上图）Skywork-SWE模型在SWE-bench Verified基准测试上达到了38.0%的pass@1准确率，超越了此前基于OpenHands智能体框架的开源最先进水平Qwen2.5-Coder-32B LLM。此外，Skywork-SWE清晰展示了LLM软件工程能力的数据扩展规律现象，在8,209条收集的训练轨迹上尚未出现饱和迹象。所有评估均基于OpenHands v0.32.0框架进行，每条实例仅尝试一次，不使用验证器或多轮展开。（下图）使用OpenHands在SWE-bench Verified上各最新先进方法的性能对比。通过引入测试时扩展（TTS）技术，Skywork-SWE的准确率进一步提升至47.0%，超越了最新的Qwen和DeepSeek系列模型。

......等问题（Pan et al., 2024, Yang et al., 2025）上。与传统的代码生成任务不同，后者仅生成简单的代码片段来解决编程竞赛问题（Jain et al., 2024, Zhuo et al., 2024），SWE任务需要跨多轮交互进行迭代式问题求解，以利用代码工具，并具备管理代码文件中长上下文依赖的能力，从而应对现实世界的软件工程挑战（Pan et al., 2024, Yang et al., 2025）。随着SWE-bench（Jimenez et al., 2024）、SWE-bench Verified（OpenAI, 2024e）等基准测试数据集的日益突出，既反映了研究兴趣的不断增长，也体现了LLM驱动软件工程所面临的内在挑战。

尽管取得了这些进展，现有数据集仍存在阻碍该领域进展的关键局限性：
• 环境和验证支持不足。如表1所示，现有基准测试通常缺乏配置可执行运行时环境或标准化代码的全面机制。

**语言识别：** 该段落为**英文**。

---

**中文翻译：**

一个所有必要依赖项均已预安装的可执行环境。已验证的单元测试：相关的单元测试是否已通过验证，以确保 FAIL_TO_PASS 和 PASS_TO_PASS 结果的正确性。代码执行套件：是否提供统一且自动化的脚本，可在无需人工干预的情况下自动初始化、配置并执行跨不同代码库的测试。Skywork-SWE 数据集涵盖了 SWE 基准的三个关键维度，并经过系统性扩展，以突出显示 SWE 数据集的规模（#Instances）和多样性（#Repos）。

| 数据集 | 可执行环境 | 已验证单元测试 | 代码执行套件 | #Instances | #Repos |
|--------|------------|----------------|--------------|------------|--------|
| SWE-bench (Jimenez et al., 2024) | ✓ | ✓ | ✗ | 2,294 | 12 |
| SWE-bench Lite (Jimenez et al., 2024) | ✓ | ✓ | ✗ | 300 | 12 |
| SWE-bench Verified (OpenAI, 2024e) | ✓ | ✓ | ✗ | 500 | 12 |
| SWE-bench Extra (Badertdinov et al., 2024) | ✗ | ✗ | ✓ | 6,376 | 1,974 |
| SWE-Fixer (Xie et al., 2025) | ✗ | ✗ | ✗ | 115,406 | 856 |
| SWE-Smith (Yang et al., 2025) | ✓ | ✓ | ✗ | 50,137 | 128 |
| SWE-Gym (Pan et al., 2024) | ✓ | ✓ | ✗ | 2,438 | 11 |
| **Skywork-SWE** | **✓** | **✓** | **✓** | **10,169** | **2,531** |

执行套件用于跨不同代码库系统性地验证生成的代码补丁。例如，SW E-bench Extra（Badertdinov et al., 2024）和 SWE-Fixer（Xie et al., 2025）要么完全省略可执行环境，要么缺乏严格的测试验证，导致评估结果不一致且不可复现。

• **高质量训练数据的稀缺性**。尽管现有某些数据集规模较大，但很少提供经过严格验证的高质量训练实例。这种公开可用的验证数据缺乏问题，导致开源 LLM 在 SWE 任务上始终表现不如专有模型。例如，SW E-Dev（Wang et al., 2025）缺乏经过严格验证的训练实例，而 SWE-Gym（Pan et al., 2024）的代码库覆盖率有限。

• **数据缩放定律的适用性不明确**。SWE 任务的训练数据量明显小于其他 LLM 领域（Pan et al., 2024）。数据缩放定律（Kaplan et al., 2020b; Hoffmann et al., 2022a）在软件工程领域是否仍然适用仍不确定。解决这一问题对于指导未来数据集扩展和优化模型训练策略至关重要。

针对这些挑战，我们开发了一个自动化数据策展管道，系统性地扩展了 Skywork-SWE 数据集的规模和多样性。如表 1 所示，Skywork-SWE 数据集包含从 2,531 个 GitHub 代码库中收集的 10,169 个真实世界 Python 任务实例。Skywork-SWE 数据集中的每个实例都包含详细的自然语言描述以及专门设计用于支持自动化执行和测试验证的关联可执行运行时环境。我们提出了一个由三阶段组成的增量管道，包括数据收集与预过滤、基于执行的验证，以及在统一框架内为 SWE 任务生成智能体轨迹。

The language of the provided paragraphs is **English**.

Here is the Chinese translation:

---

ork.这种方法通过结合GitHub仓库的广泛覆盖和严格的可复现性，确保了Skywork-SWE数据集的高质量和多样性。
利用该数据集，我们对Skywork-SWE模型进行了超过8,000条成功验证轨迹的微调，在不使用验证器或多次采样的情况下，在SWE-bench Verified基准测试上实现了38.0%的pass@1准确率。这在基于OpenHands代理框架的Qwen2.5-Coder-32B大型语言模型中确立了新的最先进水平。如图1所示，我们的大规模实验揭示了明确的数据 scaling 法则：训练有素的模型软件工程能力随着训练数据量的增加而持续提升，验证了scaling法则在SWE任务中的适用性。此外，应用测试时scaling技术将性能提升至47.0%的准确率，超过了参数少于320亿的大型语言模型的先前最先进结果。

Skywork-SWE旨在弥合开源与专有SWE代理模型之间的差距，促进大型语言模型驱动软件工程的透明度、可复现性和进步。我们的主要贡献总结如下：
• 我们提出了一种高效且自动化的SWE数据收集流程，构建了Skywork-SWE数据集，这是一个大规模、高质量的数据集，具有全面的可执行运行时环境。
• 我们发布了Skywork-SWE-32B，这是一款专为SWE任务设计的强大开源代码代理模型，在同规模的开源SWE代理中建立了新的性能基准。
• 我们在SWE任务中实证观察到了数据scaling法则，证明了随着训练数据量的增加，性能持续提升。这一发现不仅验证了scaling法则在软件工程中的适用性，也凸显了构建更大、更高质量的数据集以进一步提升模型性能的必要性。

2. 相关工作
2.1. 大型语言模型中的代码相关任务
大型语言模型（LLMs）在广泛的代码相关任务中取得了显著进展（Jiang et al., 2024; Chang et al., 2024），从代码片段生成（Leblond et al., 2023）到复杂的软件工程任务（Jimenez et al., 2024）。下面，我们简要概述大型语言模型在代码相关任务中的两个主要方向。
代码生成使大型语言模型能够根据自然语言描述合成功能程序。早期的函数级基准测试，如HumanEval（Chen et al., 2021）和MBPP（Austin et al., 2021），为这一任务奠定了基础，推动了面向代码的大型语言模型的发展，包括AlphaCode（Leblond et al., 2023）、Code Llama（Roziere et al., 2023）、WizardCoder（Luo et al., 2023）、StarCoder（Li et al., 2023）和DeepSeek-Coder（DeepSeek AI, 2024）。通过精心策划的训练数据集和专门的微调技术，这些模型在代码生成任务上展现出强大的性能，并且几乎已经饱和了相关基准测试。

这段文字是**英语**（English）。

下面是翻译成的中文学术文本：

传统基准测试的性能存在局限性。为了推进评估工作，最近出现的基准测试（如LiveCodeBench（Jain等人，2024）和BigCodeBench（Zhuo等人，2024））引入了无污染、实用且具有挑战性的编程问题，能够对现代大语言模型的代码能力进行更严格的评估（Jaech等人，2024，OpenAI，2024d）。作为回应，领先的大型推理模型（LRM），如OpenAI的o3（OpenAI，2025）、DeepSeek-R1（Guo等人，2025）和Kimi-k1.5（Team等人，2025），采用强化学习来激励大语言模型的思维链推理能力，从而显著提升了在这些代码基准测试上的表现。

软件工程（Software Engineering，简称SWE）专注于在仓库环境中解决真实的GitHub问题，要求代理定位bug、修改源代码，并根据执行结果验证修复。这一实际任务标志着该领域的一次转变——从静态的单轮代码生成转向动态的交互式编码工作流程——大大扩展了大语言模型在真实软件开发场景中的适用性和能力。SWE-bench（Jimenez等人，2024）及其后续版本SWE-bench Verified（OpenAI，2024e）是该领域的标准基准测试。它们提供了数千个真实的GitHub问题，以及完整的代码库、自然语言描述和人工筛选的回归测试套件，可靠地评估大语言模型解决真实软件问题的能力。为SWE代理创建训练数据是一个困难且劳动密集的过程。最近，许多工作致力于合成SWE任务的训练数据，每个实例都需要在相应的运行时环境中进行验证。SWE-Gym（Pan等人，2024）提供了包含真实SWE任务和相应单元测试的可执行环境，尽管规模相对有限，仅有2000多个实例。SWE-bench-extra（Badertdinov等人，2024）扩展了构建SWE-bench基准测试的方法论（Jimenez等人，2024），生成了6415个Python问题-拉取请求实例。SWE-Dev（Wang等人，2025）提出了一个使用结构化描述和执行验证的测试用例构建管道，以绕过与完整运行时验证相关的开销。为了使基于大语言模型的代理能够从大规模SWE数据中学习，SWE-fixer（Xie等人，2025）和SWE-Smith（Yang等人，2025）通过注入和验证合理的bug来合成生成Fail_to_Pass实例，从而产生了数千个SWE任务实例。此外，强化学习方法——以SkyRL（Cao等人，2025）为代表——结合异步 rollout 和基于验证器的反馈，促进SWE任务中的长程解决策略。

在这项工作中，我们提出了一个新的SWE数据语料库，包含从2531个不同的真实Python任务中收集的超过10000个实例。



---

## 论文 31

# Saving SWE-Bench: A Benchmark Mutation Approach for Realistic Agent Evaluation

**作者**: Spandan Garg, Benjamin Steenhoek, Yufan Huang

**arXiv**: https://arxiv.org/abs/2510.08996

---

标题：拯救SWE-Bench：一种用于现实智能体评估的基准测试突变方法

内容：
Saving SWE-Bench: A Benchmark Mutation Approach for
Realistic Agent Evaluation
Spandan Garg∗

arXiv:2510.08996v4 [cs.SE] 2026年1月23日

spgarg@microsoft.com
微软
美国

Benjamin Steenhoek

bensteenhoek@microsoft.com
微软
美国

Yufan Huang

yufanhuang@microsoft.com
微软
美国

摘要

当前用于评估软件工程智能体的基准测试（如SWE-Bench Verified）主要源自GitHub问题，无法准确反映开发者在集成开发环境（IDE）中与聊天式编程助手的交互方式。我们认为，这种差异导致了对智能体在现实场景中能力的高估，尤其是在错误修复方面。我们引入了一种新的基准测试框架，通过系统分析开发者与聊天式智能体的交互模式，将现有正式基准测试转化为现实用户查询。我们的方法灵活且易于扩展到现有基准测试。在本文中，我们将测试框架应用于SWE-Bench Verified、Multi-SWE-Bench的TypeScript子集以及内部基准测试SWE-Bench C#，并根据流行聊天式智能体交互的遥测分析，将正式的GitHub问题描述转化为现实的用户风格查询。我们的发现表明，对于公开基准测试，现有基准测试将某些模型的能力高估了超过50%，对于内部基准测试高估了约10-16%。这项工作通过基准测试突变技术为评估交互式聊天式软件工程智能体建立了新的范式。我们的代码可在以下地址获取：https://github.com/microsoft/SWE-Bench-Mutated-CAIN26

人工智能赋能的软件工程智能体的出现改变了开发者格局。交互式聊天式智能体，包括Claude Code [2]、VSCode Agent [12]，代表了与传统完全自主智能体（如GitHub Copilot Agent [5]）的范式转变，后者独立处理完整的问题规范。与其自主对应物不同，交互式智能体（如Claude Code）与开发者进行迭代的、情境化的对话，并在对话过程中逐步解决问题。因此，评估它们需要能够捕捉软件开发中人机聊天交互细微差别的方法。

当前软件工程智能体的评估基准，特别是SWE-Bench Verified [13]，在评估交互式聊天式智能体方面存在一些根本性局限性。首先，这些基准测试由GitHub问题构建，通常包含详细且经过深思熟虑的问题描述，这与IDE聊天交互中典型的简洁、非正式查询存在实质性差异。其次，这些基准测试的公开可用性和广泛研究导致了模型过拟合[11]，即系统在公开基准测试上表现良好，但在非公开基准测试上无法泛化。

为了解决这些局限性，我们引入了一种基准测试突变方法，该方法基于开发者行为的实证分析，将现有正式问题描述转化为更现实的用户查询。我们的方法利用了来自基于IDE的智能体的内部遥测数据，我们用它来识别开发者如何向聊天式助手传达错误的常见模式，然后使用这些模式在保留基本技术内容的同时系统地转换基准测试问题。

本文的贡献如下：

1. 开发者沟通模式分析：我们提出了一种新颖的分析，探讨开发者如何向聊天式智能体传达错误，旨在构建这样的突变管道。我们识别了几种代表现实世界用户聊天交互的独特模板。

2. 基准测试突变方法：我们引入了一种将正式基准测试问题转化为现实初始用户查询的系统方法。作为在现实世界基准测试上测试我们方法的方法，我们创建了SWE-Bench Verified-Mutated、Multi-SWE-Bench（TypeScript）-Mutated和SWE-Bench C#-Mutated数据集。我们展示了如何

ACM参考格式：
Spandan Garg, Benjamin Steenhoek, and Yufan Huang. 2026. Saving
SWE-Bench: A Benchmark Mutation Approach for Realistic Agent Evaluation. In Proceedings of 5th International Conference on AI Engineering – Software Engineering for AI (CAIN '26). ACM, New York, NY,
USA, 11 pages. https://doi.org/10.1145/nnnnnnn.nnnnnnn

∗ 通讯作者

允许出于个人或课堂使用目的免费制作本作品的全部或部分数字或纸质副本，前提是副本不用于或分发以获取利润或商业优势，且副本须附带本通知和首页的完整引用。对于本作品中由他人拥有的组件的版权必须遵守。允许在注明出处的情况下进行摘要。复制

---

