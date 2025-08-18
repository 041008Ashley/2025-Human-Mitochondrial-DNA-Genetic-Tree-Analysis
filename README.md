# 人类线粒体DNA多序列分析与系统发育树构建

本项目提供了一套完整的流程，用于处理从NCBI下载的人类线粒体DNA FASTA文件，进行多序列分析并构建系统发育树。该流程包括数据清洗、异常序列检测、多序列比对和进化树可视化等关键步骤。

![Workflow](https://via.placeholder.com/800x400.png?text=Analysis+Workflow)
*示例工作流程图 - 实际使用请替换为真实图表*

## 目录结构
```
mitochondrial-dna-phylogeny/
├── data/
│   ├── raw/                  # 原始FASTA文件
│   └── processed/            # 处理后的数据
├── results/
│   ├── alignments/           # 多序列比对结果
│   ├── trees/                # 系统发育树文件
│   └── figures/              # 可视化结果
├── scripts/
│   ├── 01_data_cleaning.py
│   ├── 02_sequence_analysis.py
│   ├── 03_alignment.py
│   ├── 04_phylogeny.py
│   └── 05_visualization.py
├── env.yml                   # Conda环境配置
└── README.md
```

## 工作流程

1. **数据清洗与预处理**
2. **序列分析与异常检测**
3. **多序列比对**
4. **系统发育树构建**
5. **结果可视化**

## 快速开始

### 前置要求
- Python 3.8+
- Conda (推荐用于环境管理)
- 以下软件通过Conda安装：
  - Biopython
  - MAFFT (多序列比对)
  - IQ-TREE (建树)
  - TrimAl (比对修剪)
  - Plotly/Dash (可视化)

### 安装

```bash
# 克隆仓库
git clone https://github.com/yourusername/mitochondrial-dna-phylogeny.git
cd mitochondrial-dna-phylogeny

# 创建Conda环境
conda env create -f env.yml
conda activate mtdna-analysis
```

### 运行完整流程

```bash
# 步骤1: 数据清洗
python scripts/01_data_cleaning.py -i data/raw/mtDNA_samples.fasta -o data/processed/cleaned.fasta

# 步骤2: 序列分析与异常检测
python scripts/02_sequence_analysis.py -i data/processed/cleaned.fasta -r data/reference/mtDNA_human_ref.fasta

# 步骤3: 多序列比对
python scripts/03_alignment.py -i data/processed/cleaned.fasta -o results/alignments/

# 步骤4: 建树
python scripts/04_phylogeny.py -i results/alignments/aligned.fasta -o results/trees/

# 步骤5: 可视化
python scripts/05_visualization.py -t results/trees/mtDNA_tree.nwk -o results/figures/
```

## 详细步骤说明

### 1. 数据清洗 (`01_data_cleaning.py`)
- 移除过短序列 (<16,000 bp)
- 过滤低质量序列 (含N比例 >5%)
- 标准化序列标识符
- 检查并修复序列方向（与参考序列比对）

**关键参数：**
```bash
python 01_data_cleaning.py \
  --input data/raw/input.fasta \
  --output data/processed/cleaned.fasta \
  --min_length 16000 \
  --max_n 0.05
```

### 2. 序列分析与异常检测 (`02_sequence_analysis.py`)
- GC含量分析
- 密码子使用偏倚检测
- 非同义/同义突变比率计算
- 使用PCA和聚类识别异常序列

**输出：**
- `sequence_statistics.csv` - 各样本统计指标
- `pca_plot.html` - PCA可视化
- `sequence_heatmap.html` - 序列相似性热图

### 3. 多序列比对 (`03_alignment.py`)
使用MAFFT进行比对：
```python
from Bio.Align.Applications import MafftCommandline
mafft_cline = MafftCommandline(input="input.fasta")
stdout, stderr = mafft_cline()
```

比对后修剪：
```bash
trimal -in aligned.fasta -out trimmed.fasta -gt 0.9 -cons 60
```

### 4. 系统发育树构建 (`04_phylogeny.py`)
使用IQ-TREE构建最大似然树：
```bash
iqtree -s trimmed.fasta -m MFP -bb 1000 -nt AUTO
```

参数说明：
- `-m MFP`: 自动选择最佳替代模型
- `-bb 1000`: 1000次bootstrap重采样
- `-nt AUTO`: 自动使用多线程

### 5. 可视化 (`05_visualization.py`)
使用Plotly生成交互式进化树：
```python
import plotly.graph_objects as go
from Bio.Phylo import read

tree = read("tree.nwk", "newick")
fig = go.Figure(go.Treemap(
    ids = ids,
    labels = labels,
    parents = parents))
fig.show()
```

## 示例结果

1. **多序列比对可视化**
   ![Alignment](https://via.placeholder.com/600x300.png?text=Multiple+Sequence+Alignment)

2. **系统发育树**
   ![Phylogenetic Tree](https://via.placeholder.com/600x400.png?text=Phylogenetic+Tree+with+Bootstrap+Values)

3. **群体结构分析**
   ![Population Structure](https://via.placeholder.com/600x300.png?text=PCA+and+Population+Structure)

## 常见问题解决

**Q: 序列比对时间过长怎么办？**
A: 尝试：
1. 使用MAFFT的`--auto`参数选择最快策略
2. 减少样本数量（特别是高度相似的序列）
3. 使用`--thread`参数增加线程数

**Q: Bootstrap值普遍偏低？**
A: 可能原因：
- 序列分化程度低
- 模型选择不当
- 信息位点不足
解决方案：
1. 增加bootstrap重复次数（`-bb 5000`）
2. 尝试不同替代模型
3. 检查比对质量，可能需要重新修剪

**Q: 如何解释树中的异常分支？**
A: 建议检查：
1. 原始序列质量（步骤2的输出）
2. 是否混杂了非人源序列
3. 线粒体异质性现象
4. 可能的测序错误或组装错误

## 引用工具
- MAFFT: [Katoh & Standley (2013)](https://mafft.cbrc.jp/alignment/software/)
- IQ-TREE: [Nguyen et al. (2015)](https://doi.org/10.1093/molbev/msu300)
- Biopython: [Cock et al. (2009)](https://doi.org/10.1093/bioinformatics/btp163)

## 许可证
本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件
