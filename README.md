# 人类线粒体基因组系统发育分析 / Human Mitochondrial Genome Phylogenetic Analysis

## 项目描述 / Project Description

本项目旨在利用公共数据库（NCBI）中当前所有可获取的人类线粒体DNA（mtDNA）完整基因组序列（共64,028条），构建一个高分辨率的人类线粒体系统发育树。通过这一分析，我们可以从宏观上追溯人类母系遗传谱系的进化历史、观察不同人群的遗传多样性，并为后续的群体遗传学研究提供参考。

This project aims to construct a high-resolution phylogenetic tree of human mitochondrial DNA (mtDNA) using all currently available complete mitochondrial genome sequences (64,028 in total) from public databases (NCBI). This analysis will allow us to trace the evolutionary history of human maternal lineages on a macro scale, observe the genetic diversity among different populations, and provide a reference for subsequent population genetics studies.

## 项目步骤概览 / Project Steps Overview

1.  **数据获取 / Data Acquisition**: 从NCBI数据库批量下载全部人类线粒体完整基因组序列。
    Bulk download all complete human mitochondrial genome sequences from the NCBI database.
2.  **多序列比对 / Multiple Sequence Alignment**: 使用MAFFT软件对所有序列进行精准的多序列比对，确保同源位点对齐。
    Use MAFFT software to perform accurate multiple sequence alignment of all sequences, ensuring homologous sites are aligned.
3.  **模型选择与建树 / Model Selection and Tree Building**: 使用IQ-TREE软件进行 / Use IQ-TREE software to:
    *   **模型比较 / Model Comparison**: 自动测试多种核苷酸替代模型，并选择最适合当前数据的模型。
        Automatically test multiple nucleotide substitution models and select the best-fit model for the current data.
    *   **系统树构建 / Phylogenetic Tree Construction**: 基于所选的最佳模型，构建最大似然系统发育树。
        Construct a Maximum Likelihood phylogenetic tree based on the selected best model.
4.  **结果解释 / Result Interpretation**: 对生成的系统树进行生物学解释，可视化展示（例如，从完整数据中抽取一个30条序列的子集生成示意图）。
    Biologically interpret the generated phylogenetic tree and create visualizations (e.g., generate a schematic diagram from a subset of 30 sequences extracted from the full dataset).

## 文件说明 / File Description

- `all.aln.uniqueseq.aln`: 经过MAFFT比对后产生的序列比对文件（FASTA或Phylip格式）。
  The sequence alignment file (FASTA or Phylip format) produced by MAFFT alignment.
- `all.aln.uniqueseq.aln.ckp`: IQ-TREE的检查点文件，用于恢复意外中断的分析。
  IQ-TREE checkpoint file used to resume an accidentally interrupted analysis.
- `all.aln.uniqueseq.aln.iqtree`: IQ-TREE的主要结果报告文件，包含详细的分析摘要、模型选择结果、树信心评估等。
  IQ-TREE's main report file, containing a detailed analysis summary, model selection results, tree confidence assessments, etc.
- `all.aln.uniqueseq.aln.treefile`: 以NEWICK格式存储的最终最大似然树。
  The final Maximum Likelihood tree stored in NEWICK format.
- `all.aln.uniqueseq.aln.contree`: 共识树文件（如果使用了`-b`或`-B`选项进行自举法分析）。
  Consensus tree file (if the `-b` or `-B` option was used for bootstrap analysis).
- `all.aln.uniqueseq.aln.bionj`: BIONJ树文件，通常用作最大似然分析的起始树。
  BIONJ tree file, often used as the starting tree for the Maximum Likelihood analysis.
- `all.aln.uniqueseq.aln.mldist`: 最大似然距离矩阵。
  Maximum Likelihood distance matrix.
- `all.aln.uniqueseq.aln.model`: 模型文件，包含所选模型的参数。
  Model file containing the parameters of the selected model.
- `all.aln.uniqueseq.aln.splits.nex`: 以Nexus格式存储的支持率网络（splits），可用于其他软件（如SplitsTree）可视化。
  Support network (splits) stored in Nexus format, which can be used for visualization in other software (e.g., SplitsTree).

## 核心知识背景 / Core Knowledge Background

### 1. 生物学背景 / Biological Background

- **人类线粒体DNA / Human Mitochondrial DNA**: 是母系遗传的环状DNA分子，约16.6 kb。其进化速率比核DNA快10-20倍，且缺乏重组，使其成为研究近期进化历史和群体遗传学的强大工具。
  A maternally inherited circular DNA molecule, approximately 16.6 kb. Its evolutionary rate is 10-20 times faster than nuclear DNA, and it lacks recombination, making it a powerful tool for studying recent evolutionary history and population genetics.
