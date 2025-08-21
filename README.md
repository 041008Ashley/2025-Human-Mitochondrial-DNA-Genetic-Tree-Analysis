# 人类线粒体基因组系统发育与群体遗传分析

##  项目概述

本项目是一个完整的生物信息学分析流程，旨在通过对从NCBI下载的人类线粒体DNA全基因组数据进行多序列比对和系统发育重建，深入探究人类群体的母系遗传进化关系、种群结构以及线粒体基因组的变异模式。

**核心工作流:** `数据获取 (NCBI)` → `数据清洗` → `多序列比对 (MAFFT)` → `系统发育建树 (IQ-TREE)` → `可视化与解读`

##  研究目的

1.  **构建高分辨率系统发育树:** 利用最大似然法构建可靠的人类线粒体DNA系统发育树，揭示不同单倍群之间的进化关系。
2.  **评估节点可靠性:** 使用超快速Bootstrap (UFBoot) 和 SH-aLRT 检验量化系统发育树中关键分支的统计支持率。
3.  **探究群体遗传结构:** 基于系统发育拓扑结构，分析不同地理人群的遗传分化和历史动态。

##  数据来源

-   **数据库:** [NCBI Nucleotide](https://www.ncbi.nlm.nih.gov/nucleotide/)
-   **格式:** GenBank (full format)
-   **检索词:** `"Human mitochondrion"[Organism] AND complete genome AND "mtDNA"`
-   **序列数量:** >60,000 条


##  所需工具与环境

本项目依赖于以下开源软件和库。建议使用Conda进行环境管理。

### 核心工具
| 工具 | 版本 | 用途 |
| :--- | :--- | :--- |
| **Python** | ≥ 3.8 | 主编程语言，运行数据清洗脚本 |
| **Biopython** | ≥ 1.80 | 处理序列数据（GenBank, FASTA） |
| **MAFFT** | ≥ 7.5 | 高性能多序列比对 |
| **IQ-TREE** | ≥ 2.2 | 模型选择与最大似然法系统发育分析 |
| **TrimAl** | ≥ 1.4 | 修剪多序列比对结果 |

### 可视化工具
| 工具 | 类型 | 用途 |
| :--- | :--- | :--- |
| **FigTree** | 桌面软件 | 交互式查看和美化系统发育树 |
| **iTOL** | 在线平台 | 创建高级、可发表的树形图 |

### 推荐环境配置
```bash
# 1. 创建并激活Conda环境
conda create -n mtdna-analysis python=3.8
conda activate mtdna-analysis

# 2. 通过Bioconda安装所有依赖
conda install -c bioconda biopython mafft iqtree trimal
```

##  项目结构

```
mitogenome-phylogeny/
├── data/
│   ├── raw/                   # 原始数据
│   │   └── ncbi_search_results.gb
│   ├── processed/             # 处理后的数据
│   │   ├── cleaned_sequences.fasta
│   │   └── reference.fasta
│   └── alignment/             # 比对结果
│       ├── aligned_sequences.fasta
│       └── trimmed_alignment.phy
├── results/
│   ├── trees/                 # 树文件
│   │   ├── mtdna.treefile
│   │   └── mtdna.contree
│   ├── reports/               # 分析报告
│   │   └── mtdna.iqtree
│   └── figures/               # 可视化结果
│       └── phylogenetic_tree.svg
├── scripts/
│   ├── 01_fetch_and_clean.py  # 数据获取与清洗
│   ├── 02_align_sequences.py  # 多序列比对
│   └── 03_run_iqtree.py       # 系统发育分析
└── README.md
```

##  分析流程

### 步骤 1: 数据获取与清洗
```bash
# 运行数据清洗脚本 (示例)
python scripts/01_fetch_and_clean.py \
  --input data/raw/ncbi_search_results.gb \
  --output data/processed/cleaned_sequences.fasta \
  --min_length 16000 \
  --max_n 0.05
```
**功能:** 提取GenBank文件中的序列、过滤低质量序列、标准化序列标识符、进行方向校正。

### 步骤 2: 多序列比对
```bash
# 使用MAFFT进行比对
mafft --auto --thread 8 data/processed/cleaned_sequences.fasta > data/alignment/aligned_sequences.fasta

# 使用TrimAl修剪比对结果
trimal -in data/alignment/aligned_sequences.fasta -out data/alignment/trimmed_alignment.phy -gt 0.9 -cons 60
```

### 步骤 3: 系统发育分析
```bash
# 使用IQ-TREE进行完整的模型选择、建树和分支支持率评估
iqtree -s data/alignment/trimmed_alignment.phy \
  -m MFP \           # 自动进行ModelFinder模型选择
  -B 1000 -bnni \    # 1000次UFBoostrap计算
  -alrt 1000 \       # 1000次SH-aLRT检验
  -T AUTO \          # 自动选择最佳线程数
  --prefix results/trees/mtdna # 输出文件前缀
```

### 步骤 4: 结果可视化
1.  将生成的 `results/trees/mtdna.contree` 文件导入 **FigTree**。
2.  调整样式：显示节点支持率、调整分支颜色、缩放分支长度。
3.  导出高质量图片（PDF/SVG/PNG）用于发表。
4.  （可选）将树文件上传至 [iTOL](https://itol.embl.de) 进行更高级的在线注释。

##  预期结果与解读

-   **`mtdna.iqtree`**: 文本报告，包含：
    -   最佳拟合模型（如 `TIM3+F+I+G4`）及其参数。
    -   模型选择标准（BIC/AIC/AICc值）。
    -   建树日志似然值（Log-likelihood）。
-   **`mtdna.contree`**: 包含分支支持率（UFBoot/SH-aLRT）的共识树文件。
    -   **支持率解读:** `UFBoot ≥ 95%` 和 `SH-aLRT ≥ 80%` 通常认为分支具有强支持。
-   **系统发育树:** 可视化的树形图，用于阐释各单倍群（Haplogroup）间的进化关系。


##  参考文献

*   Nguyen, L.-T., et al. (2015). IQ-TREE: A fast and effective stochastic algorithm for estimating maximum-likelihood phylogenies. *Mol. Biol. Evol.*, 32(1), 268-274.
*   Katoh, K., & Standley, D. M. (2013). MAFFT multiple sequence alignment software version 7: improvements in performance and usability. *Mol. Biol. Evol.*, 30(4), 772-780.
*   Cock, P. J. A., et al. (2009). Biopython: freely available Python tools for computational molecular biology and bioinformatics. *Bioinformatics*, 25(11), 1422-1423.

---

**⭐ 如果这个项目对您有帮助，请给它点个星！**
