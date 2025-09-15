由于supplementary file元数据是这种结构
```
“# Program:featureCounts v2.0.1; Command:"featureCounts" "-T" "1" "-p" "-t" "exon" "-g" "gene_id" "-a" "/Share2/home/20WLJ/RNAseq/genome/human_genome/Homo_sapiens.GRCh38.95.gtf" "-o" "/Share2/home/20WLJ/WHF-DATA/ZNF296/puro-2.id.txt" "/Share2/home/20WLJ/WHF-DATA/ZNF296/pLVX-Puro-2.bam"
 Geneid /Share2/home/20WLJ/WHF-DATA/ZNF296/pLVX-Puro-2.bam 
 ENSG00000223972 0”，
```
 
 
 我得用header=1才能生成 
  ```
           ctrl1 
 Geneid 
 ENSG00000223972 0 
 ENSG00000227232 98 
 ENSG00000278267 6
```
这样的df形式

---
```python
import pandas as pd

# 读取并处理第一个样本
df1 = pd.read_csv("C:/Users/Windows/Desktop/GSM8655184_A549_Ctrl-1.counts.txt", 
                 sep="\t", 
                 comment="#", 
                 header=0,   #header =0是根据文件元数据来定的，具体可查看《非标准
                 skiprows=1)
df1.columns = ["Geneid", "ctrl1"]
df1.set_index("Geneid", inplace=True)

# 读取并处理第二个样本
df2 = pd.read_csv("C:/Users/Windows/Desktop/GSM8655184_A549_Ctrl-2.counts.txt",  # 请替换为实际路径
                 sep="\t", 
                 comment="#", 
                 header=0,
                 skiprows=1)
df2.columns = ["Geneid", "ctrl2"]  # 注意列名改为ctrl2
df2.set_index("Geneid", inplace=True)

# 方法1：按索引合并（推荐）
merged_df = pd.concat([df1, df2], axis=1)  # axis=1表示按列合并
print("合并后的数据形状:", merged_df.shape)
print(merged_df.head())
```
---
方法二：使用merge函数（更灵活）
```python
# 重置索引，以便进行merge操作
df1_reset = df1.reset_index()
df2_reset = df2.reset_index()

# 按Geneid列合并
merged_df = pd.merge(df1_reset, df2_reset, on="Geneid", how="inner")
merged_df.set_index("Geneid", inplace=True)

print("合并后的数据形状:", merged_df.shape)
print(merged_df.head())
```

---
方法三
```python
import pandas as pd
import os

# 定义样本文件路径和对应的样本名称
sample_files = {
    "ctrl1": "C:/Users/Windows/Desktop/GSM8655184_A549_Ctrl-1.counts.txt",
    "ctrl2": "C:/Users/Windows/Desktop/GSM8655184_A549_Ctrl-2.counts.txt",
    # 可以继续添加更多样本
    # "treated1": "path/to/treated1.counts.txt",
}

# 创建一个空的数据框来存储所有样本
all_samples_df = pd.DataFrame()

for sample_name, file_path in sample_files.items():
    # 读取每个样本文件
    df = pd.read_csv(file_path, 
                    sep="\t", 
                    comment="#", 
                    header=None,
                    skiprows=1)
    df.columns = ["Geneid", sample_name]
    df.set_index("Geneid", inplace=True)
    
    # 如果是第一个样本，直接赋值；否则进行合并
    if all_samples_df.empty:
        all_samples_df = df
    else:
        all_samples_df = pd.concat([all_samples_df, df], axis=1)

print("最终合并矩阵的形状:", all_samples_df.shape)
print("样本列名:", all_samples_df.columns.tolist())
print(all_samples_df.head())
```
重要提示：
确保基因ID一致：合并前最好检查一下两个文件的基因ID是否完全一致：
```python
print("基因ID是否一致:", df1.index.equals(df2.index))
```
`on`和`how`是pd.merge()函数中两个最重要的参数，它们共同决定了合并的规则。

1. on 参数：按什么合并？
作用：指定依据哪一列（或哪几列） 来合并两个表格。

类比：就像Excel的VLOOKUP函数，你需要告诉电脑“按照学号来查找匹配成绩”。

使用场景：当两个表有相同名称的列，且你想按这列进行匹配时。

示例：

```python
# 假设有两个表：
# df1: Geneid | ctrl1
# df2: Geneid | ctrl2

merged_df = pd.merge(df1, df2, on="Geneid")
# 这表示：请按照 "Geneid" 这一列来合并df1和df2
```
如果列名不同，可以使用 left_on 和 right_on：

```python
# 假设两个表的ID列名不同：
# df1: Gene_ID | ctrl1
# df2: Geneid  | ctrl2

merged_df = pd.merge(df1, df2, left_on="Gene_ID", right_on="Geneid")
```
2. how 参数：如何合并？（四种模式）
这是最关键的部分，决定了合并的逻辑。我用您的基因表达数据来举例：

假设有两个样本：

df1 (ctrl1): 有基因A, B, C

df2 (ctrl2): 有基因B, C, D

模式一：`how='inner'`（内连接）→ 取交集
```
python
merged_df = pd.merge(df1, df2, on="Geneid", how='inner')
```
结果：只保留两个表都有的基因 → B, C
适用场景：只分析在两个样本中都检测到的基因，最严格。

模式二：`how='outer'`（外连接）→ 取并集
```python
merged_df = pd.merge(df1, df2, on="Geneid", how='outer')
```
结果：保留所有基因 → A, B, C, D（缺失值用NaN填充）
适用场景：保留所有基因信息，后续可以处理缺失值。

模式三：`how='left'`（左连接）→ 以左边为准
```python
merged_df = pd.merge(df1, df2, on="Geneid", how='left')
```
结果：保留左边表(df1)的所有基因 → A, B, C（df2没有的基因显示NaN）
适用场景：以某个样本或参考基因组为基准。

模式四：`how='right'`（右连接）→ 以右边为准
```python
merged_df = pd.merge(df1, df2, on="Geneid", how='right')
```
结果：保留右边表(df2)的所有基因 → B, C, D（df1没有的基因显示NaN）

可视化理解
```text
df1 (ctrl1)      df2 (ctrl2)
+---------+      +---------+
| Geneid  |      | Geneid  |
+---------+      +---------+
|   A     |      |   B     |
|   B     |      |   C     |
|   C     |      |   D     |
+---------+      +---------+

inner → [B, C]        # 交集
outer → [A, B, C, D]  # 并集 (NaN填充)
left  → [A, B, C]     # 以df1为准
right → [B, C, D]     # 以df2为准
```
在您代码中的应用建议
对于基因表达矩阵合并，最常用的是 how='inner'，因为：

确保比较的基因集合完全一致

避免后续分析中出现缺失值问题

```python
# 这是您场景下的最佳实践
merged_df = pd.merge(df1, df2, on="Geneid", how='inner')
```
理解on和how参数，您就掌握了数据合并的核心技能！这是生物信息分析中每天都会用到的操作。