- **系统发育学 / Phylogenetics**: 通过比较物种或其他分类单元的遗传特征（如DNA序列）来推断它们之间的进化关系。目标是构建一个代表这些序列进化历史的“树”（系统发育树），其中分支点代表共同的祖先。
  The inference of evolutionary relationships among species or other taxa by comparing genetic characteristics (e.g., DNA sequences). The goal is to construct a "tree" (phylogenetic tree) representing the evolutionary history of these sequences, where branch points represent common ancestors.
- **单倍群 / Haplogroups**: 共享一个共同祖先的mtDNA谱系，通常由特定的突变组合定义。全球人类mtDNA可分为多个主要单倍群（如L, M, N, H等），它们记录了人类走出非洲和迁徙的历史。
  mtDNA lineages that share a common ancestor, usually defined by a specific set of mutations. Global human mtDNA can be divided into several major haplogroups (e.g., L, M, N, H), which record the history of human migration out of Africa.

### 2. 数学与统计学背景 / Mathematical and Statistical Background

- **最大似然法 / Maximum Likelihood (ML)**: 本项目建树的核心方法。它寻找一棵能“以最高概率”产生我们所观察到的序列数据的系统树和进化模型。计算公式涉及在所有可能的树上对位点概率求和，这是一个NP难问题，因此需要启发式算法。
  The core method for tree building in this project. It finds the phylogenetic tree and evolutionary model that would have most likely produced the observed sequence data. The calculation involves summing site probabilities over all possible trees, an NP-hard problem, hence requiring heuristic algorithms.
- **模型选择 / Model Selection**: 使用**贝叶斯信息准则（BIC）** 或**Akaike信息准则（AIC）** 等统计标准来比较不同模型的拟合优度，同时惩罚模型的复杂性（参数数量），以防止过拟合。BIC更倾向于选择更简单的模型。
  Uses statistical criteria like the **Bayesian Information Criterion (BIC)** or **Akaike Information Criterion (AIC)** to compare the goodness-of-fit of different models while penalizing model complexity (number of parameters) to prevent overfitting. BIC tends to prefer simpler models.
- **自举法 / Bootstrapping**: 一种重采样技术，用于评估系统树分支的可信度。通过对比对数据随机重采样（有放回）生成数百个甚至数千个重复数据集，并重建每个数据集的树。一个分支的自举支持率越高，代表该分支在数据中越可靠。
  A resampling technique used to assess the confidence of phylogenetic tree branches. Hundreds or thousands of replicate datasets are generated by randomly resampling (with replacement) the alignment data, and a tree is rebuilt for each. A higher bootstrap support value for a branch indicates greater reliability.

### 3. 计算机科学背景 / Computer Science Background

- **算法复杂性 / Algorithmic Complexity**: 系统发育重建是一个计算密集型任务。对于64k条序列，彻底搜索所有可能的树形在计算上是不可行的（树的数量是超指数增长的）。IQ-TREE使用了高效的启发式算法（如星形分解）和随机搜索来寻找似然率最高的树。
  Phylogenetic reconstruction is a computationally intensive task. For 64k sequences, an exhaustive search of all possible tree topologies is computationally infeasible (the number of trees grows super-exponentially). IQ-TREE uses efficient heuristic algorithms (e.g., star decomposition) and stochastic search to find the tree with the highest likelihood.
- **并行计算 / Parallel Computing**: 为了处理如此大规模的数据，IQ-TREE可以利用多核CPU进行并行计算，显著缩短分析时间（例如，使用`-T AUTO`选项）。
  To handle such large datasets, IQ-TREE can utilize multi-core CPUs for parallel computing, significantly reducing analysis time (e.g., using the `-T AUTO` option).
- **数据压缩 / Data Compression**: 由于序列数量巨大，IQ-TREE会自动识别并压缩完全相同的序列，仅使用唯一序列进行建树，以节省内存和计算时间。这解释了文件名中的`uniqueseq`。
  Due to the large number of sequences, IQ-TREE automatically identifies and compresses identical sequences, using only unique sequences for tree building to save memory and computation time. This explains the `uniqueseq` in the filenames.

## 碱基替代模型比较与选择 / Nucleotide Substitution Model Comparison and Selection

IQ-TREE的模型比较功能（如`ModelFinder`）是项目成功的关键一步。不同的模型对序列进化的假设不同，会直接影响树的拓扑结构和分支长度。
IQ-TREE's model comparison function (e.g., `ModelFinder`) is a critical step for the project's success. Different models make different assumptions about sequence evolution, which directly affects the tree topology and branch lengths.

### 常见模型及其差异 / Common Models and Their Differences:

| 模型名称<br>Model Name | 全称<br>Full Name | 可变参数<br>Parameters | 特点与假设<br>Characteristics & Assumptions | 对结果的影响<br>Impact on Results |
| :--- | :--- | :--- | :--- | :--- |
| **JC** | Jukes-Cantor | 0 | 假设所有碱基频率相等，所有类型的替代速率相等。是最简单的模型。<br>Assumes equal base frequencies and equal substitution rates. The simplest model. | 过于简单，会高估远缘序列间的距离，建树准确性通常较低，仅作为基线。<br>Oversimplified, overestimates distances between divergent sequences, usually low accuracy, used only as a baseline. |
| **K2P** | Kimura 2-Parameter | 1 | 将替代分为**转换**和**颠换**，并假设转换速率高于颠换速率。考虑了线粒体DNA的进化特点。<br>Divides substitutions into **transitions** and **transversions**, assuming a higher transition rate. Accounts for mtDNA evolution characteristics. | 比JC模型更合理，但假设所有位点速率相同，且碱基频率相等。<br>More reasonable than JC, but assumes equal rates across sites and equal base frequencies. |
| **HKY** | Hasegawa-Kishino-Yano | 4 | 在K2P的基础上，引入了**碱基频率**参数。<br>Extends K2P by incorporating **base frequency** parameters. | 更符合真实数据（如mtDNA通常富含A/T），能提供更准确的遗传距离估计。<br>Fits real data better (e.g., mtDNA is often AT-rich), provides more accurate genetic distance estimates. |
| **TN** | Tamura-Nei | 5 | 在HKY基础上进一步区分转换类型（嘌呤间和嘧啶间）和颠换类型，更精细地描述替代过程。<br>Extends HKY by further distinguishing transition types (purine-purine and pyrimidine-pyrimidine) and transversion types, providing a more refined description of the substitution process. | 比HKY更灵活，能更好地区分不同类型的转换事件，适合描述复杂进化模式。<br>More flexible than HKY, better distinguishes different types of transition events, suitable for describing complex evolutionary patterns. |
| **GTR** | General Time Reversible | 8 | 最通用的可逆模型。为6种替代类型分别设置速率，并引入碱基频率参数。<br>The most general reversible model. Sets different rates for 6 substitution types and includes base frequency parameters. | 非常灵活，能更好地拟合复杂数据。但参数多，需要更多数据来估计，计算量大。<br>Very flexible, fits complex data better. But has many parameters, requires more data for estimation, computationally intensive. |

### 模型选择的重要性 / Importance of Model Selection:

**基于30条序列子集的实际分析结果 / Based on actual analysis results of the 30-sequence subset:**
通过ModelFinder基于BIC准则选择，最适合30条人类线粒体基因组序列数据的模型是 **TN+F+I**。
According to the ModelFinder selection based on BIC criterion, the best-fit model for the 30 human mitochondrial genome sequences is **TN+F+I**.

- **TN**: Tamura-Nei替代模型，考虑了不同碱基间替换的速率差异。
  TN: Tamura-Nei substitution model, considering rate differences between different base substitutions.
- **F**: 使用 empirically observed 碱基频率（A=0.309, C=0.313, G=0.131, T=0.247）。
  F: Denotes using empirically observed base frequencies (A=0.309, C=0.313, G=0.131, T=0.247).
- **I**: 包含不变位点（Proportion of invariable sites: 0.933），反映了序列中高度保守的区域。
  I: Includes invariant sites (Proportion of invariable sites: 0.933), reflecting highly conserved regions in the sequences.

**选择该模型的原因 / Reason for selecting this model:**
数据分析显示，19956个位点中有19661个（约98.5%）是恒定不变的，仅有极少的变异位点。对于这样的高保守性数据，TN+F+I模型在模型复杂度和对数据的解释力之间取得了最佳平衡（BIC最低），避免了过度参数化。
Data analysis showed that 19,661 out of 19,956 sites (approximately 98.5%) were constant, with only very few variable sites. For such highly conserved data, the TN+F+I model achieved the best balance between model complexity and explanatory power (lowest BIC), avoiding over-parameterization.

**选择错误模型的后果 / Consequences of Choosing the Wrong Model:**
- **过于简单（如JC） / Too Simple (e.g., JC)**: 会低估近缘序列间的细微差异，高估远缘序列的距离，导致树拓扑结构错误（尤其是深层分支）和不可靠的分支长度。
  Underestimates subtle differences between closely related sequences, overestimates distances between divergent sequences, leads to incorrect tree topology (especially deep branches) and unreliable branch lengths.
- **过于复杂 / Too Complex**: 对于高保守性数据集，复杂模型（如GTR+F+I+G4）可能会导致过拟合，降低模型的泛化能力。
  For highly conserved datasets, complex models (e.g., GTR+F+I+G4) may lead to overfitting and reduce the model's generalization ability.

**结论 / Conclusion**: 使用ModelFinder等工具进行严格的模型选择，是确保系统发育推断准确性的基石。它让数据自己"告诉"我们它最适合的进化模型。对于高保守性的人类线粒体基因组数据，相对简单但适当的TN+F+I模型可能是最优选择。
Rigorous model selection using tools like ModelFinder is the cornerstone of accurate phylogenetic inference. It lets the data itself "tell" us its most suitable evolutionary model. For highly conserved human mitochondrial genome data, a relatively simple but appropriate model like TN+F+I may be the optimal choice.

## 系统树解释 / Phylogenetic Tree Interpretation

生成的系统树（见示例示意图，基于30条序列的子集）将呈现以下特征：
The generated phylogenetic tree (see example schematic based on a 30-sequence subset) will exhibit the following features:
- **根部 / Root**: 代表所有序列的最晚共同祖先（mtDNA Eve）。
  Represents the Most Recent Common Ancestor (MRCA) of all sequences (mtDNA Eve).
- **分支模式 / Branching Pattern**: 主要的分支对应于已知的mtDNA单倍群（如L, M, N, H等）。
  Major branches correspond to known mtDNA haplogroups (e.g., L, M, N, H).
- **分支长度 / Branch Lengths**: 与进化距离成正比，即累积的突变数量。分支越长，表明该谱系积累了越多突变。
  Proportional to evolutionary distance, i.e., the number of accumulated mutations. Longer branches indicate more accumulated mutations in that lineage.
- **支持率 / Support Values**: 分支节点上的数字代表自举支持率。高支持率（>95%）表明该分支非常可靠。
  Numbers on branches represent bootstrap support values. High support (>95%) indicates a highly reliable branch.

通过分析这棵包含全部序列的"宏树"，我们可以清晰地看到人类mtDNA的全球遗传多样性结构，验证已知的单倍群分类，并可能发现新的亚支系。
By analyzing this "macro-tree" containing all sequences, we can clearly see the global genetic diversity structure of human mtDNA, validate known haplogroup classifications, and potentially discover new sub-clades.

## 软件依赖 / Software Dependencies

- **MAFFT** (v7.520+): 用于多序列比对。/ For multiple sequence alignment.
- **IQ-TREE** (v3.0.1+): 用于模型选择、系统发育树构建和评估。/ For model selection, phylogenetic tree construction, and evaluation.
- **可选可视化工具 / Optional Visualization Tools:**
    - FigTree
    - iTOL (Interactive Tree Of Life)
    - Dendroscope

## 基础命令示例 / Basic Command Examples

```bash
# 1. 序列比对 / Sequence Alignment
mafft --auto --thread 40 input_sequences.fasta > all.aln.uniqueseq.aln

# 2. 使用IQ-TREE进行模型选择、自举评估并建树
iqtree3 -s all.aln.uniqueseq.aln -m MFP -B 1000 -alrt 1000 -T AUTO --prefix all.aln.uniqueseq.aln
# 参数解释 / Parameter Explanation:
# -s： 输入比对文件 / Input alignment file
# -m MFP： 执行ModelFinder找到最佳模型（MFP），并用该模型建树 / Execute ModelFinder to find the best model (MFP) and build the tree with it
# -B 1000： 执行1000次自举法重复以评估分支支持率 / Perform 1000 bootstrap replicates to assess branch support
# -alrt 1000： 执行1000次SH-aLRT检验以评估分支支持率 / Perform 1000 SH-aLRT tests to assess branch support
# -T AUTO： 使用所有可用的CPU线程 / Use all available CPU threads
# --prefix： 设置输出文件前缀 / Set output file prefix
```

---
**备注 / Note**: 由于原始数据量巨大（64,028条序列），实际计算过程可能需要在高性能计算集群上运行数小时甚至数天。文档中提供的示意图仅为基于小子集的示例，以便于快速可视化和理解。
Due to the enormous size of the raw data (64,028 sequences), the actual computation may require hours or even days to run on a high-performance computing cluster. The schematic diagram provided in the documentation is only an example based on a small subset for quick visualization and understanding.

**最后更新时间： / Last edit time**：2025-8-26
